If you are a developer and want to help build CodaLab, this page will explain how to to get started.

## Install Prerequisites

CodaLab is built with Python, supporting development on local machines with Windows, Linux, Mac and BSD. If something is broken, please [open a new issue](https://github.com/codalab/codalab/issues?state=open).

In this section, we will walk you through installing Python 2.7.x, installing prerequisites, and running the CodaLab site in a local virtual environment.

Assuming a fresh Windows environment the following should get you started:

1. Install [Python 2.7](http://www.python.org/download/). Be sure to select the installer for your architecture. This typically will install to C:\Python27. The rest of these instructions will assume that you have installed to this path.

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
cd C:\Python27\Scripts
pip install virtualenv
```

1. Install [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx). Web Platform Installer makes it easy to install a wide variety of packages that you may need.

1. Launch Web Platform Installer and user it to install MySQL 5.1.

1. Install Git: http://msysgit.github.io/ (be sure to check the **Run Git from Windows Command Prompt** option, as this will add Git to your local path).

5. Install and configure Compass.

   - Install Ruby from  [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/). (Version 1.9.3 is recommended). During installation be sure to select the option to add Ruby to your PATH.
 
   - Open a command window and install compass by executing: 

     `gem install compass`

   Take the following steps to generate the css files after you've made changes to the scss files in the sass folder:

     `cd codalab\apps\web\static`

     `compass compile`

   Or to start a process which will automatically re-generate the files when changes are made:

     `cd codalab\apps\web\static`

     `compass watch .`

6. Install the JavaScript Closure compiler.

   - Install Java from  [https://www.java.com/en/download/](https://www.java.com/en/download/).
  
   - Add Java to PATH environment variable. For instance 'c:\Program Files (x86)\Java\jre7\bin'

   - Download and extract Closure from [http://dl.google.com/closure-compiler/compiler-latest.zip](http://dl.google.com/closure-compiler/compiler-latest.zip).

   Take the following steps to generate the javascript files after you've made changes to the js folder:

     `cd codalab\codalab\apps\web\static\js`

     `python ..\..\..\..\..\scripts\javascript.py c:\closure\compiler.jar`

## Next steps
- [Configure CodaLab for Development](Dev_Configure-Codalab-For-Development)
- Review the [Developer Guidelines](Dev_Developer-Guidelines) of the CodaLab team.