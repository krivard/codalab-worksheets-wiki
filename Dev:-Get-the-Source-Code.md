1. Install the prerequisites:

* [Windows](https://github.com/codalab/codalab/wiki/Dev:-Getting-Started-on-Windows)
* [Unix-based systems](https://github.com/codalab/codalab/wiki/Dev:-Getting-Started-on-UNIX-based-Systems)

1. [Fork](https://help.github.com/articles/fork-a-repo) the [CodaLab repo](https://github.com/codalab/codalab) from GitHub.

1. Clone your fork.
```
git clone https://github.com/<username>/codalab.git
```

1. Configure your local environment. The dev_setup script will install everything you need to run CodaLab locally, including all of the dependencies within the default virtual environment.

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
    venv\Scripts\activate.ps1
    ```
    
    **Linux**
    ```
    source venv/bin/activate
    ```

1. Install [RabbitMQ](http://www.rabbitmq.com/download.html) (optional).

    **Windows**
    Run the .exe installer.
    
    **Linux**
    ```
    sudo apt-get install rabbitmq-server
    ```

1. Now you are ready to install the application schema and default data into the database. The default Dev setup uses a local sqllite database. You will learn how to use a different database backend later; sqllite is sufficient to get started.

    **Windows** 
    ```
    cd c:\users\username\documents\github\codalab\codalab
    config\templates\startup_env.bat
    python manage.py syncdb --migrate
    python scripts\initialize.py
    ```
    
    **Linux**
    ```
    source config/templates/startup_env.sh
    python manage.py syncdb --migrate
    python scripts/initialize.py
    ```

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
    python manage.py runserver 0.0.0.0:8000
    ```

1. Create a local settings override in `codalab/settings/local.py`. There is an example, `local_sample.py`, in the settings directory.

When your next coding session comes along, remember to work in the virtual environment you created:

```
cd C:\Users\username\Documents\GitHub\codalab\
venv\Scripts\activate.ps1
```

If you need to bring the model and database in sync:

```
python codalab/manage.py syncdb --migrate
```
    
Note: If you experience database errors try deleting the database file (\codalab\codalab\dev_db.*) and run syncdb again (but if you create a new database, be sure to run `initialize.py` in the `scripts` folder in order to insert initial data required by the app).
