This page contains instructions for installing and configuring Python to work with the CodaLab project.

### Prerequisites
* Visual Studio 2012
* [Python 2.7](http://python.org/ftp/python/2.7.5/python-2.7.5.msi) from [Python.org](http://python.org/).
* [Python Tools for Visual Studio](https://pytools.codeplex.com/)

### Get the CodaLab source
**Fork and Clone the codalab repo**

1. Go to [https://github.com/codalab/codalab](https://github.com/codalab/codalab).
2. Fork the repo.
3. Go to your account page at GitHub.com and clone Codalab to your local computer.

For help with forking and cloning, see [Fork a Repo](https://help.github.com/articles/fork-a-repo). For more information about working with Git, see: [http://git-scm.com/book](http://git-scm.com/book).

### Running the site with Visual Studio
**Create the codalab project:**

1. In Visual Studio File menu click **New Project**. Under **Templates**, select **Other Languages**, then choose **Python**.
2. Select the **From Existing Python Code** template.
3. Name the project "codalab".
4. **Location** should be root of Git (for example C:\Users\user\Documents\GitHub\).
5. Uncheck the **Create directory for solution** option.
6. Click OK. The Create New Project from Existing Python Code dialog appears.
7. Enter the path for the root of the project (for example C:\Users\user\Documents\GitHub\codalab)
8. add *.scss to the filter list.
9. Click **Next**.
10. Select **Python 2.7** from the dropdown menu.
11. Click Finish. The Visual Studio project file and solution files should be located in the root folder for the project: codalab\codalab.pyproj & codalab\codalab.sln.

**After you have created the project:**

1. In Solution Explorer right-click the **Python Environments** node and select "Add existing virtual environment...".
2. Pick the codalab\venv directory.


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

