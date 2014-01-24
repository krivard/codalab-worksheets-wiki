This guide will show you how to deploy a CodaLab instance to Azure. The CodaLab deployment tools use [Fabric](http://www.fabfile.org), a Python library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks.

**Note:** If you are a project committer, there is a set of credentials, predefined certificates, and a `.codalabconfig` template for you to use. Contact the project coordinator to get access to these items.

## Install Prerequisites
- [Windows Azure](http://www.windowsazure.com/) account
- [Visual C++ 2008 redistributable package](http://www.microsoft.com/en-us/download/details.aspx?id=29)
- Cygwin (required to run Fabric on Windows)
- Remote Git repository with cloned fork of the CodaLab repo. Follow these instructions: [Dev: Configure CodaLab for Development](https://github.com/codalab/codalab/wiki/Dev:-Configure-Codalab-For-Development).

### Install and configure Cygwin
1. Download the appropriate installer from [Cygwin.com](http://cygwin.com/install.html).
2. Install the following packages (listed here by category):

- **Python**
    - python (Python language interpreter)
    - python-setuptools
    - python-openssl
- **Net**
    - wget
    - openssl
    - openssh
- **Devel**
    - git (Distributed version control system)
    - gcc-core

**Notes:**
- Be sure to select only the packages you require, as installs can quickly grow to hundreds of megabytes.
- If you are prompted to install dependencies, click **OK** to install them (default option).

### Install and configure Fabric
1. Launch Cygwin.
1. Install the following packages (in this order):
    `$ easy_install pip`
    `$ pip install fabric`
    `$ pip install azure pyyaml`
    `$ pip install ecdsa`

1. Install [PyCrypto](https://www.dlitz.net/software/pycrypto/) using the appropriate [prebuilt binary](http://www.voidspace.org.uk/python/modules.shtml#pycrypto).

#### Verify your Fabric installation
Now let's take a moment to ensure that Fabric has been properly configured.

1. Launch PowerShell as an administrator.
1. Navigate to the deploy directory of your CodaLab repo:
    `cd "C:\Users\[USER]\Documents\GitHub\codalab\codalab\codalabtools\deploy"`

1. Test Fabric by getting a list of commands:
    `fab -l`

If Fabric returns a list of commands, you're good to go. If not, you'll need to revisit the previous steps to determine what is missing.

## Set up Azure certificates and SSL keys
In this section you will create a set of Azure certificates and SSL keys. These certificates and keys provide the necessary authentication for CodaLab to interact with Azure.

**Note:** If you are a project committer, there is a set of predefined certificates for you to use, so you can skip these steps. Contact the project coordinator to get access to the certificates and credentials you will need.

There are two sets of certificates. 
- The self-signed management certificate used to authenticate the connection to Azure so that you can send commands to the Azure VMs.
- The .key and .pfx files which are used by the Azure VMs.

For general information about Azure certificates, see [Manage Certificates](http://msdn.microsoft.com/en-us/library/windowsazure/gg981929.aspx).

For help with **Makecert**, see [Makecert.exe (Certificate Creation Tool)](http://msdn.microsoft.com/en-us/library/bfsktky3.aspx).

### Create and upload a self-signed management certificate
1. On the taskbar, click **Start**, click **All Programs**, click **Microsoft Visual Studio**, then click **Visual Studio Tools**.
1. Right-click **Developer Command Prompt** and select **Run as administrator**.
1. Run the following command to create a new self-signed management certificate:
    `makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\<user>\Desktop\AzureCertificate.cer"`
    The command will create the .cer file at the specified path, and install it in your Personal certificate store. 

1. Go to the Azure [management portal](https://manage.windowsazure.com/).
1. Click the **Settings** tab, then click **Management Certificates**.
1. Click **Upload**.
1. Browse to the .cer file you just created and upload it.
1. Copy the .cer file to ``.

For more information, see [Create and Upload a Management Certificate for Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx).

### Create the Azure VM keys
1. On the taskbar, click **Start**, click **All Programs**, click **Microsoft Visual Studio**, then click **Visual Studio Tools**.
1. Right-click **Developer Command Prompt** and select **Run as administrator**.
1. Run the following command to create a new keypair:
    `makecert -r -sv azureuser.pvk -n "cn=azureuser" azureuser.key -e 12/31/2014`

1. Enter passwords for the certificate when prompted. Don't forget to make a note of the passwords you entered!

1. Run the following command to convert the .key to .pfx.
    `pvk2pfx -pvk azureuser.pvk -spc azureuser.key -pfx azureuser.pfx -po password`
    Where `password` is the password you entered when creating the keypair.
    
### Import certificates to your local store
1. Copy the .key, .pfx, and .cer files from `C:\Windows\SysWOW64` into your `C:\cygwin64\home\<user>\.ssh` directory.
1. Click **Start**, then type `certmgr.msc`.
1. Expand **Personal** then expand **Certificates**.
1. Click **Action**, **All Tasks**, **Import**.
1. Click **Next**.
1. Click **Browse** and navigate to the management certificate (.cer) you created earlier.
1. Click **Next**.
1. Ensure that **Place all certificates in the following store** is checked, and **Certificate store** is set to **Personal**.
1. Click **Next**, then click **Finish**.
1. Repeat the steps to import the .pfx you created.

## Configure CodaLab
To set up the CodaLab configuration file, you'll need to have all of your Windows Azure account information on hand. In this section, we will:
- Set up an email delivery service account.
- Generate a Django secret key.
- Create a .codalabconfig file.

### Set up an email delivery service account
In order to receive notifications from your CodaLab deployment, you will need to have a transactional email delivery service account. You can do this easily using [SendGrid](http://sendgrid.com/) or a similar service. You'll need to enter the host IP address, user name and password into the `.codalabconfig` file.

**Note:** If you are a project committer, there is a Sendgrid account that you can use, so you can skip this step. Contact the project coordinator to get access to the `.codalabconfig` template with the account credentials.

### Get your Django secret key
Each Django installation has a secret key which is used to provide cryptographic signing. The `SECRET_KEY` value can be found in the `codalab\codalab\settings\base.py` file for the CodaLab project. For more information, see the Django Help [Settings](https://docs.djangoproject.com/en/dev/ref/settings/#secret-key) topic.

### Create a .codalabconfig file
1. Launch PowerShell as an administrator.
1. In your home directory create a file named `.codalabconfig`.
    `new-item -path "C:\Users\[USER]\Documents\GitHub\codalab\codalab\codalabtools\deploy" -name .codalabconfig -type "file"`

1. Paste the following configuration file template into `.codalabconfig` and save the file. You will need to replace values with your own unique values wherever `<value>` is indicated, and replace `[USER]` with your user name.

**Note:** If you are a project committer, there is a special `.codalabconfig` template that you should use. Contact the project coordinator to get access to this template.

```
deployment:
    azure-management:
        subscription-id: '<value>' # Your Azure subscription ID.
        certificate-path: "CURRENT_USER\\my\\<name_of_certificate>"
        operation-timeout: 1800
    service-global:
        prefix: '<value>' # A unique prefix that is used when creating items in Azure.
        location: '<value>' # Region setting for your service bus.
        certificate:
            algorithm: 'sha1'
            thumbprint: '<value>' 
            format: 'pfx'
            filename: 'C:\\cygwin64\\home\\azureuser.pfx'
            key-filename: 'C:\\cygwin64\\home\\azureuser.key'
            password: '<value>'
        vm:
            username: '[USER]'
            password: '<value>'
        e-mail:
            # Your email service account credentials.
            host: '<value>'
            user: '<user>'
            password: '<value>'
    build-configuration:
        os-image: 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20131022-en-us-30GB'
        role-size: 'Small'
    service-configurations:
        dev:
            vm:
                os-image: 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_04-amd64-server-20131022-en-us-30GB'
                count: 2
                role-size: 'Medium'
                ssh-port: 57190
            git:
                # Your Git credentials.
                user: [USER]
                repo: codalab
                tag: dev
            django:
                # Your Django secret key.
                configuration: 'Dev'
                secret-key: '<value>'
            database:
                engine: 'django.db.backends.mysql'
                name : 'mysql_dev'
                user : 'b46b0ea1846080'
                password : '<value>'
                host : 'us-cdbr-azure-west-b.cleardb.com'
                port : '3306'
            storage:
                public-container: 'pub'
                bundles-container: 'bundles'
            bus:
                namespace: '' # leave empty for default '<Prefix><Label>Bus' (e.g. CodalabProdBus)
logging:
    version: 1
    formatters:
        simple:
            format: '%(asctime)s %(levelname)s %(message)s'
    handlers:
        console:
            class: logging.StreamHandler
            level: DEBUG
            formatter: simple
            stream: ext://sys.stdout
        file:
            class: logging.FileHandler
            level: DEBUG
            formatter: simple
            filename: 'C:\cygwin64\home\[USER]\deployment-log.txt'
            mode: w
    loggers:
        codalabtools:
            level: DEBUG
            handlers: [console, file]
            propagate: no
    root:
        level: WARNING
        handlers: [console]
```

## Create Azure VMs and deploy to Azure
1. Navigate to the deploy directory.
    `cd "C:\Users\[USER]\Documents\GitHub\codalab\codalab\codalabtools\deploy"`

1. Create the Azure virtual machines that will host your deployment.
    `fab config:dev provision:all`

1. Deploy to Azure for the first time.
    `fab config:dev build push_build deploy_web supervisor nginx_restart`

You can use the **teardown** command to remove a deployment (handy to reset things if you experience issues):
    `fab config:dev teardown:all`

