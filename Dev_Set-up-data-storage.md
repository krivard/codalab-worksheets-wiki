In order to test uploading and running bundles in CodaLab, you will need to have a [Windows Azure storage account](http://www.windowsazure.com/en-us/pricing/details/storage/ "Windows Azure storage account"). Once you have set up your Azure account, log on to the [Azure Portal](https://manage.windowsazure.com/) and follow the steps in this section.

## Create storage containers.

1. Log on to the [Azure Portal](https://manage.windowsazure.com/).
1. In the left pane, click **Storage**.
1. Select your storage account.
1. At the bottom of the dashboard, click **Manage Access Keys**. Copy your access keys, you'll need them later.
1. At the top of the dashboard page, click **Containers**.
1. At the bottom of the Containers page click **Add**.
1. Create a new container named "bundles". Set the **Access** to "Private".
1. Add another container named "public". Set the **Access** to "Public Blob".

## Add a service bus namespace

1. Log on to the [Azure Portal](https://manage.windowsazure.com/).
1. In the left pane, click **Service Bus**.
1. At the bottom of the dashboard, click **Create**, enter a name and select a region for the namespace, then click the check icon to create it.
1. In the left pane, click **Service Bus**.
1. Select the service bus you just created.
1. At the top of the screen click **Queues**.
1. Click **Create a new queue**.
1. Click **Quick Create** and create a new queue named "compute".
1. Click **Create A New Queue**.
1. At the bottom of the screen, click **New**, and create another queue named "response".
1. In the left pane, click **Service Bus**.
1. At the bottom of the page, click **Connection Information**.
1. Copy the following connection information:
    - Namespace name
    - Default issuer
    - Default key

## Create a local configuration file

1. Open your local configuration file (`local.py`). If there is no `local.py`, save a copy of `local_sample.py` named `local.py` in the same directory.
1. In the `Azure storage` section, enter your Azure account details:
    ```
    DEFAULT_FILE_STORAGE = 'codalab.azure_storage.AzureStorage'
    AZURE_ACCOUNT_NAME = "<enter name>"
    AZURE_ACCOUNT_KEY = '<enter key>'
    AZURE_CONTAINER = '<enter container name>'
    
    PRIVATE_FILE_STORAGE = 'codalab.azure_storage.AzureStorage'
    PRIVATE_AZURE_ACCOUNT_NAME = "<enter name>"
    PRIVATE_AZURE_ACCOUNT_KEY = "<enter key>"
    PRIVATE_AZURE_CONTAINER = "<enter container name>"
    
    BUNDLE_AZURE_CONTAINER = "<enter the name of your bundle container>"
    BUNDLE_AZURE_ACCOUNT_NAME = PRIVATE_AZURE_ACCOUNT_NAME
    BUNDLE_AZURE_ACCOUNT_KEY = PRIVATE_AZURE_ACCOUNT_KEY
    ```

1. In the `Service Bus` section, enter your service bus connection information:
    ```
    SBS_NAMESPACE = '<enter the name of your service bus>'
    SBS_ISSUER = 'owner'
    SBS_ACCOUNT_KEY = '<enter value for 'default key'>'
    ```

**Important:** Do not change the values for `DEFAULT_FILE_STORAGE` and `PRIVATE_FILE_STORAGE`, as these parameters contain the name of the Python class which implements the Azure storage back-end for Django.

## Create a local compute configuration file

1. Open `codalab/codalab/codalabtools/compute/sample.config`.
1. Save a copy of `sample.config` named `.codalabconfig` in the same directory.
1. Open `.codalabconfig`.
1. In the `compute-worker` section, enter the configuration settings for the storage account and the compute queue.

    ```
    compute-worker:
        azure-storage:
            account-name: "your account name"
            account-key: "your account key"
        azure-service-bus:
            namespace: "your namespace"
            key: "your secret key"
            issuer: "owner"
            listen-to: "name of queue"
        local-root: "D:\\Temp"
    ```

## Enable CORS (Cross-origin resource sharing) on Blob Storage
In order to work with competitions and bundles in your local development environment, you will need to manually enable Cross-origin resource sharing (CORS). CORS is a mechanism that allows many resources on a web page to be requested from another domain outside the domain the resource originated from. Web Browsers commonly apply same origin restriction policy to network requests. CORS relaxes these restrictions allowing domains to give each other permissions for accessing each other's resources. CORS is supported for Blob, Table and Queue services and can be enabled for each service using [Azure SDK for python](http://azure.microsoft.com/en-us/documentation/articles/python-how-to-install/).

1. Make sure you have a valid management certificate to connect to the Service Management endpoint. This tutorial explains how to create a certificate and upload it to the Azure management portal: [http://azure.microsoft.com/en-us/documentation/articles/cloud-services-python-how-to-use-service-management/](http://azure.microsoft.com/en-us/documentation/articles/cloud-services-python-how-to-use-service-management/). 

1. Open `codalab\scripts\cors-enable.py` in a text editor, and update `account_name` and `account_key` with the account name and key for your blob storage account:

    ```
    import sys
    import yaml
    from os.path import dirname, abspath
    # Add codalabtools to the module search path
    sys.path.append(dirname(dirname(abspath(__file__))))
    
    from codalabtools.azure_extensions import (Cors,CorsRule,set_storage_service_cors_properties)
    
    account_name = "<your blob storage account name>"
    account_key = "<your blob storage account key>"
    cors_rule = CorsRule()
    cors_rule.allowed_origins = '*' # this is fine for dev setup
    cors_rule.allowed_methods = 'PUT'
    cors_rule.exposed_headers = '*'
    cors_rule.allowed_headers = '*'
    cors_rule.max_age_in_seconds = 1800
    cors_rules = Cors()
    cors_rules.cors_rule.append(cors_rule)
    set_storage_service_cors_properties(account_name, account_key, cors_rules)
    ```

1. Save your changes, activate your virtual environment and run the script:
    
    **Windows**
    ```
    python scripts\cors-enable.py
    ```

    **Linux**
    ```
    python scripts/cors-enable.py
    ```