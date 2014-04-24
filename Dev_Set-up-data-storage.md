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