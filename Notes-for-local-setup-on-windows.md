If you are developing on Windows, these notes should be helpful. Thy need to be cleaned up but should be accurate.

### Running the site with Visual Studio

Install Python Tools for Visual Studio. Then, create a new project using the option "From Existing Python code". You'll want the Visual Studio project file and solution files to be in the root folder: codalab\codalab.pyproj & codalab\codalab.sln. When the project is created, use "Add existing virtual environment..." option on Python Environments and pick the codalab\venv directory.

### Running against a local SQL server instance.

Install pyodbc: Download the correct flavor from https://code.google.com/p/pyodbc/. Then from your virtual environment:

    easy_install <path>\<pyodbc_installer_file>.exe.

Install the Django-pyodbc-azure package: 

    pip install django-pyodbc-azure.

Create a database in your SQL database:

    create database djangodb.

Modify your local configuration

    DB_ENGINE = 'sql_server.pyodbc'
    DB_NAME = 'djangodb' # Database name for django; from previous steps
    DB_HOST = '(localdb)\\v11.0'
    DB_PORT = ''
    DB_USER = ''
    DB_PASSWORD = '' 
    DB_OPTIONS =  {
        'driver': 'SQL Server Native Client 11.0', # The driver as specified by the Azure Management Console
    }
  
    DATABASES = {
        'default': {
            'ENGINE':  DB_ENGINE, 
            'NAME': DB_NAME,            
            'HOST': DB_HOST,                     
            'PORT': DB_PORT,
            'USER': DB_USER,
            'PASSWORD': DB_PASSWORD,
            'OPTIONS': DB_OPTIONS,
        }
    }

Notice that User and Password are left blank in this example in order to use integrated security.

Once you've carried out the steps above, you should be able to use the normal workflow, e.g.:

    python manage.py syncdb
    cd scripts
    python initialize.py
    python users.py
    python competitions.py

### Processing tasks

Install Erlang: http://www.erlang.org/download.html

Install RabbitMQ (including the service): http://www.rabbitmq.com/releases/rabbitmq-server/v3.1.5/rabbitmq-server-3.1.5.exe

Celery is in the requirements.

Setup your local config:

    BROKER_URL = "amqp://guest:guest@localhost:5672//"
    CELERY_RESULT_BACKEND = "amqp"
    CELERY_TASK_RESULT_EXPIRES=3600

    COMPUTATION_SUBMISSION_URL = 'http://<host>/api/computation/'

At the command prompt:

    python manage.py config_gen

Then you can use the manage.bat script which will make sure all the right variables are set. In one terminal you need to run:

    .\runserver.bat

Then in another you need to run:

    .\runcelery.bat

