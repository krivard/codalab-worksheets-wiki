This page contains instructions for running against a local SQL server instance on Windows.

1. Follow the instructions: [Getting Started on Windows](https://github.com/codalab/codalab/wiki/Dev:-Getting-Started-on-Windows).

1. From your virtual environment:
```
easy_install pyodbc
```
Install the Django-pyodbc-azure package: 
```
pip install "django-pyodbc-azure<1.1"
```
Create a database in your SQL database:
```
create database djangodb.
```
Modify your local configuration
```
DB_ENGINE = 'sql_server.pyodbc'
DB_NAME = 'djangodb' # Database name for django; from previous steps
DB_HOST = '(localdb)\\\\v11.0'
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
```
Notice that User and Password are left blank in this example in order to use integrated security.

Once you've carried out the steps above, you should be able to use the normal workflow, e.g.:
```
python manage.py syncdb --migrate
cd scripts
python initializer.py
python users.py
python competitions.py
```

### Processing tasks

Install Erlang: http://www.erlang.org/download.html

Install RabbitMQ (including the service): http://www.rabbitmq.com/releases/rabbitmq-server/v3.1.5/rabbitmq-server-3.1.5.exe

Celery is in the requirements.

Setup your local config:
```
BROKER_URL = "amqp://guest:guest@localhost:5672//"
CELERY_RESULT_BACKEND = "amqp"
CELERY_TASK_RESULT_EXPIRES=3600

COMPUTATION_SUBMISSION_URL = 'http://<host>/api/computation/'
```
At the command prompt:
```
python manage.py config_gen
```
Then you can use the manage.bat script which will make sure all the right variables are set. In one terminal you need to run:
```
.\runserver.bat
```
Then in another you need to run:
```
.\runcelery.bat
```
