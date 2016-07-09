Every run bundle is created by executing a command in a
[Docker](https://www.docker.com/) container, which provides a standardized
Linux environment that is lighter-weight than a full virtual machine.

You need to install Docker if you intend to execute CodaLab runs yourself.
There are two use cases:

1. You have set up [your own CodaLab server](Server-Setup).
1. You want to run [your own worker](Execution#running-your-own-worker) that
   connects to the main CodaLab server ([worksheets.codalab.org](https://worksheets.codalab.org)).

# Installing on Ubuntu Linux 14.04

Follow these [instructions](https://docs.docker.com/engine/installation/ubuntulinux/):

    sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
    sudo sh -c "echo deb https://apt.dockerproject.org/repo ubuntu-trusty main > /etc/apt/sources.list.d/docker.list"
    sudo apt-get update
    sudo apt-get install docker-engine
    sudo usermod -a -G docker $USER

Note that after adding yourself to the docker group you need to log out and log back in for the setting to take effect.

If you are upgrading Docker, then you might need to remove `devicemapper` by [doing this](https://github.com/docker/docker/issues/14088).

To start Docker:

    sudo service docker start

To test that everything is working, type:

    docker run --rm codalab/ubuntu:1.9 cat /etc/issue

# Installing on OS X / Windows

Because Windows and OS X don't support LXC natively, you need to actually
install Docker in a VM that you access from your machine. Thankfully, Docker
has already packaged a solution for this that they release whenever they have a
version bump of Docker: [Docker Toolbox (click for download
page)](https://www.docker.com/docker-toolbox). After selecting the download for
your OS, go through and follow the setup instructions for your platform:

* [Mac OS X](https://docs.docker.com/mac/). Also see the OS X Environment Settings section below.
* [Windows](https://docs.docker.com/windows/)

## OS X environment settings

The `docker-machine` binary is used to control the VM running Docker. By
default, there is a VM named `"default"` that you can start and stop when you
would like to use Docker.

To start the VM:

```bash
docker-machine start default
eval $(docker-machine env default)
```

This should be run in the same shell session before you start the worker. In the shell session you can run any of the `docker` commands to check that docker can connect to the local docker "machine". The commands will usually hang for a long time if the machine is not started or the environment variables are set incorrectly.
