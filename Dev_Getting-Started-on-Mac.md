If you are a developer and want to help build CodaLab, this page will explain how to to get started.

To use and test the submission process, a queue server will need to be installed. [RabbitMQ](http://www.rabbitmq.com/download.html) installs easily on Linux and Windows and requires no configuration out of the box for testing purposes. A database besides SQLite is recommended. MySQL and Postgres work on Windows, Linux and Mac.

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

8. Install and configure Compass.

   - Install Ruby from  [http://rubyinstaller.org/downloads/](http://rubyinstaller.org/downloads/). (Version 1.9.3 is recommended). During installation be sure to select the option to add Ruby to your PATH.
 
   - Open a command window and install compass by executing: 

     `sudo gem install compass`

   Take the following steps to generate the css files after you've made changes to the scss files in the sass folder:

     `cd codalab/apps/web/static`

     `compass compile`

   Or to start a process which will automatically re-generate the files when changes are made:

     `cd codalab/apps/web/static`

     `compass watch .`

## Next steps
- [Configure CodaLab for Development](Dev_Configure-Codalab-For-Development)
- Review the [Developer Guidelines](Dev_Developer-Guidelines) of the CodaLab team.