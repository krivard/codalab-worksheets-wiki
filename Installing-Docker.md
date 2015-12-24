Every execution on CodaLab (should ideally) happen in a
[docker](https://www.docker.com/) container, which provides a standardized
Linux environment that is lighter-weight than a full virtual machine.

The current official docker image is `codalab/ubuntu`, which consists of
Ubuntu 14.04 plus some standard packages.  See the [CodaLab docker
registery](https://registry.hub.docker.com/u/codalab/ubuntu/).

# Installing on Ubuntu Linux

To install docker on your local machine (either if you want see what's actually
in the environment or to run your own local CodaLab instance), follow these
[instructions](http://docs.docker.com/installation/ubuntulinux/):

    sudo sh -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
    sudo apt-get update
    sudo apt-get install lxc-docker
    sudo useradd $USER docker

Then, to test out your environment, open a shell (the first time you do this,
it will take some time to download the image):

    docker run -t -i codalab/ubuntu:1.9

# For OS X / Windows

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