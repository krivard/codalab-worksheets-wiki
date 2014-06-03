If you are a developer and want to help build CodaLab, this page will explain how to to get started.

## Install Prerequisites

CodaLab is built with Python, supporting development on local machines with Windows, Linux, Mac and BSD. If something is broken, please [open a new issue](https://github.com/codalab/codalab/issues?state=open).

In this section, we will walk you through installing Python 2.7.x, installing prerequisites, and running the CodaLab site in a local virtual environment.

Assuming a fresh Windows environment the following should get you started:

1. Install [Python 2.7](http://www.python.org/download/). Be sure to select the appropriate installer for your architecture. This typically will install to C:\Python27. The rest of these instructions will assume that you have installed to this path.

1. To Install pip you should first install distribute. Download the file at the following URL:
   http://python-distribute.org/distribute_setup.py and run it from a shell from the directory into which it was downloaded:

    ```
    C:\Python27\python.exe distribute_setup.py
    ```

    This will put easy_install.exe in the Scripts directory of your Python installation. Then, you're ready to install pip from a command prompt:
    ```
    C:\Python27\Scripts\easy_install.exe pip
    ```

1. Next install [virtualenv](http://www.virtualenv.org/)

    ```
    cd C:\Python27\Scripts
    pip install virtualenv
    ```

1. Activate the virtual environment.
    
    `venv\scripts\activate`

1. Install Git: http://msysgit.github.io/ (be sure to check the **Run Git from Windows Command Prompt** option, as this will add Git to your local path).

## Install MySQL
Follow these steps to install MySQL 5.1 on your system.

1. Install [Microsoft Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx). Web Platform Installer makes it easy to install a wide variety of packages that you may need.

1. Launch Web Platform Installer and use it to install MySQL 5.1.

## Install Compass
If you want to make changes to CodaLab's stylesheets via SCSS files, you will need to install [Compass](http://compass-style.org/). Note that this step is only required if you plan to make any changes to stylesheets.

1. Install Ruby from  [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/). (Version 1.9.3 is recommended). During installation be sure to select the option to add Ruby to your PATH.

1. Open a command window and install compass by executing: 
    
    `gem install compass`
    
    Each time you make changes to the SCSS files in the **sass** folder, take the following steps to generate the CSS files:
        ```
        cd codalab\apps\web\static
        compass compile
        ```
    
    Alternatively, you can run the `compass watch` command to start a process which will automatically re-generate the files when changes are made:
        ```
        cd codalab\apps\web\static
        compass watch .
        ```
    
    For more information, see [https://rubygems.org/gems/compass](https://rubygems.org/gems/compass).

## Install the JavaScript Closure Compiler
If you need to modify any JavaScript files, you'll need the [JavaScript Closure Compiler](https://developers.google.com/closure/compiler/). Note that this step is only required if you make changes to JavaScript files.

1. Install the JavaScript Closure compiler.

    &bull; Install Java from  [https://www.java.com/en/download/](https://www.java.com/en/download/).
    
    &bull; Add Java to PATH environment variable. For instance 'c:\Program Files (x86)\Java\jre7\bin'
    
    &bull; Download and extract Closure from [http://dl.google.com/closure-compiler/compiler-latest.zip](http://dl.google.com/closure-compiler/compiler-latest.zip).

    &bull; Create `C:\closure` and copy **compiler.jar** into the new folder.

1. Take the following steps to generate the JavaScript files after you've made changes to the js folder:

    `cd codalab\codalab\apps\web\static`

    `python ..\..\..\..\scripts\javascript.py c:\closure\compiler.jar`

   For more information, see [https://developers.google.com/closure/compiler/](https://developers.google.com/closure/compiler/)

## Next steps
- [Configure CodaLab for Development](Dev_Configure-Codalab-For-Development)
- Review the [Developer Guidelines](Dev_Developer-Guidelines) of the CodaLab team.