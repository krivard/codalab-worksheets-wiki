## Contents
- [Install the prerequisites](#install-the-prerequisites)
- [Get the source code](#get-the-source-code)
- [Configure your local environment](#configure-your-local-environment)
- [Install app schema and default data](#install-app-schema-and-default-data)
- [Configure the Bundle Service to run Locally](#Configure-the-Bundle-Service-to-run-Locally)
- [Start the web server](#start-the-web-server)
- [Start the worker role](#start-the-worker-roles)

## Install the prerequisites

* [Windows](Dev_Getting-Started-on-Windows)
* [Unix](Dev_Getting-Started-on-UNIX-based-Systems)
* [Mac](Dev_Getting-Started-on-Mac)

## Get the source code
1. [Fork](https://help.github.com/articles/fork-a-repo) the [CodaLab repo](https://github.com/codalab/codalab) from GitHub.

1. Clone your fork.
```
git clone https://github.com/<username>/codalab.git
```

For more details and recommended practices, see [Developer Guidelines](Dev_Developer-Guidelines).

## Configure your local environment
In this segment, you will run the dev_setup script. This will install all dependencies and create a new virtual environment (venv) for CodaLab.

1. Run the dev_setup script. 

    **Windows**
    ```
    cd codalab
    .\dev_setup.bat
    ```
    If you are going to use SQL Server as a database, you will need to install the [Python PyODBC library](http://code.google.com/p/pyodbc/). Before running `dev_setup`, you can download the installer, then run the setup script as follows (assuming the installer was downloaded at the root of a D drive):
    ```
    cd codalab
    dev_setup.bat D:\pyodbc-3.0.7.win-amd64-py2.7.exe
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
Now you are ready to install the application schema and default data into the database. 

### Set up databases
You can configure CodaLab to use either SQL Server or MySQL. Both of these require you to explicitly create a database.

#### Install MySQL Python
Follow these steps to install MySQL Python.

**Windows**

1. Open a Windows command prompt.
2. Navigate to the virtual environment (venv) for CodaLab and use the following command to install MySql-Python.

    `easy_install mysql-python`

3. Launch the MySQL Command Line Client.
4. Use the following command to create a new database:

   `create database if not exists MySQL_DevDB;`

**Linux**

1. Open a terminal window.
2. Run the following command to install MySQL:

    `sudo apt-get install mysql-server`

3. Login to MySQL as root by typing the following command:

    `mysql -u root -p`
    Enter your root password when prompted.

4. Use the following command to create a new database:

    `create database if not exists MySQL_DevDB;`

5. Type `exit` to return to the terminal prompt.

**Mac**

1. Open a terminal window.

2. Login to MySQL as root by typing the following command:

    `mysql -u root -p`
    Enter your root password when prompted.

3. Use the following command to create a new database:

    `create database if not exists MySQL_DevDB;`

4. Type `exit` to return to the terminal prompt.

5. Finish up installation for Django

    `export DYLD_LIBRARY_PATH=/usr/local/mysql/lib:$DYLD_LIBRARY_PATH`

### Create a local config file
The local configuration file `local.py` tells CodaLab which type of database you are using, and stores credentials for the database.

1. Follow these instructions to set up Azure bundle storage: [Set Up Data Storage](https://github.com/codalab/codalab/wiki/Dev_Set-up-data-storage).
1. Open `codalab/codalab/settings/local_sample.py`.
1. Save a copy of `local_sample.py` named `local.py` in the same directory. Naming is important, since CodaLab will recognize `local.py`.
1. Open `local.py`.
1. In the `DATABASES` section, enter the configuration settings for the database you want to use.

    **SQL Server***
    ```
    DATABASES = {
        'default': {
            'ENGINE': 'sql_server.pyodbc',
            'NAME': 'somename',
            # Leaver user and password blank to use integrated security
            'USER': '',
            'PASSWORD': '',
            'HOST': '(localdb)\\v11.0', 
            'PORT': '',
            'OPTIONS': {
               'driver': 'SQL Server Native Client 11.0',
            }
        }
    ```

    **MySQL**
    ```
    DATABASES = {
        'default': {
            'ENGINE':  'django.db.backends.mysql',
            'NAME': 'MySQL_DevDB',
            'USER': 'someuser',
            'PASSWORD': 'somepassword',
            'HOST': 'someserver', # Empty for localhost through domain sockets or '127.0.0.1' for localhost through TCP.
            'PORT': '',           # Set to empty string for default.
        }
    ```

    **Note:** If you want to use [MySQL](http://www.mysql.com/) you'll need to [manually install](#install-mysql-python) it and create a database before proceeding. CodaLab setup does not install MySQL.

### Initialize the database
To initialize the database, you will need to run a few standard [Django commands](https://docs.djangoproject.com/en/dev/ref/django-admin/), and the CodaLab database initialization script.
- **validate**: Validates all installed models (according to the INSTALLED_APPS setting) and prints validation errors to standard output.
- **syncdb**: Synchronizes the database state with the current set of models and migrations (note that the `migrate` option is used).
- **initialize.py**: Inserts initial data into the database.

1. Run the following commands to initialize the database.

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

1. Run tests to verify that everything is working.

   `python manage.py test`

1. **Optional:** Populate the site with some sample data.

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

## Configure the Bundle Service to run Locally
If you want to run the CodaLab bundle service on the same machine as CodaLab, follow these instructions: [Run the Web Site and Bundle Service on the same development machine](https://github.com/codalab/codalab/wiki/Dev_Run-web-site-and-bundle-service-on-the-same-development-machine).

## Start the web server
1. Use the following command to start the CodaLab server locally.

    ```
    python manage.py runserver
    ```

1. Open a browser and navigate to [http://127.0.0.1:8000](http://127.0.0.1:8000) to preview the site.

1. When your next coding session comes along, remember to work in the virtual environment you created:
    
    **Windows**
    ```
    venv\Scripts\activate
    ```
        
    **Linux**
    ```
    source venv/bin/activate
    ```
    
**Note:** If you experience database errors try deleting the database file (\codalab\codalab\dev_db.*) and run syncdb again. After creating a new database be sure to run `initialize.py` in the `scripts` folder in order to insert initial data required by the app.


## Start the worker roles
In order to test competitions and bundles locally, you'll need to run the compute worker roles. There are two `worker.py` scripts that you will need to run.

1. Open a command prompt and activate your CodaLab virtual environment.
1. Start the first compute worker as shown here:

    ```
    cd codalab
    python worker.py
    ```

1. Open a *second* command prompt and activate your CodaLab virtual environment.
1. Start the second compute worker as shown here:

    ```
    cd codalabtools\compute
    python worker.py
    ```

1. If you plan to test competitions locally, open a *third* command prompt and activate the virtual environment for the CodaLab CLI, then start the bundle server:

   ```
   cl server
   ```