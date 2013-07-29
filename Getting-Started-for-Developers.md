If you are a developer and want to help build CodaLab, here is how to get started:

### Ubuntu Linux 13.04+

Assuming a fresh Ubuntu install the following commands should get you started:

1. sudo apt-get update
    1. enter your password to get admin access
1. sudo apt-get upgrade -y
1. sudo apt-get install git python-pip -y
1. sudo pip install virtualenv
1. git clone https://github.com/codalab/codalab.git
1. cd codalab
1. ./dev_setup.sh
1. source venv/bin/activate
1. python codalab/manage.py syncdb
1. python codalab/scripts/users.py
1. python codalab/scripts/competitions.py
1. python codalab/manage.py runserver 0.0.0.0:8000

### Windows setup (Tested on Windows 8)

Assuming a fresh windows environment the following should get you started:

1. Install python 2.7 (the latest for windows)
   This will install to C:\Python27
1. Using a cmd prompt:
   1. `\Python27\Scripts\easy_install.exe pip`
   1. `\Python27\Scripts\pip.exe install virtualenv`
1. Install git (either of these, or both):
   1. https://code.google.com/p/msysgit/
   1. http://windows.github.com/
1. git clone https://github.com/codalab/codalab.git
1. cd codalab
1. ./dev_setup.bat
1. venv/bin/activate.bat
1. python codalab/manage.py syncdb
1. python codalab/scripts/users.py
1. python codalab/scripts/competitions.py
1. python codalab/manage.py runserver 0.0.0.0:8000