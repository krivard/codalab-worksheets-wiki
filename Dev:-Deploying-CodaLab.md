# CodaLab deployment
This guide will show you how to deploy a CodaLab instance to Azure. The CodaLab deployment tools use [Fabric](http://www.fabfile.org), a Python library and command-line tool for streamlining the use of SSH for application deployment or systems administration tasks.

## Install Prerequisites
- [Windows Azure](http://www.windowsazure.com/) account
- [Visual C++ 2008 redistributable package](http://www.microsoft.com/en-us/download/details.aspx?id=29)
- [Cygwin](http://cygwin.com/)
- Remote Git repository with cloned fork of CodaLab repo. Follow these instructions: [Dev: Configure CodaLab for Development](https://github.com/codalab/codalab/wiki/Dev:-Configure-Codalab-For-Development).

### Install and configure Cygwin
1. Download the appropriate installer from [Cygwin.com](http://cygwin.com/install.html).
2. Install the following packages (listed here by category):

- **Python**
    - python
    - python-setuptools
    - python-openssl
- **Net**
    - wget
    - openssl
    - openssh
- **Devel**
    - git
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

1. Generate an Azure key. See [How to Use SSH with Linux on Windows Azure](http://www.windowsazure.com/en-us/manage/linux/how-to-guides/ssh-into-linux/) for detailed instructions.

1.  In your home directory create a folder named `.ssh`
    `$ mkdir .ssh`

1. Copy the Azure key into the newly created `~/.ssh` directory.
    `cp azureuser.key ~/.ssh`

#### Verify your Fabric installation
Now let's take a moment to ensure that Fabric has been properly configured.

1. Launch Cygwin.
1. Navigate to your CodaLab repo:
    `$ cd "C:\Users\[USER]\Documents\GitHub\codalab\codalab\codalabtools\deploy"`

1. To get a list of commands:
    `$ fab -l`

If Fabric returns a list of commands, you're good to go. If not, you'll need to revisit the previous steps to determine what is missing.

## Set up certificates
In this section you will create a set of Azure certificates and SSL keys. These certificates and keys provide the necessary authentication for CodaLab to interact with Azure.

For general information about Azure certificates, see [Manage Certificates](http://msdn.microsoft.com/en-us/library/windowsazure/gg981929.aspx).

### Create and upload a self-signed management certificate
1. In a Windows console, run the following command to create a new self-signed management certificate:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

The command will create the .cer file, and install it in the Personal certificate store.

1. Upload the .cer file to Windows Azure via the **Upload** action of the **Settings** tab of the [management portal](https://manage.windowsazure.com/).

For more information, see [Create and Upload a Management Certificate for Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg551722.aspx).

### Export a private key (.pfx)
1. Open the Certificate Manager snap-in for the management console by typing **certmgr.msc** in the **Start** menu textbox.

1. If you used the procedure that includes using the makecert program to create a certificate, the new certificate was automatically added to the personal certificate store. If your certificate is not listed under Personal Certificates, import your X.509 certificate.

1. Export the certificate by right-clicking the `AzureCertificate` certificate in the right pane, pointing to **All Tasks**, and then clicking **Export**.

1. On the **Export Private Key** page, ensure that you select **Yes, export the private key**.

1. Finish the wizard.

For more information, see [Create a Service Certificate for Windows Azure](http://msdn.microsoft.com/en-us/library/windowsazure/gg432987.aspx)

### Export an RSA keypair
1. Go to: [How to Use SSH with Linux on Windows Azure](http://www.windowsazure.com/en-us/documentation/articles/linux-use-ssh-key/). CodaLab installs OpenSSL by default so you will not need to repeat that step.
1. Follow the instructions to use openssl to generate an X509 certificate with a 2048-bit RSA keypair. Note that this topic features instructions for both Windows and Linux platforms.

!!!Notes!!!
http://www.openssl.org/docs/HOWTO/keys.txt
1. Use the OpenSSL utility to export an RSA key (.pem).?

    openssl genrsa -des3 -out privkey.pem 2048

!!! Or do we use the .pfx created in the previous step?
    pkcs12 -in client_ssl.pfx -out client_ssl.pem -clcerts
!!! Do we need -cacerts or -clcerts option?
!!! http://www.openssl.org/docs/apps/pkcs12.html#
http://www.windowsazure.com/en-us/documentation/articles/linux-use-ssh-key/
http://stackoverflow.com/questions/15413646/converting-pfx-to-pem-using-openssl
http://www.windowsazure.com/en-us/documentation/articles/cloud-services-python-how-to-use-service-management/
[guide users through creating .cer, .pem, .pfx, .key. They will need exact steps of what to do, as this is not clear and just attempting to browse the Azure docs can be confusing. Link to appropriate Azure help topics.]

- Windows or Linux
- Outercurve CodaLab or private CodaLab deployment

First cert is the management certificate (2 flavors: Windows, Linux)
The other certs are for automated authentication

•	A developer working with the project’s Azure subscription (both internal and Outercurve sponsored account) would use existing certificates. The certificates files are on DropBox (Credentials\Keys\OuterCurveAzureUser) and the certificates that need to be uploaded to Azure have been uploaded.
!!!Notes!!!


## Configure Fabric
To set up the Fabric configuration file, you'll need to have all of your Windows Azure account information on hand.

1. Download the following files from your Windows Azure account:
    - azureuser.key
    - azureuser.pfx
    - codalabazuremgmtnix.cer

1. Copy the files into your `/home/[USER]/.ssh/` directory.

1. From Cygwin, in your home directory create a file named `.codalabconfig`.
    `$ touch .codalabconfig`

1. Paste the following configuration file template into `.codalabconfig` and save the file. You will need to replace values with your own unique values wherever `<value>` is indicated, and replace `[USER]` with your user name.

```
deployment:
    azure-management:
        subscription-id: '<value>'
        certificate-path: '/home/[USER]/.ssh/codalabazuremgmtnix.pem'
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
            host: 'smtp.sendgrid.net'
            user: 'hendapui'
            password: '<value>@azure.com'
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
                user: [USER]
                repo: codalab
                tag: dev
            django:
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
1. Create the Azure virtual machines that will host your deployment.
    `fab config:dev provision:all`

1. Deploy to Azure for the first time.
    `fab config:dev build push_build deploy_web supervisor nginx_restart`

