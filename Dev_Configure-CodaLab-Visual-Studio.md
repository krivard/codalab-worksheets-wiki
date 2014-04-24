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
