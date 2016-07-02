Follow these instructions to setup your own CodaLab server.

## Get the source code

Check out the
[codalab-worksheets](https://github.com/codalab/codalab-worksheets) repository
for the website and the [codalab-cli](https://github.com/codalab/codalab-cli)
repository for the bundle service, respectively:

    git clone https://github.com/codalab/codalab-worksheets
    git clone https://github.com/codalab/codalab-cli

In the following, `$HOME` will refer to the directory where `codalab-worksheets` and `codalab-cli` reside.

For reference, look at the automatic deployment script, which will not be run
here, but provides the commands needed to install everything:

    $HOME/codalab-worksheets/deployment/fabfile.py

## Installing basics

Make sure you have the dependencies (Python 2.7 and virtualenv). If you're
running Ubuntu 14.04, you can just install them:

    sudo apt-get install python2.7 python2.7-dev python-virtualenv

Install node:

    sudo apt-get install npm node-legacy

Install MySQL:

    sudo apt-get install mysql-server libmysqlclient-dev

[Install Docker](Installing-Docker).

Run the setup scripts to install the necessary packages:

    cd $HOME/codalab-worksheets
    ./setup.sh

    cd $HOME/codalab-cli
    ./setup.sh server

## Creating a MySQL database

Create a user in the `mysql -u root -p` prompt:

    CREATE USER 'codalab'@'localhost' IDENTIFIED BY '<password>';
    CREATE DATABASE codalab_bundles;
    GRANT ALL ON codalab_bundles.* TO 'codalab'@'localhost';

Then configure CodaLab to use the database:

    cd $HOME/codalab-cli
    codalab/bin/cl config server/engine_url mysql://codalab:<password>@localhost:3306/codalab_bundles

## Configuring email

To allow users to register and receive email from your CodaLab server, you
should specify where email will be sent from:

    cd $HOME/codalab-cli
    codalab/bin/cl config email/host <host, e.g., smtp.gmail.com>
    codalab/bin/cl config email/user <username>
    codalab/bin/cl config email/password <password>

Notifications/errors will be sent to an admin email address, which you can
specify as follows:

    codalab/bin/cl config admin-email <email>

## Starting Nginx

Nginx puts all the services behind one endpoint.  First we need to generate the
configuration file (this also generates a file for supervisord which is used in production,
but we won't use it here):

    cd $HOME/codalab-worksheets/codalab
    ./manage config_gen

### Ubuntu

    sudo apt-get install nginx
    sudo ln -sf $HOME/codalab-worksheets/codalab/config/generated/nginx.conf /etc/nginx/sites-enabled/codalab.conf
    sudo service nginx restart

### Mac using Homebrew

    brew install nginx
    sudo ln -sf $HOME/codalab-worksheets/codalab/config/generated/nginx.conf /usr/local/etc/nginx/servers/codalab.conf
    sudo nginx -s reload

### Mac using MacPorts

    sudo /opt/local/bin/port install nginx
    sudo ln -sf $HOME/codalab-worksheets/codalab/config/generated/nginx.conf /opt/local/etc/nginx/codalab.conf

Make Nginx use that file by editing `/opt/local/etc/nginx/nginx.conf` and adding the following into the `http` section:

    include /opt/local/etc/nginx/codalab.conf;

Restart:

    sudo /opt/local/bin/port unload nginx
    sudo /opt/local/bin/port load nginx

## Running the worker

Workers actually execute the commands to run bundles.  First, [install
docker](Installing-Docker).

To set up a worker that can run bundles from any user, first create the root
`codalab` user:

    cd $HOME/codalab-cli
    scripts/create-root-user.py

which will create the `codalab` user.

Then, to start a worker, do:

    cd $HOME
    codalab-cli/worker/worker.sh --server http://localhost:2900

This will prompt you for a username/password.  Put in your root `codalab` user
and its password.

Finally, you will need to start the bundle manager which schedules the bundles:

    cd $HOME/codalab-cli
    codalab/bin/cl bundle-manager

## Summary

To summarize, there are five processes to start to make up the full CodaLab system:

    cd $HOME/codalab-worksheets/codalab
    ./manage runserver 127.0.0.1:2700

    cd $HOME/codalab-cli
    codalab/bin/cl server
    codalab/bin/cl rest-server
    codalab/bin/cl bundle-manager
    codalab-cli/worker/worker.sh --server http://localhost:2900

Now, to use CodaLab, navigate to
[http://localhost:8000](http://localhost:8000).

To test it out the system, try running a job:

    cl run date -t

That's it!

## FAQ

1. What if the static files fail to load? You likely have permissions problems.
   Make sure that the user running nginx has permission to read the static
   directory in codalab-worksheets/codalab/apps/web/static. You will need to
   check that all the parent directories are readable all the way to /. That
   includes your home directory.
