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

## Installing packages

Make sure you have the dependencies (Python 2.7 and virtualenv). If you're
running Ubuntu 14.04, you can just install them:

    sudo apt-get install python2.7 python2.7-dev python-virtualenv

Install node.js for the website:

    sudo apt-get install npm node-legacy

Install MySQL for the backend:

    sudo apt-get install mysql-server libmysqlclient-dev

[Install Docker](Installing-Docker) so that you can run commands.

Now, run the setup scripts for each repository to install the necessary
packages (in user space):

    cd $HOME/codalab-worksheets
    ./setup.sh

    cd $HOME/codalab-cli
    ./setup.sh server

## Configuring MySQL

The `codalab-cli` repository uses a MySQL database to store all the bundle
information.

Type 

    mysql -u root -p

and at the MySQL prompt type the following to create a `codalab` user and a
`codalab_bundles` database:

    CREATE USER 'codalab'@'localhost' IDENTIFIED BY '<password>';
    CREATE DATABASE codalab_bundles;
    GRANT ALL ON codalab_bundles.* TO 'codalab'@'localhost';

Finally, configure CodaLab to use the database:

    cd $HOME/codalab-cli
    codalab/bin/cl config server/engine_url mysql://codalab:<password>@localhost:3306/codalab_bundles

## Configuring email

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

## Configuring the worker

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

## Starting Nginx

We use NGINX to put all CodaLab servers (website, bundle service) behind one
endpoint.

First we need to generate the `nginx.conf` file (this also generates a
`supervisord.conf` file which is used in production, but we won't use it here):

    cd $HOME/codalab-worksheets/codalab
    ./manage config_gen

Follow the instructions corresponding to your installation, which installs
NGINX and points it to the `nginx.conf` file that CodaLab generated.

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

Make NGINX use that file by editing `/opt/local/etc/nginx/nginx.conf` and adding the following into the `http` section:

    include /opt/local/etc/nginx/codalab.conf;

Restart:

    sudo /opt/local/bin/port unload nginx
    sudo /opt/local/bin/port load nginx

## Starting CodaLab

Now, having configured everything, we are ready to launch CodaLab.  The CodaLab
deployment actually consists of six processes.

Recommendation: start each of the processes below in a separate window inside
[GNU Screen](https://www.gnu.org/software/screen/).  Press Ctrl+C to kill
any of the processes (though you might want to do this with some care).

### Step 1: start the website

This is the Django app that serves HTML and Javascript, but relies on the
backend for data.

    cd $HOME/codalab-worksheets/codalab
    ./manage runserver 127.0.0.1:2700

### Step 2: start the bundle service (XMLRPC server)

This is the bundle service that provides access to worksheets and bundles (and
is soon to be deprecated):

    cd $HOME/codalab-cli
    codalab/bin/cl server

### Step 3: start the bundle service (REST server)

The bundle service provides the `/rest` endpoints which powers the website, the
CLI, and any third-party applications.

    cd $HOME/codalab-cli
    codalab/bin/cl rest-server

### Step 4: start the the bundle manager

The bundle manager checks for bundles that need to be run (in the `staged`
state) and schedules them onto workers:

    cd $HOME/codalab-cli
    codalab/bin/cl bundle-manager

### Step 5: start a worker

You can run 

    cd $HOME
    codalab-cli/worker/worker.sh --server http://localhost:2900 --password $CODALAB_HOME/root.password

### Step 6: start the monitoring script (optional)

This script backs up the database periodically and does basic sanity checks
(tries to run jobs) to make sure that everything is behaving properly:

    ./monitor.py

### Test it out

Navigate to [http://localhost:8000](http://localhost:8000).  Try signing up for
an account, creating some worksheets and bundles.

Try out the CLI:

    cl run date -t

That's it!
