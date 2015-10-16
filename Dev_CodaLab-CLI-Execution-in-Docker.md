## Execution using docker

Every execution on CodaLab (should ideally) happen in a
[docker](https://www.docker.com/) container, which provides a standardized
Linux environment that is lighterweight than a full virtual machine.

The current official docker image is `codalab/ubuntu`, which consists of
Ubuntu 14.04 plus some standard packages.  See the [CodaLab docker
registery](https://registry.hub.docker.com/u/codalab/ubuntu/).

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

    docker run -t -i codalab/ubuntu:1.8

Now, let us integrate docker into CodaLab.  First, we need to setup a job
scheduling system (that manages the deployment of runs on machines).  Note that
CodaLab itself doesn't do this, so that it can be easily integrated into
different systems.  An easy way to set this up is to use `q` from Percy Liang's `fig` package:

    git clone https://github.com/percyliang/fig
    # Add fig/bin/q to your $PATH
    q -mode master   # Run in a different terminal
    q -mode worker   # Run in a different terminal

Now, let us tell CodaLab to use `q` and run things in docker (these two things
are orthogonal choices).  Edit the `.codalab/config.json` as follows:

    "workers": {
        "q": {
            "verbose": 1,
            "docker_image": "codalab/ubuntu:1.8"
            "dispatch_command": "python $CODALAB_CLI/scripts/dispatch-q.py"
        }
    }

To test it out:

    cl work-manager -t q                 # Run in a different terminal
    cl run 'cat /proc/self/cgroup' -t    # Should eventually print out lines containing the string `docker`