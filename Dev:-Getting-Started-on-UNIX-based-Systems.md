If you are a developer and want to help build CodaLab, this page will explain how to to get started.

To use and test the submission process, a queue server will need to be installed. [RabbitMQ](http://www.rabbitmq.com/download.html) installs easily on Linux and Windows and requires no configuration out of the box for testing purposes. A database besides SQLite is recommended. MySQL and Postgres work on Windows, Linux and Mac.

## Install Prerequisites

CodaLab is built with Python, supporting development on local machines with Windows, Linux, Mac and BSD. If something is broken, please [open a new issue](https://github.com/codalab/codalab/issues?state=open).

In this section, we will walk you through installing Python 2.7.x, installing prerequisites, and running the CodaLab site in a local virtual environment.

Assuming a fresh Ubuntu install the following commands should get you started. Environment variables are used to control settings for a flexible build process. The build script will accept an environment variable, CONFIG_MODULE_PATH, to a location of an installable python source package which will get installed and be used as the settings module, which is specifically specified with DJANGO_SETTINGS_MODULE.

1. Install [Python 2.7](http://www.python.org/getit/). For current Debian-based Linux distributions, BSD and Mac Python 2.7 is usually installed. However Redhat-based Linux distributions, such as RHEL and CentOS, are sometimes behind the curve and do not have Python 2.7. As of this writing, CentOS 6.4 is at Python 2.6, which is well on its way to EOL. Python 2.6 may work, but code will be written with 2.7 and 3.3+ in mind. Below are instructions for Ubuntu Linux 13.04+.

1. Install [PIP](https://pypi.python.org/pypi/pip).
```
sudo apt-get install python-pip
```

1. Install virtualenv.
```
sudo apt-get install python-virtualenv
```

1. Install [Git](http://git-scm.com/download/linux).
```
sudo apt-get install git
```

## Get the Source Code

1. [Fork](https://help.github.com/articles/fork-a-repo) the [CodaLab source code](https://github.com/codalab/codalab) from GitHub.

1. Clone your fork.
```
git clone https://github.com/username/codalab.git
```

1. Configure your local environment. The dev_setup.sh script will install everything you need to run CodaLab locally, including all of the dependencies within the default virtual environment.
```
cd codalab
source ./dev_setup.sh
```

1. Activate the virtual environment.
```
source venv/bin/activate
```

1. Install [RabbitMQ](http://www.rabbitmq.com/download.html) (optional)
```
sudo apt-get install rabbitmq-server
```

1. Now you are ready to install the application schema and default data into the database. The default Dev setup uses a local sqllite database. You will learn how to use a different database backend later; sqllite is sufficient to get started. 
```
cd codalab
source config/templates/startup_env.sh
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
python codalab/manage.py runserver 0.0.0.0:8000
```

1. Create a local settings override in `codalab/settings/local.py`. There is an example, `local_sample.py`, in the settings directory.

## Next steps

Now that you're setup you might want to review some of the [conventions and practices](https://github.com/codalab/codalab/wiki/Dev:-Developer-Guidelines) of the CodaLab team.