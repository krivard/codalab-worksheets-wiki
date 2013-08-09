1. Now Launch PuTTy from your Start menu

    For our project, include the following Host Name information

        Linux - dev: cl-dev-ubuntu.cloudapp.net 
        Linux – test: cl-test1-ubuntu.cloudapp.net

2. Login to your VM via ssh as: azureuser <password provided at vm build - Step 3)
3. Update CodaLab
   1. kill -9 \`ps auwx | grep python  | grep codalab | awk '{ print  }' | xargs\`
   1. cd codalab
   1. git fetch --all
   1. git pull
   1. source venv/bin/activate
   1. ./requirements dev.txt azure.txt
   1. rm dev_db.sqlite
   1. python codalab/manage.py syncdb
   1. python codalab/scripts/initialize.py
   1. python codalab/scripts/users.py
   1. python codalab/scripts/competitions.py
   1. ./run_on_linux_vm.sh

Now you should be able to open a browser and type in your DNS URL to access CodaLab.
	In this example it would be http://cl-dev-ubuntu.cloudapp.net/