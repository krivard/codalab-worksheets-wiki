**Assumptions**
* You are on a fresh machine: CodaLab has not been installed or CodaLab has been removed

If that is true these steps should work:
 
1. git clone https://github.com/codalab/codalab.git
2. cd codalab
3. ./dev_setup.sh
4. source venv/bin/activate
5. Run CodaLab by either:
    5. python codalab/manage.py runserver 0.0.0.0:8000, or
    5. ./run_on_linux_vm.sh

Now you should be able to open a browser and type in your DNS URL to access CodaLab.
	In this example it would be http://cl-dev-ubuntu.cloudapp.net/