Follow these instructions to setup your own CodaLab server.

# Development / Local Deployment

This tutorial is primarily intended for people setting up a server on the same machine they intend to use for codalab tasks -- everything in this section is configured to use localhost. If you want to setup a server to be used remotely, the instructions are different in only a few places; we've compiled all the differences into a Production Deployment section at the end.


## Get the source code

Check out the
[codalab-worksheets](https://github.com/codalab/codalab-worksheets) repository
for the website and the [codalab-cli](https://github.com/codalab/codalab-cli)
repository for the bundle service, respectively:

    git clone https://github.com/codalab/codalab-worksheets
    git clone https://github.com/codalab/codalab-cli

In the following, `$HOME` will refer to the directory where `codalab-worksheets` and `codalab-cli` reside.

Configuration files will be stored in `$CODALAB_HOME`, which by default is `~/.codalab`.

## Install packages

Make sure you have the dependencies (Python 2.7 and virtualenv). If you're
running Ubuntu 14.04, you can just install them:

    sudo apt-get install -y python2.7 python2.7-dev python-virtualenv

Install node.js for the website:

    sudo apt-get install -y npm nodejs-legacy

Install MySQL for the backend:

    sudo apt-get install -y mysql-server libmysqlclient-dev

[Install Docker](Installing-Docker) so that you can run commands (instructions in the link).

Now, run the setup scripts for each repository to install the necessary
packages (in user space):

    (cd $HOME/codalab-worksheets && ./setup.sh)
    (cd $HOME/codalab-cli && ./setup.sh server)

## Configure the database

The `codalab-cli` repository uses a MySQL database to store all the bundle
information.

Type

    sudo mysql -u root -p

and at the MySQL prompt type the following to create a `codalab` user and a
`codalab_bundles` database:

    CREATE USER 'codalab'@'localhost' IDENTIFIED BY '<password>';
    CREATE DATABASE codalab_bundles;
    GRANT ALL ON codalab_bundles.* TO 'codalab'@'localhost';

Configure CodaLab to use the database:

    cd $HOME/codalab-cli
    codalab/bin/cl config server/engine_url mysql://codalab:<password>@localhost:3306/codalab_bundles

## Configure email service

To allow users to register and receive email from your CodaLab server, you
should specify where email will be sent from:

    cd $HOME/codalab-cli
    codalab/bin/cl config email/host <host>
    codalab/bin/cl config email/user <username>
    codalab/bin/cl config email/password <password>

One option is to [SendGrid](https://sendgrid.com).  Google doesn't let you use
gmail in this way, unfortunately.  If you don't do this, then the emails will
be printed to the console, and you (as the administrator) will have to relay
that information to users.

If you have access to an SMTP server that doesn't require authentication (this is common in academic environments), just specify the host. **NB: Authless SMTP requires [this patch](https://github.com/krivard/codalab-cli/commit/d4899bca89f4c1790d61f7dacef97827e22932e9).**

Separately, notifications/errors will be sent to an admin email address, which
you can specify as follows:

    codalab/bin/cl config admin-email <email>

## Configure the workers

Workers actually execute the commands to run bundles.  First, [install
docker](Installing-Docker).

Then create the root `codalab` user as follows, who can run bundles on behalf
of all users:

    cd $HOME/codalab-cli
    scripts/create-root-user.py

Create a file `$CODALAB_HOME/root.password` with `codalab` on the first line
and the password on the second:

    codalab
    <password>

Make sure this file is only accessible to you:

    chmod 600 $CODALAB_HOME/root.password

If you want your workers to support jobs that use NVIDIA GPUs (e.g. CUDA, cuDNN, etc.), you should install the `nvidia-docker` plugin on your machine as well, and make \
sure that `nvidia-docker-plugin` is listening at `localhost:3476`. Instructions for installation are found here:
https://github.com/NVIDIA/nvidia-docker

## Start NGINX

We use NGINX to put all CodaLab servers (website, bundle service) behind one
endpoint.

First we need to generate the base `nginx` configuration file:

    cd $HOME/codalab-worksheets/codalab
    ./manage config_gen

Follow the instructions below corresponding to your installation, which will guide you through
how to install NGINX and to point it at the `nginx` conf file that CodaLab generated. **Make sure
that you install at least version 1.7.x!**

#### Ubuntu
By default, `apt-get` on Ubuntu may install a version of NGINX older than 0.7.x. Follow these instructions to ensure that you have the latest stable version ([source](h\
ttps://www.nginx.com/resources/wiki/start/topics/tutorials/install/#ubuntu-ppa)).

    sudo -s
    nginx=stable # use nginx=development for latest development version
    add-apt-repository ppa:nginx/$nginx
    apt-get update
    apt-get install nginx
    cd $HOME/codalab-worksheets/codalab
    sudo ln -sf $PWD/config/generated/nginx /etc/nginx/sites-available/codalab.conf
    sudo ln -s /etc/nginx/sites-available/codalab.conf /etc/nginx/sites-enabled/

Restart NGINX:

    sudo nginx -s reload


#### Mac using Homebrew

    brew install nginx
    cd $HOME/codalab-worksheets/codalab
    sudo ln -sf $PWD/config/generated/nginx /usr/local/etc/nginx/servers/codalab.conf

Make NGINX use that file by editing `/etc/nginx/nginx.conf` and adding the following into the `http` section:

    include /etc/nginx/servers/codalab.conf;

Restart NGINX:

    sudo nginx -s reload

#### Mac using MacPorts

    sudo /opt/local/bin/port install nginx
    cd $HOME/codalab-worksheets/codalab
    sudo ln -sf $PWD/config/generated/nginx /opt/local/etc/nginx/codalab.conf

Make NGINX use that file by editing `/opt/local/etc/nginx/nginx.conf` and adding the following into the `http` section:

    include /opt/local/etc/nginx/codalab.conf;
    
Restart:

    sudo /opt/local/bin/port unload nginx
    sudo /opt/local/bin/port load nginx

## Start CodaLab services

Now, having configured everything, we are ready to launch CodaLab.  The CodaLab
deployment actually consists of six processes.

_**Recommendation**_: Start each of the processes below in a separate window inside
[GNU Screen](https://www.gnu.org/software/screen/).  Press Ctrl+C to kill
any of the processes (though you might want to do this with some care).

### Step 1: Start the website

This is the Django app that serves HTML and Javascript, but relies on the
backend for data.

    cd $HOME/codalab-worksheets/codalab
    ./manage runserver 127.0.0.1:2700


### Step 2: Start the API service

The worksheets API service provides the `/rest` endpoints which power the website, the
CLI, and any third-party applications.

    cd $HOME/codalab-cli
    codalab/bin/cl server


### Step 3: Start the the bundle manager

The bundle manager checks for bundles that need to be run (in the `staged`
state) and schedules them onto workers:

    cd $HOME/codalab-cli
    codalab/bin/cl bundle-manager

### Step 4: Start a worker

You can run

    cd $HOME
    codalab-cli/worker/worker.sh --server http://localhost:2900 --password $CODALAB_HOME/root.password

### Step 5: Start the monitoring script (optional)

This script backs up the database periodically and does basic sanity checks
(tries to run jobs) to make sure that everything is behaving properly:

    cd $HOME
    codalab-cli/monitor.py

## Create initial worksheets

Create the default worksheets and populate with initial content:

    cd $HOME/codalab-cli
    codalab/bin/cl new home
    codalab/bin/cl new dashboard

## Test it out

Navigate to [http://localhost:80](http://localhost:80).  Try signing up for
an account, creating some worksheets and bundles.

Try out the CLI:

    cl run date -t

That's it!

# Production/Remote Deployment

If you want to deploy on a server that other people can use, the local deployment instructions serve as a template (so read them!) but the following modifications are necessary:

## Install packages: Docker

By default, Docker keeps its metadata in `/var`. If your `/var` partition is small, you will want to store this metadata somewhere else. To do this, we're going to stop Docker, move its files around, then set up symlinks so everything appears to be where Docker expects it.

    sudo service docker stop
    sudo mv /var/lib/docker </somewhere/else>
    sudo ln -sf </somewhere/else> /var/lib/docker
    sudo service docker start

If you forget to stop Docker before you do this, you may see error messages about files or sockets in use. If it is an aufs problem, rebooting will clear the lock, and you can proceed.

## Configure the database

If you installed MySQL for the express purpose of running CodaLab, you do not need to make any changes: CodaLab does not access the DB remotely.

There are two options for using an existing MySQL installation.

### Option 1: Existing MySQL on the same machine as CodaLab

This is only an issue if your MySQL is configured to use a non-standard port. To find out if your MySQL is configured this way, open `/etc/mysql/my.cnf` and look for the following line:

    port               = 3306       # your port may differ

If your `my.cnf` looks like the above, you're all set. If you have a different port number, record it in your CodaLab configuration like so:

    codalab/bin/cl config server/engine_url mysql://codalab:<password>@localhost:<port>/codalab_bundles
    
Note that this should still be localhost; we don't need or want CodaLab to try to connect remotely to a MySQL server running on the same machine.

### Option 2: Existing MySQL on a different machine

This is a little tricker because we need a more permissive MySQL login for the codalab user.

First, you will set up the `codalab` MySQL user exactly as in the local deployment section, and then add the following line:

    > GRANT ALL ON *.* to 'codalab'@'%' IDENTIFIED BY '<password>'; 

using the same password as you used for the localhost `GRANT` statement. You need both lines for this to work.

Second, configure CodaLab to use the remote MySQL server like so:

    codalab/bin/cl config server/engine_url mysql://codalab:<password>@<host>:<port>/codalab_bundles

using the host and port of your MySQL server.

## Additional CLI configuration

The settings below get used by the API server and the monitoring script, and when creating initial worksheets.

    cd $HOME/codalab-cli
    codalab/bin/cl config server/rest_host <host>
    codalab/bin/cl config server/rest_port 2900
    codalab/bin/cl config server/instance_name <host>
    codalab/bin/cl config aliases/localhost http://<host>:2900
    codalab/bin/cl config cli/default_address http://<host>:2900

## Start NGINX

The default `nginx` config file in `$HOME/codalab-worksheets/codalab/config/generated` uses "localhost" everywhere. Replace these with your hostname, then reload the config:

    # Ubuntu & Homebrew
    sudo nginx -s reload
    
    # MacPorts
    sudo /opt/local/bin/port unload nginx
    sudo /opt/local/bin/port load nginx
    
## Starting CodaLab services

Everything is the same with one exception:

### Step 1: Start the website

    cd $HOME/codalab-worksheets/codalab
    ./manage runserver <host>:2700


