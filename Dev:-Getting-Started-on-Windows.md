If you are a developer and want to help build CodaLab, this page will explain how to to get started.

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
cd C:\Python27\Scripts
pip install virtualenv
```

1. Install Git: http://msysgit.github.io/ (be sure to check the **Run Git from Windows Command Prompt** option, as this will add Git to your local path).

## Next steps
- [Configure CodaLab for development](https://github.com/codalab/codalab/wiki/Dev:-Configure-Codalab-For-Development).
- Review the [Developer Guidelines](https://github.com/codalab/codalab/wiki/Dev:-Developer-Guidelines) of the CodaLab team.