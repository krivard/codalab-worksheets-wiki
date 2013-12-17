## Install the prerequisites

* [Windows](https://github.com/codalab/codalab/wiki/Dev:-Getting-Started-on-Windows)
* [Unix-based systems](https://github.com/codalab/codalab/wiki/Dev:-Getting-Started-on-UNIX-based-Systems)

## Get the source code
1. [Fork](https://help.github.com/articles/fork-a-repo) the [CodaLab repo](https://github.com/codalab/codalab) from GitHub.

1. Clone your fork.
```
git clone https://github.com/<username>/codalab.git
```

## Configure your local environment

1. Run the dev_setup script. This will install everything you need to run CodaLab locally, including all of the dependencies within the default virtual environment.

    **Windows**
    ```
    cd codalab
    .\dev_setup.bat
    ```
    **Linux**
    ```
    cd codalab
    source ./dev_setup.sh
    ```

1. Activate the virtual environment.

    **Windows**
    ```
    venv\Scripts\activate
    ```
    
    **Linux**
    ```
    source venv/bin/activate
    ```

## Install app schema and default data
Now you are ready to install the application schema and default data into the database. The default Dev setup uses a local sqlite database, which requires very little configuration and is sufficient to get started. You can also configure CodaLab to use MySQL, which requires you to explicitly create a database.

### Create a local config file

1. Open `codalab/codalab/settings/local_sample.py`.
1. Save a copy of `local_sample.py` named `local.py` in the same directory. Naming is important, since CodaLab will recognize `local.py`.
1. Open `local.py`.
1. In the `DATABASES` section, enter the configuration settings for the database you want to use. 

**Note:** If you want to use [MySQL](http://www.mysql.com/) you'll need to manually install it and create a database before proceeding. CodaLab setup does not install MySQL.

#### sqlite3
    DATABASES = {
        'default': {
            'ENGINE':  'django.db.backends.sqlite3',
            'NAME': '..\dev_db.sqlite',
            # The following settings are not used with sqlite3:
            'USER': 'someuser',
            'PASSWORD': 'somepassword',
            'HOST': 'someserver', 
            'PORT': '',
        }

#### MySQL
    DATABASES = {
        'default': {
            'ENGINE':  'django.db.backends.mysql',
            'NAME': 'MySQL_DevDB',
            'USER': 'someuser',
            'PASSWORD': 'somepassword',
            'HOST': 'someserver', # Empty for localhost through domain sockets or '127.0.0.1' for localhost through TCP.
            'PORT': '',           # Set to empty string for default.
        }

### Migrate and initialize
Follow these steps to initialize the database.

**Windows** 
    ```
    cd codalab
    python manage.py validate
    python manage.py syncdb --migrate
    python scripts\initialize.py
    ```
    
**Linux**
    ```
    cd codalab
    python manage.py validate
    python manage.py syncdb --migrate
    python scripts/initialize.py
    ```

## Run the site

1. Run tests to verify that everything is working:

    ```
    python manage.py test
    ```

1. Populate the site with some sample data:
**Windows**
    ```        
    python scripts\users.py
    python scripts\competitions.py
    ```
**Linux**
    ```        
    python scripts/users.py
    python scripts/competitions.py
    ```

1. Sync the model and database:
**Windows**
    ```
    python codalab\manage.py syncdb --migrate
    ```
**Linux**
    ```
    python codalab/manage.py syncdb --migrate
    ```

1. Start the web server:

    ```
    python manage.py runserver
    ```

**Important:** When your next coding session comes along, remember to work in the virtual environment you created:

**Windows**
```
venv\Scripts\activate
```
    
**Linux**
```
source venv/bin/activate
```
    
Note: If you experience database errors try deleting the database file (\codalab\codalab\dev_db.*) and run syncdb again (but if you create a new database, be sure to run `initialize.py` in the `scripts` folder in order to insert initial data required by the app).

## Set up data storage

In order to test uploading and running bundles in CodaLab, you will need to have a [Windows Azure storage account](http://www.windowsazure.com/en-us/pricing/details/storage/ "Windows Azure storage account"). Once you have set up your Azure account, log on to the [Azure Portal](https://manage.windowsazure.com/) and follow the steps in this section.

### Create storage containers.

1. Log on to the [Azure Portal](https://manage.windowsazure.com/).
1. In the left pane, click **Storage**.
1. Select your storage account.
1. At the bottom of the dashboard, click **Manage Access Keys**. Copy your access keys, you'll need them later.
1. At the top of the dashboard page, click **Containers**.
1. At the bottom of the Containers page click **Add**.
1. Create a new container named "bundles". Set the **Access** to "Private".
1. Add another container named "public". Set the **Access** to "Public Blob".

### Add a service bus namespace

1. Log on to the [Azure Portal](https://manage.windowsazure.com/).
1. In the left pane, click **Service Bus**.
1. At the bottom of the dashboard, click **Create**, enter a name and select a region for the namespace, then click the check icon to create it.
1. At the bottom of the page, click **Connection Information**.
1. Copy the following connection information:
    - Namespace name
    - Default issuer
    - Default key

### Create a local configuration file

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

## Work with CodaLab in Visual Studio

You can increase your productivity by writing code, running and debugging the web site in Visual Studio using the [Python Tools for Visual Studio](https://pytools.codeplex.com/). 

**Create the codalab project:**

1. In Visual Studio File menu click **New Project**. Under **Templates**, select **Other Languages**, then choose **Python**.
1. Select the **From Existing Python Code** template.
1. Name the project "codalab".
1. **Location** should be the codalab subdirectory within the codalab project (for example C:\Users\user\Documents\GitHub\codalab\codalab).
1. Uncheck the **Create directory for solution** option.
1. Click OK. The **Create New Project from Existing Python Code** dialog appears.
1. Enter the path for the codalab subdirectory within the codalab project (for example C:\Users\user\Documents\GitHub\codalab\codalab)
1. add *.scss to the filter list.
1. Click **Next**.
1. Select **manage.py** as the file to run when F5 is pressed. If no files are showing up, then you may have used an incorrect project directory in steps 2 or 5.
1. Click **Next**.
1. Enable **Django** as a feature of your project.
1. Click Finish. The Visual Studio project file and solution files should be located in the codalab subfolder within the codalab project: codalab\codalab\codalab.pyproj & codalab\codalab\codalab.sln.

**After you have created the project:**

1. In Solution Explorer right-click the **Python Environments** node and select "Add existing virtual environment...".
1. Pick the codalab\venv directory.
1. Save your solution.
1. In Solution Explorer, right-click on the codalab project node, then choose Django > Validate Django App... The Django Management Console should appear and report zero errors found.
