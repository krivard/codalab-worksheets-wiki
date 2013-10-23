If you are a developer and want to help build CodaLab, this page will explain how to to get started.

To use and test the submission process, a queue server will need to be installed. RabbitMQ installs easily on Linux and Windows and requires no configuration out of the box for testing purposes. A database besides SQLite is recommended. MySQL and Postgres work on Windows, Linux and Mac. SQL Server on Windows can be used, but support is not as complete as MySQL and Postgres.

## Local installation

CodaLab is built with Python, supporting development on local machines with Windows, Linux, Mac and BSD. If something is broken, please [open a new issue](https://github.com/codalab/codalab/issues?state=open).

In this section, we will walk you through installing Python 2.7.x, installing a version of pip >= 1.3 (for installing Python packages) and running the CodaLab site in a local virtual environment.

### Windows (tested on Windows 8)

Assuming a fresh windows environment the following should get you started:

1. Install python 2.7 (the latest for windows) which you can find [here](http://www.python.org/download/releases/). Be sure to select the installer for your architecture. This typically will install to C:\Python27. The rest of these instructions will assume installation in this path.
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
C:\Python27\Scripts\pip.exe install virtualenv
```

1. Install git (either of these, or both):
   1. https://code.google.com/p/msysgit/
   1. http://windows.github.com/

1. Get the source:
```
git clone https://github.com/codalab/codalab.git
cd codalab
```

1. Create a virtual environment and finish the setup:
```
./dev_setup.bat
```
At this point you should be able to run the site locally:
```
cd codalab
python manage.py runserver 0.0.0.0:8000
```

1. You could also verify your setup by running tests from the directory where `manage.py` is located: 
```
python manage.py test
```

1. If you start from a clean database, there are ways to programmatically insert sample data:
```
python scripts/users.py
python scripts/competitions.py
```
When your next coding session comes along, remember to work in the virtual environment you created:
```
venv/scripts/activate.bat
```
If you need to bring the model and database in sync:
```
python codalab/manage.py syncdb --migrate
```
Note: If you experience database errors try deleting the database file (\codalab\codalab\dev_db.*) and run syncdb again (but if you create a new database, be sure to run `initialize.py` in the `scripts` folder in order to insert initial data required by the app).

#### Work with CodaLab in Visual Studio

You can increase your productivity by writing code, running and debugging the web site in Visual Studio using the [Python Tools for Visual Studio](https://pytools.codeplex.com/). 

**Create the codalab project:**

1. In Visual Studio File menu click **New Project**. Under **Templates**, select **Other Languages**, then choose **Python**.
1. Select the **From Existing Python Code** template.
1. Name the project "codalab".
1. **Location** should be root of Git (for example C:\Users\user\Documents\GitHub\).
1. Uncheck the **Create directory for solution** option.
1. Click OK. The **Create New Project from Existing Python Code** dialog appears.
1. Enter the path for the root of the project (for example C:\Users\user\Documents\GitHub\codalab)
1. add *.scss to the filter list.
1. Click **Next**.
1. Select **manage.py** as the file to run when F5 is pressed.
1. Click **Next**.
1. Enable **Django** as a feature of your project.
1. Click Finish. The Visual Studio project file and solution files should be located in the root folder for the project: codalab\codalab.pyproj & codalab\codalab.sln.

**After you have created the project:**

1. In Solution Explorer right-click the **Python Environments** node and select "Add existing virtual environment...".
1. Pick the codalab\venv directory.
1. Save your solution.
1. In Solution Explorer, right-click on the codalab project node, then choose Django > Validate Django App... The Django Management Console should appear and report zero errors found.

## Next steps

Now that you're setup you'll want to review the [Developer Guidelines](https://github.com/codalab/codalab/wiki/Dev:-Developer-Guidelines) of the CodaLab team.