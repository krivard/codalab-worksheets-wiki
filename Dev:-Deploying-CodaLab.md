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
    `new-item -path "C:\\users\\[USER]" -name .codalabconfig -type "file"`
    Be sure to replace "[USER]" with the name of your actual user directory.

1. Paste the following configuration file template into `.codalabconfig` and save the file. You will need to replace values with your own unique values wherever `<value>` is indicated, and replace `[USER]` with the appropriate user name.

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
        os-image: 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_10-amd64-server-20131015-en-us-30GB'
        role-size: 'Small'
    service-configurations:
        dev:
            vm:
                os-image: 'b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_10-amd64-server-20131015-en-us-30GB'
                count: 2
                role-size: 'Medium'
                ssh-port: 57190
            git:
                # Your Git credentials.
                user: [USER] # Your Github username.
                repo: codalab # The name of your cloned CodaLab repo.
                tag: master # The name of the remote branch to clone. 
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

For subsequent deployments, use the following:
    `fab config:dev supervisor_stop build push_build deploy_web supervisor nginx_restart`

You can use the **teardown** command to remove a deployment. This is a handy way to reset things if you experience issues, but use caution since teardown cannot be undone:
    `fab config:dev teardown:all`

## Test your deployment
Once all of your virtual machines are up and running, you can use SSH to connect and verify that things are working properly. You will need the following tools:

- PuTTY
- PuTTYgen

You can get them from here: [PuTTY download page](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

For more information about using SSH to connect to Azure Linux VMs, see [How to Use SSH with Linux on Windows Azure](http://www.windowsazure.com/en-us/documentation/articles/linux-use-ssh-key/).

### Use SSH to connect to your VMs
1. Launch PuTTYgen.
1. Under **Actions**, click **Load**.
1. Navigate to the location of the .key that is specified in your .codalabconfig file.
1. Be sure **All Files(*.*)** is selected, select your .key file and click **Open**.
1. Enter a **Key passphrase**.
1. Click **Save private key** and save the .ppk file.
1. Return to PuTTY.
1. Under **Host Name (or IP address)** enter the VM host name.
1. Under **Port** enter the port for the VM (the build machine will be port 22, the web instances will be a longer number).
1. Under **Saved Sessions**, enter the host name, then click **Save**.
1. In the left pane expand **Connection**, **SSH**, **Auth**.
1. Under **Private key file for authentication** click **Browse** and navigate to the .ppk file you created.
1. Click **Open** to select the file.
1. In the left pane click **Session**.
1. Click **Save** to ensure that the .ppk is saved with the session.
1. Click **Open** to start the SSH session.
1. When prompted, enter your username and the passphrase for the .ppk certificate. You should now be successfully logged on using SSH.

### Use SSH to connect to a web instance
1. From Putty, connect to your build VM (use the previous instructions).
1. If this is your first time connecting, you'll need to generate keys.
    `openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myPrivateKey.key -out myCert.pem`
1. Use SSH to connect to your web instance:
    `$ ssh [USER]@[MACHINE_NAME].cloudapp.net –p [PORT_NUMBER]`
    You can get the connection information from the VM dashboard on your [Azure Management Portal](http://manage.windowsazure.com). 

## Tips and tricks
Due to the complexity of the deployment process, things might not always go in accordance with plans. This section outlines some useful tips and scripts that you can use to troubleshoot your deployment.

### Check your certificate
If you are receiving errors during the initial parts of the provisioning process, it could be due to an issue with your certificate. Use this Python script to verify whether your certificate is valid (if the script fails, your certificate is most likely bad).

    ```
    import sys
    import os, os.path
    from azure import *
    from azure.servicemanagement import *
    
    SUBSCRIPTION_ID = "[AZURE_SUBSCRIPTION_ID]"
    
    sms = ServiceManagementService(SUBSCRIPTION_ID, "CURRENT_USER\\my\\[AZURE_CERT_NAME]")
    print sms.list_locations()
    results = sms.list_locations()
    for location in results:
        print location.name
    ```

### List all local certificates
You can list all of your local certificates by running a few commands in PowerShell. This is a good way to get certificate thumbprints.

    ```
    PS C:\dev> cd cert:
    PS Cert:\> ls
     
     
    Location   : CurrentUser
    StoreNames : {TrustedPublisher, ClientAuthIssuer, Root, UserDS...}
     
    Location   : LocalMachine
    StoreNames : {TrustedPublisher, ClientAuthIssuer, Remote Desktop, Root...}
     
     
     
    PS Cert:\> cd .\\CurrentUser
    PS Cert:\CurrentUser> cd .\My
    PS Cert:\CurrentUser\My> ls
     
     
        Directory: Microsoft.PowerShell.Security\Certificate::CurrentUser\My
     
     
    Thumbprint                                Subject
    ----------                                -------
    ADD5BA24A11C2935C0000040C35199BC964E63E7  CN=####@microsoft.com
    00000B2042C77E2A35656822C5CDFFACA30A8E72  CN=#####DC...
    9B000007A54112C3FD15E4AE21CCA97EFB69B786  CN=#####
    963A1A1214F0700000A361A2A8D7F0000DFD22B0  CN=#####
    ```


### List available OS images
From time to time the OS images available on Azure will change. If, while in the provisioning stage, you receive an error stating that an OS image cannot be located, you may need to check for a new one. This Python script outputs a list of all Ubuntu OS images.

    ```
    from azure import *
    from azure.servicemanagement import *
    
    subscription_id = "[AZURE_SUBSCRIPTION_ID]"
    certificate_path = "CURRENT_USER\\my\\[AZURE_CERT_NAME]"
    
    sms = ServiceManagementService(subscription_id, certificate_path)
    
    result = sms.list_os_images()
    
    for os in result:
        if "Ubuntu" in os.name:
            print("Name: " + os.name)
    ```

The OS image names look like this:
    `b39f27a8b8c64d52b05eac6a62ebad85__Ubuntu-13_10-amd64-server-20131015-en-us-30GB`

If the image name from your **.codalabconfig** is not in the list, you will need to select a different one.

- Be sure to select an image for Ubuntu Server 13.x with 30GB.
- Don't forget to paste the new image name into the `os-image` entries in **.codalabconfig**.