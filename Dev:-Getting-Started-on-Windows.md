If you are a developer and want to help build CodaLab, this page will explain how to to get started.

To use and test the submission process, a queue server will need to be installed. [RabbitMQ](http://www.rabbitmq.com/download.html) installs easily on Linux and Windows and requires no configuration out of the box for testing purposes. A database besides SQLite is recommended. MySQL and Postgres work on Windows, Linux and Mac. You can also use SQL Server on Windows. For more information, see [Running against a local SQL server instance](https://github.com/codalab/codalab/wiki/Test:-Running-against-a-local-SQL-server-instance)

## Install Prerequisites

CodaLab is built with Python, supporting development on local machines with Windows, Linux, Mac and BSD. If something is broken, please [open a new issue](https://github.com/codalab/codalab/issues?state=open).

In this section, we will walk you through installing Python 2.7.x, installing prerequisites, and running the CodaLab site in a local virtual environment.

Assuming a fresh Windows environment the following should get you started:

1. Install [Python 2.7](http://www.python.org/getit/). Be sure to select the installer for your architecture. This typically will install to C:\Python27. The rest of these instructions will assume that you have installed to this path.

1. To Install pip you should first install distribute. Download the file at the following URL:
   http://python-distribute.org/distribute_setup.py and run it from a shell from the directory into which it was downloaded:
```
C:\Python27\python.exe distribute_setup.py
```
This will put easy_install.exe in the Scripts directory of your Python installation. Then, you're ready to install pip from a cmd prompt:
```
C:\Python27\Scripts\easy_install.exe pip
```

1. Next install [virtualenv](http://www.virtualenv.org/)
```
pip install virtualenv
```

1. Install git (either of these, or both):

   * https://code.google.com/p/msysgit/
   * http://windows.github.com/

## Get the Source Code

1. [Fork](https://help.github.com/articles/fork-a-repo) the [CodaLab source code](https://github.com/codalab/codalab) from GitHub.

1. Clone your fork.
```
git clone https://github.com/username/codalab.git
```

1. Configure your local environment. The dev_setup.bat script will install everything you need to run CodaLab locally, including all of the dependencies within the default virtual environment.
```
./dev_setup.bat
```

1. Activate the virtual environment.
```
cd C:\Users\username\Documents\GitHub\codalab\
venv\Scripts\activate.ps1
```

1. Install [RabbitMQ](http://www.rabbitmq.com/download.html) (optional).

1. Now you are ready to install the application schema and default data into the database. The default Dev setup uses a local sqllite database. You will learn how to use a different database backend later; sqllite is sufficient to get started. 
```
cd codalab
config/templates/startup_env.bat
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

## Next steps

Now that you're setup you'll want to review the [Developer Guidelines](https://github.com/codalab/codalab/wiki/Dev:-Developer-Guidelines) of the CodaLab team.