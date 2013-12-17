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
    ./dev_setup.bat
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

Now you are ready to install the application schema and default data into the database. The default Dev setup uses a local sqllite database. You will learn how to use a different database backend later; sqllite is sufficient to get started.

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
## Test your site

1. Run tests to verify that everything is working:

    ```
    python manage.py test
    ```

1. Populate the site with some sample data:

    ```        
    python scripts/users.py
    python scripts/competitions.py
    ```

1. Start the web server:

    ```
    python manage.py runserver
    ```

1. Create a local settings override in `codalab/settings/local.py`. There is an example, `local_sample.py`, in the settings directory.

**Important:** When your next coding session comes along, remember to work in the virtual environment you created:

    **Windows**
    ```
    venv\Scripts\activate
    ```
    
    **Linux**
    ```
    source venv/bin/activate
    ```

To sync the model and database:

```
python codalab/manage.py syncdb --migrate
```
    
Note: If you experience database errors try deleting the database file (\codalab\codalab\dev_db.*) and run syncdb again (but if you create a new database, be sure to run `initialize.py` in the `scripts` folder in order to insert initial data required by the app).

## Create an Azure Storage account

In order to use CodaLab you will need to have a Windows Azure account.