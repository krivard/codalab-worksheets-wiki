Setup and Deploy a VM in Azure

###  Create a Database
###  Create Storage containers…(public, private, bundles)

### Setup
### Setup code
### Setup Service
### Setup website

## Deploy to VM in Azure
## Create Directory
Example:  mkdir build
cd build

Clone this repo.
git clone https://github.com/beaucephus/msr_codalab_config

use GitHub username and password

To use this to deploy you will need pycrypto and fabric installed, but you dont need it to use this configuration necessarily.

When you have fabric and pycrypto installed, from the top-level of the cloned repo you run:

.\checkout.bat

That will clone codalab into project\codalab

You can use that to develop with or you use it to deploy.

There is a README, but here are the necessary commands (all run from the top-level (msr_codalab_config) where the file fabfile.py resides. In fact, you may need to edit fabfile.py to add the hosts to the roles defined in the various sections, or add new ones. Some refactoring needs to be done, but in time.

When you have setup a vm with a user you can do this:

  fab -k -u <user> -R web Test provision bootstrap UpdateConfig update restart_supervisor

If you want a specific branch all you have to do is specify:

  bootstrap:tag=BRANCH

where BRANCH is the branch you want. 

When you want to update an existing deployment you do this:

  fab -k -u <user> -R web Test update initialize_brats restart_supervisor

If you are still using sqlite, then you might do something like:

  fab -k -u <user> -R web Test rm_sqlite update initialize_brats restart_supervisor

They are run in order, so the order matters.
