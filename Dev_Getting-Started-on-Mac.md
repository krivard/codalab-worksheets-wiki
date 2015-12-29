If you are a developer and want to help build CodaLab, this page will explain how to to get started.

To use and test the submission process, a queue server will need to be installed. [RabbitMQ](http://www.rabbitmq.com/download.html) installs easily on a Mac and requires no configuration out of the box for testing purposes. A database besides SQLite is recommended. MySQL and Postgres work on Windows, Linux and Mac.

## Install Prerequisites

CodaLab is built with Python, supporting development on local machines with Windows, Linux, Mac and BSD. If something is broken, please [open a new issue](https://github.com/codalab/codalab/issues?state=open).

In this section, we will walk you through installing Python 2.7.x, installing prerequisites, and running the CodaLab site in a local virtual environment.

1. Install [Python 2.7](http://www.python.org/getit/).

2. Install [PIP](https://pypi.python.org/pypi/pip).

   `sudo easy_install python-pip`

3. Install virtualenv.

   `sudo easy_install virtualenv`

4. Install [Git](https://code.google.com/p/git-osx-installer/).

5. Install [MySQL](http://dev.mysql.com/downloads/file.php?id=450908)

   `export PATH=$PATH:/usr/local/mysql/bin`

6. Install [XCode](https://developer.apple.com/xcode/)

7. Install GCC. XCode Menu > Preferences > Downloads > Install Command Line Tools

## Install Compass (optional)
If you want to make changes to CodaLab's stylesheets via SCSS files, you will need to install [Compass](http://compass-style.org/). Note that this step is only required if you plan to make any changes to stylesheets.

   - Install Ruby from  [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/). (Version 1.9.3 is recommended). During installation be sure to select the option to add Ruby to your PATH.
 
   - Open a command window and install compass by executing: 

     `sudo gem install compass`

   Take the following steps to generate the css files after you've made changes to the scss files in the sass folder:

     `cd codalab/apps/web/static`

     `compass compile`

   Or to start a process which will automatically re-generate the files when changes are made:

     `cd codalab/apps/web/static`

     `compass watch .`

## Install the JavaScript Closure Compiler (optional)
If you need to modify any JavaScript files, you'll need the [JavaScript Closure Compiler](https://developers.google.com/closure/compiler/). Note that this step is only required if you make changes to JavaScript files.

   - Install Java from  [https://www.java.com/en/download/](https://www.java.com/en/download/).
  
   - Add Java bin folder to the PATH environment variable.

   - Download and extract Closure from [http://dl.google.com/closure-compiler/compiler-latest.zip](http://dl.google.com/closure-compiler/compiler-latest.zip).

   Take the following steps to generate the javascript files after you've made changes to the js folder:

     `cd codalab/codalab/apps/web/static`

     `python ../../../../scripts/javascript.py ~/java/compiler.jar`

You can also make this easier by `pip install watchdog` and then:

`cd codalab/codalab/apps/web/static`

`watchmedo shell-command --patterns="*.js" --recursive --command "python ../../../../scripts/javascript.py ~/java/compiler.jar" js/`

## Next steps
- [Configure CodaLab for Development](Setup-Local-Competitions##Get the source code)
- Review the [Developer Guidelines](Dev_Developer-Guidelines) of the CodaLab team.