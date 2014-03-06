1. Now Launch PuTTy from your Start menu

    For our project, include the following Host Name information

        Linux - dev: cl-dev-ubuntu.cloudapp.net 
        Linux – test: cl-test1-ubuntu.cloudapp.net

2. Login to your VM via ssh as: azureuser <password provided at vm build - Step 3)

If no breaking changes have been made to the CodaLab codebase (which should be communicated in the github issue),

3. Update CodaLab 
   1. kill -9 `ps auwx | grep -e "python \./.*py" | awk '{ print $2 }' | xargs`
   1. cd codalab
   1. git pull
   1. source venv/bin/activate
   1. rm dev_db.sqlite
   1. python codalab/manage.py syncdb
   1. python codalab/scripts/initialize.py
   1. python codalab/scripts/users.py
   1. python codalab/scripts/competitions.py
   1. ./run_on_linux_vm.sh

Otherwise, if breaking changes are made, 

4. Update CodaLab (if breaking changes have been made)
   1. kill -9 \`ps auwx | grep python  | grep codalab | awk '{ print  }' | xargs\`
   1. rm -rf codalab
   1. Follow the [instructions to run CodaLab from github on Linux](https://github.com/codalab/codalab/wiki/Test:-Running-CodaLab-from-Git-on-Linux)

Now you should be able to open a browser and type in your DNS URL to access CodaLab.
	In this example it would be http://cl-dev-ubuntu.cloudapp.net/