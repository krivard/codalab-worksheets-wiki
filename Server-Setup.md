Follow these instructions to setup your own CodaLab server.

## Get the source code

Check out the
[codalab-worksheets](https://github.com/codalab/codalab-worksheets) repository
for the website and the [codalab-cli](https://github.com/codalab/codalab-cli)
repository for the bundle service, respectively:

    git clone https://github.com/codalab/codalab-worksheets
    git clone https://github.com/codalab/codalab-cli

In the following, `$HOME` will refer to the directory where `codalab-worksheets` and `codalab-cli` reside.

Configuration files will be stored in `$CODALAB_HOME`, which by default is `~/.codalab`.

For reference, look at the automatic deployment script, which we will not be run
here, but provides the commands needed to install everything:

    $HOME/codalab-worksheets/deployment/fabfile.py

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

## Start NGINX

We use NGINX to put all CodaLab servers (website, bundle service) behind one
endpoint.

First we need to generate the `nginx.conf` file (this also generates a
`supervisord.conf` file which is used in production, but we won't use it here):

    cd $HOME/codalab-worksheets/codalab
    ./manage config_gen

Follow the instructions below corresponding to your installation, which will guide you through
how to install NGINX and to point it at the `nginx.conf` file that CodaLab generated. **Make sure
that you install at least version 1.7.x!**

#### Ubuntu 
By default, `apt-get` on Ubuntu may install a version of NGINX older than 0.7.x. Follow these instructions to ensure that you have the latest stable version ([source](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/#ubuntu-ppa)).

    sudo -s
    nginx=stable # use nginx=development for latest development version
    add-apt-repository ppa:nginx/$nginx
    apt-get update
    apt-get install nginx


#### Mac using Homebrew

    brew install nginx
    cd $HOME/codalab-worksheets/codalab
    sudo ln -sf $PWD/config/generated/nginx.conf /usr/local/etc/nginx/servers/codalab.conf
    sudo nginx -s reload

#### Mac using MacPorts

    sudo /opt/local/bin/port install nginx
    cd $HOME/codalab-worksheets/codalab
    sudo ln -sf $PWD/config/generated/nginx.conf /opt/local/etc/nginx/codalab.conf

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

Navigate to [http://localhost:8000](http://localhost:8000).  Try signing up for
an account, creating some worksheets and bundles.

Try out the CLI:

    cl run date -t

That's it!
