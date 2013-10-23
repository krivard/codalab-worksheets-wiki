1. Login to Windows Azure [Management Portal](http://manage.windowsazure.com/)
2. Go to VM Screen, select "+ NEW" from the lower left corner of the screen
3. On the pane that appears select, Compute -> Virtual Machine -> Quick Create

        DNS Name: pick something unique and memorable
        Image: Ubuntu Server 13.04
        New Password/Confirm: provide a good password for the azureuser
        Region/Affinity Group: West US
    => Click Create a virtual machine

4. Once your virtual machine is created, select it from the VM screen
5. Go to the Endpoints menu

    (+ Add) an Endpoint at the bottom of the screen

        Provide the following information
            Name: HTTP
            Public Port: 80
            Private Port: 8000

5. Restart the VM from the management console

    Troubleshooting note: You may get a message that Endpoint was successfully created but restart failed. In this case, go back to Dashboard (Management Console) and click Restart. In some cases, multiple attempts may be required. 

    At this point, it appears that a restart is not mandatory to continue with configuration.

6. Now you can login to it and start configuring things

    First, install PuTTY Configuration tool from [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

7. Now Launch PuTTy from your Start menu

    For our project, include the following Host Name information

        Linux - dev1: cl-dev-ubuntu.cloudapp.net 
        Linux – test1: cl-test1-ubuntu.cloudapp.net

8. Login to your VM via ssh as: azureuser <password provided at vm build - Step 3)
9. Things to do once you're in Ubuntu

    1. sudo apt-get update
        1. enter password (azureuser password from Step 3)
    2. sudo apt-get upgrade -y
    3. sudo apt-get install git python-pip -y
    4. sudo pip install virtualenv
    5. Follow the [the instructions to run CodaLab from github](https://github.com/codalab/codalab/wiki/Test:-Running-CodaLab-from-Git-on-Linux)