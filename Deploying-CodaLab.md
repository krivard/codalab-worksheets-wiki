The following steps descripbe how to deploy CodaLab from scratch.

The procedure assumes that you have access to a [Windows Azure](https://account.windowsazure.com/) subscription which will host the deployment. 

## Setup a build machine

Login to the [Windows Azure management portal](http://manage.windowsazure.com) using the appropriate Microsoft Account ID.

From the management portal, [create a new Virtual Machine from gallery](http://www.windowsazure.com/en-us/manage/linux/tutorials/virtual-machine-from-gallery/):

* OS = Ubuntu 13.04
* VM size = medium (2 cores)
* For the VM credentials, we recommend setting both a username/password and a certificate for the admin user. This [intro](http://www.windowsazure.com/en-us/manage/linux/tutorials/intro-to-linux/) provides more details about authentication with SSH keys.
* Set machine name and DNS name using the convention `<prefix>-build`. For instance, if the unique prefix is `codalab`, then the machine name is `codalab-build` and the DNS name is `codalab-build.cloudapp.net`.
* Specify the location of the VM. Our current choice is to co-locate them in the 'West US' region.

When the server is provisioned, use SSH to log into it using the admin user.

In order to setup the build server, run these commands to get the basic packages installed:

    sudo apt-get install -y git

You also need to checkout the private configuration:

	mkdir src
    cd src
    git clone https://codalab-project.visualstudio.com/DefaultCollection/_git/codalab-config

Finally, the `codalab-config` directory contains a file named `bootstrap.sh`:

	cd codalab-config
    bash ./bootstrap.sh
    sudo pip install fabric

Now the build server should be all setup.

## Deploy a configuration of your choice

There can be multiple, independent deployments of the CodaLab site. Each deployment is identified with a label. Typically, the live site is the `prod` deployment, then there are `test` or `dev` deployments.

### Setup the database

The application for a given deployment connects to [a MySQL database in the cloud](http://www.windowsazure.com/en-us/store/service/?id=b2e344bf-2252-44f5-9f5f-4f7aac6d4fa3). A database instance is created from the Windows Azure management portal: New > Store > Select ClearDB MySQL database.

### Setup machine instances

In a new deployment, the first step is to create one or more VMs to run the web site. From the management portal, [create a new Virtual Machine from gallery](http://www.windowsazure.com/en-us/manage/linux/tutorials/virtual-machine-from-gallery/):

* OS = Ubuntu 13.04
* VM size = medium (2 cores)
* For the VM credentials, we recommend setting both a username/password and a certificate for the admin user. This [intro](http://www.windowsazure.com/en-us/manage/linux/tutorials/intro-to-linux/) provides more details about authentication with SSH keys.
* Set machine name using the convention `<prefix>-<label><number>`. For example, for a production deployment, the unique prefix might be `codalab` and the deployment label `prod`; thus the first machine in the deployment set would be named `codalab-prod1`.
* Set DNS name using the convention `<prefix>-<label>.cloudapp.net` (e.g. `codalab-prod.cloudapp.net`). It is important to note that if multiple VMs are going to be load-balanced, the first VM will be created in a new cloud service but subsequent VMs will be created in the cloud service of the first VM. See this [description of how to load balance VMs in Windows Azure](http://www.windowsazure.com/en-us/manage/windows/common-tasks/how-to-load-balance-virtual-machines/).
* You should also group related VMs in one availability set. Create the availability set when the first VM is provisioned and add subsequent VMs to the same set. Name the set using the convention: `<prefix>-<label>` (e.g. `codalab-prod`). See [manage VM availability in Windows Azure](http://www.windowsazure.com/en-us/manage/windows/common-tasks/manage-vm-availability/) for further information.
* Specify the location of the VM. Our current choice is to co-locate them in the 'West US' region.

After each VM is created add a load-balance HTTP endpoint:
* Name: HTTP
* Protocol: TCP
* ports: 80

Follow [these instructions](http://www.windowsazure.com/en-us/manage/windows/common-tasks/how-to-load-balance-virtual-machines/) to load-balance them. Basically, when creating the first endpoint, create a load-balanced set using the naming convention `<prefix>` (e.g. `codalab-prod`). Wait for the endpoint to be created. Then, when adding an endpoint to the remaining VMs, simply add the new HTTP endpoint to the existing load-balanced set. 

The current deployment also uses one additional machine to host the queues. Create the VM from the gallery in the management portal:

* OS = Ubuntu 13.04
* VM size = medium (2 cores)
* Setting both a username/password and a certificate for the admin user.
* Set machine name and DNS name. The naming convention is `<prefix>-q` (e.g. `codalab-q`) because multiple deployments share the same queue server.
* Specify the location of the VM. Our current choice is to co-locate machines in the 'West US' region.

After the VM is provisioned, add an endpoint:
* Name: AMPQ
* Protocol: TCP
* ports: 5672

### Deploy the bits

#### Setup the configuration.

For a new deployment setup, one will need to create a matching configuration in the [codalab-config](https://codalab-project.visualstudio.com/) repo.

In the rest of this document, we will assume that the name of the new configuration is **ProdEx**. We will also assume that the servers have been creating using the naming conventions outlined above with a unique prefix of `codalab` and a label of `prodex`.

1. Edit `fabfile.py`. Copy and paste one of the existing configuration to create the new configuration. For our fictive **ProdEx** deployment, we would add:  

```
def prodex(configuration='ProdEx', build_base_dir='codalab'):
    # set the config name
    env.config_set = 'prodex'

    env.http_port = '80' 
    # set the outward facing DNS name
    env.server_name = 'codalab-prodex.cloudapp.net'

    env.django_configuration=configuration
    set_config()
   
    env.build_base_dir=build_base_dir

    # set the user@host for the build server
    env.roledefs = { 'build': ['youruser@codalab-build.cloudapp.net'] }

    # set the user@host:port for the servers
    env.roledefs.update( {'web': ['youruser@codalab-prodex.cloudapp.net:22',
                                  'youruser@codalab-prodex.cloudapp.net:62945'
                                  ],
                          'queue': ['youruser@codalab-q.cloudapp.net'],
                          'workers': [],
                          })

    env.SHELL_ENV.update(dict(DJANGO_CONFIGURATION=env.django_configuration,
                              CONFIG_HTTP_PORT=env.http_port,
                              CONFIG_SERVER_NAME=env.server_name))
```

Notice how the two web servers are not referred to using the machine names. Instead of `codalab-prodex2.cloudapp.net`, we have `codalab-prodex.cloudapp.net:62845`. The latter host name and port number are available in the Windows Azure management portal under 'SSH Details' in the Dashboard view of the selected VM.

2. Edit the site configuration in the folder `site-configuration\configurations`. For **ProdEx**, we would edit `prod.py` and create a new class deriving from the existing configuration:

```
 	class Prod2(Prod): 
        # override settings...
```

The new class should override existing settings as appropriate.

#### Provision the various servers and deploy.

Login to the build server and go into the codalab-config directory:

    cd src/codalab-config


This setup is only performed after new machines are created for a given deployment. Assuming the deployment label is `prodex`:

    fab prodex provision_build 
    fab prodex provision_web 
    fab prodex provision_queue

You are now ready for your first deployment:

    CONFIG_SERVER_NAME=codalab-prodex.cloudapp.net CONFIG_HTTP_PORT=80 fab tag:master prodex build push_build deploy_web supervisor nginx_restart

In the above example, we are deploying the head of the `master` branch into the web servers which are load-balanced under the address `codalab-prodex.cloudapp.net`. You can think of the deployment as follows. The `build` step combines the source code from `master` and the configuration for `prodex`. The resulting package is pushed (`push_build`) and deployed (`deploy_web`) to each web server. The supervisor services and the web server are also restarted on each web server. 

Final step: open a web browser and ensure that the site is up!

When a next set of changes becomes available, you can deploy them using:

    CONFIG_SERVER_NAME=codalab-prodex.cloudapp.net CONFIG_HTTP_PORT=80 fab tag:master prodex build supervisor_stop push_build deploy_web supervisor nginx_restart

## Contribution opportunities

* Automate provisioning of VMs using the [Windows Azure cross-platform command line tools](https://github.com/WindowsAzure/azure-sdk-tools-xplat).
