Every execution on CodaLab (should ideally) happen in a
[docker](https://www.docker.com/) container, which provides a standardized
Linux environment that is lighter-weight than a full virtual machine.

The current official docker image is `codalab/ubuntu`, which consists of
Ubuntu 14.04 plus some standard packages.  See the [CodaLab docker
registery](https://registry.hub.docker.com/u/codalab/ubuntu/).

# Installing on Ubuntu Linux 14.04

To install docker on your local machine (either if you want see what's actually
in the environment or to run your own local CodaLab instance), follow these
[instructions](https://docs.docker.com/engine/installation/ubuntulinux/):

    sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
    sudo sh -c "echo deb https://apt.dockerproject.org/repo ubuntu-trusty main > /etc/apt/sources.list.d/docker.list"
    sudo apt-get update
    sudo apt-get install docker-engine
    sudo usermod -a -G docker $USER

Note that after adding yourself to the docker group you need to log out and log back in for the setting to take effect.

If you are upgrading docker, then you might need to remove `devicemapper` by [doing this](https://github.com/docker/docker/issues/14088).

To start docker:

    sudo service docker start

Then, to test out your environment, open a shell (the first time you do this,
it will take some time to download the image):

    docker run -ti ubuntu bash

# Installing on OS X / Windows

Because Windows and OS X don't support LXC natively, you need to actually install Docker in a VM that you access from your machine. Thankfully, Docker has already packaged a solution for this that they release whenever they have a version bump of docker: [Docker Toolbox (click for download page)](https://www.docker.com/docker-toolbox). After selecting the download for your OS, go through and follow the setup instructions for your platform:

* [Mac OS X](https://docs.docker.com/mac/). Also see the OS X Environment Settings section below.
* [Windows](https://docs.docker.com/windows/)

# OS X Environment Settings
The `docker-machine` binary is used to control the VM running Docker. By default, there is a VM named `"default"` that you can start and stop when you would like to use Docker.

## Starting Docker

To start the VM and allow you to use Docker:

```bash
> docker-machine start default
> eval $(docker-machine env default)
```