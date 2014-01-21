# CodaLab deployment
This guide will show you how to deploy a CodaLab instance to Azure. The CodaLab deployment tools use [Fabric](http://www.fabfile.org), a Python library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks.

## Install Prerequisites
- [Windows Azure](http://www.windowsazure.com/) account
- [Visual C++ 2008 redistributable package](http://www.microsoft.com/en-us/download/details.aspx?id=29)
- Cygwin
- Remote Git repository with cloned fork of CodaLab repo. Follow these instructions: [Dev: Configure CodaLab for Development](https://github.com/codalab/codalab/wiki/Dev:-Configure-Codalab-For-Development).

### Install and configure Cygwin
1. Download the appropriate 32-bit installer from [Cygwin.com](http://cygwin.com/install.html).
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

1. Launch Cygwin.
1. Navigate to the deploy directory of your CodaLab repo:
    `$ cd "C:\Users\[USER]\Documents\GitHub\codalab\codalab\codalabtools\deploy"`

1. To get a list of commands:l
    `$ fab -l`

If Fabric returns a list of commands, you're good to go. If not, you'll need to revisit the previous steps to determine what is missing.

## Set up Azure certificates and SSL keys
In this section you will create a set of Azure certificates and SSL keys. These certificates and keys provide the necessary authentication for CodaLab to interact with Azure.

There are two sets of certificates. 
- The self-signed management certificate used to authenticate the connection to Azure so that you can send commands to the Azure VMs.
- The .key and .pfx files which are used by the Azure VMs.

For general information about Azure certificates, see [Manage Certificates](http://msdn.microsoft.com/en-us/library/windowsazure/gg981929.aspx).

For help with the **makecert** utility, see [Makecert.exe (Certificate Creation Tool)](http://msdn.microsoft.com/en-us/library/bfsktky3.aspx).

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

For more information, see [Create and Upload a Management Certificate for Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx).

### Create the Azure VM keys
1. On the taskbar, click **Start**, click **All Programs**, click **Microsoft Visual Studio**, then click **Visual Studio Tools**.
1. Right-click **Developer Command Prompt** and select **Run as administrator**.
1. Run the following command to create a new keypair:
    `makecert -r -sv azureuser_WF.pvk -n "cn=azureuser_WF" azureuser_WF.cer -e 12/31/2014`
1. Enter passwords for the certificate when prompted. Don't forget to make a note of the passwords you entered!

1. Run the following command to convert the .key to .pfx.
    `pvk2pfx -pvk azureuser.pvk -spc azureuser.cer -pfx azureuser.pfx -po password`
    Where `password` is the password you entered when creating the .pvk.

## Configure CodaLab
To set up the CodaLab configuration file, you'll need to have all of your Windows Azure account information on hand. In this section, we will:
- Set up an email delivery service account.
- Generate a Django secret key.
- Create a .codalabconfig file.

### Set up an email delivery service account
In order to receive notifications from your CodaLab deployment, you will need to have a transactional email delivery service account. You can do this easily using [SendGrid](http://sendgrid.com/) or a similar service. You'll need to enter the host IP address, user name and password into the `.codalabconfig` file.

### Get your Django secret key
Each Django installation has a secret key which is used to provide cryptographic signing. The `SECRET_KEY` value can be found in the `codalab\codalab\settings\base.py` file for the CodaLab project. For more information, see the Django Help [Settings](https://docs.djangoproject.com/en/dev/ref/settings/#secret-key) topic.

### Create a .codalabconfig file
1. Copy the .key, .pfx, and .cer files from `C:\Windows\SysWOW64` into your `C:\cygwin64\home\<user>\.ssh` directory.

1. From Cygwin, in your home directory create a file named `.codalabconfig`.
    `$ touch .codalabconfig`

1. Paste the following configuration file template into `.codalabconfig` and save the file. You will need to replace values with your own unique values wherever `<value>` is indicated, and replace `[USER]` with your user name.

```
deployment:
    azure-management:
        subscription-id: '<value>' # This is your Azure subscription ID.
        certificate-path: 'CURRENT_USER\\my\\<name_of_certificate>'
        operation-timeout: 1800
    service-global:
        prefix: 'cxp'
        location: 'West US'
        certificate:
            algorithm: 'sha1'
            thumbprint: '<value>' 
            format: 'pfx'
            filename: '/home/[USER]/.ssh/azureuser.pfx'
            key-filename: '/home/[USER]/.ssh/azureuser.key'
            password: '<value>'
        vm:
            username: 'azureuser'
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
                configuration: 'CxpDev'
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
            filename: '/home/[USER]/deployment-log.txt'
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
    `codalab\codalab\codalabtools\deploy`

1. Create the Azure virtual machines that will host your deployment.
    `fab config:dev provision:all`

1. Deploy to Azure for the first time.
    `fab config:dev build push_build deploy_web supervisor nginx_restart`

