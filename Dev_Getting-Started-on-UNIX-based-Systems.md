If you are a developer and want to help build CodaLab, this page will explain how to to get started.

To use and test the submission process, a queue server will need to be installed. [RabbitMQ](http://www.rabbitmq.com/download.html) installs easily on Linux and Windows and requires no configuration out of the box for testing purposes. A database besides SQLite is recommended. MySQL and Postgres work on Windows, Linux and Mac.

## Install Prerequisites

CodaLab is built with Python, supporting development on local machines with Windows, Linux, Mac and BSD. If something is broken, please [open a new issue](https://github.com/codalab/codalab/issues?state=open).

In this section, we will walk you through installing Python 2.7.x, installing prerequisites, and running the CodaLab site in a local virtual environment.

Assuming a fresh Ubuntu install the following commands should get you started. Environment variables are used to control settings for a flexible build process. The build script will accept an environment variable, CONFIG_MODULE_PATH, to a location of an installable python source package which will get installed and be used as the settings module, which is specifically specified with DJANGO_SETTINGS_MODULE.

1. Install [Python 2.7](http://www.python.org/getit/). For current Debian-based Linux distributions (such as Ubuntu), BSD and Mac Python 2.7 is usually installed. However Redhat-based Linux distributions, such as RHEL and CentOS, are sometimes behind the curve and do not have Python 2.7. As of this writing, CentOS 6.4 is at Python 2.6, which is well on its way to EOL. Python 2.6 may work, but code will be written with 2.7 and 3.3+ in mind. Below are instructions for Ubuntu Linux 13.04+.

1. Install [PIP](https://pypi.python.org/pypi/pip).

   `sudo apt-get install python-pip`

1. Install virtualenv.

   `sudo apt-get install python-virtualenv`

1. Install [Git](http://git-scm.com/download/linux).

   `sudo apt-get install git`

1. Install the prerequisites for [MySQL-Python](http://mysql-python.blogspot.no/2012/11/is-mysqldb-hard-to-install.html).

   `apt-get install build-essential python-dev libmysqlclient-dev`

1. Install and configure Compass.

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