Follow these instructions to setup an instance of CodaLab Worksheets on your local machine.

Check out the [codalab-worksheets](https://github.com/codalab/codalab-worksheets) repository for the website and the [codalab-cli](https://github.com/codalab/codalab-cli) repository for the bundle service:

    git clone https://github.com/codalab/codalab-worksheets
    git clone https://github.com/codalab/codalab-cli

In the following, `$HOME` will refer to the directory where `codalab-worksheets` and `codalab-cli` reside.

Run the following setup script to install the necessary packages and set up the bundle service:

    cd $HOME/codalab-cli
    ./setup.sh server

If you want to use MySQL, set up MySQL per the instructions below. Then, start the bundle server:

    cd $HOME/codalab-worksheets/codalab
    ../../codalab-cli/codalab/bin/cl server

Now let us set up the website.  Install all the required Python packages:

    cd $HOME/codalab-worksheets
    ./setup.sh

Next, install the standard configuration file at `~/.codalab/website-config.json`.
You can start with just an empty configuration file:

    echo "{}" > ~/.codalab/website-config.json

By default, the website will create and use a SQLite database at `$HOME/codalab-worksheets/codalab/codalab.sqlite3`.
If you want to use MySQL instead, add a "databases" field to your config file:
    
    {
        "database": {
            "ENGINE": "django.db.backends.mysql",
            "NAME": "YOUR DATABASE NAME",
            "USER": "YOUR MYSQL USER NAME",
            "PASSWORD": "YOUR PASSWORD HERE",
            "HOST": "YOUR MYSQL HOST NAME",
            "PORT": "YOUR MYSQL PORT (OR EMPTY FOR DEFAULT)"
        }
    }

> For more details about what you can put in your website config, look in `$HOME/codalab-worksheets/codalab/codalab/settings/__init__.py`.

Now update the database schema and generate all the configuration files:

    cd $HOME/codalab-worksheets/codalab
    ./manage syncdb --migrate
    ./manage config_gen
    ./manage set_site your_domain_name.com

Because we use LESS and [React](http://facebook.github.io/react/)––and JSX in particular––we have an extra build step, which is entirely automated through NPM. Look at the [README](https://github.com/codalab/codalab-worksheets/tree/develop/codalab/apps/web/README.md) for specifics, but concisely, you should install Node.js for your system and then do the following (it will compile JSX into JS and LESS into CSS):

    cd $HOME/codalab-worksheets/codalab/apps/web
    npm run build

You also need to install all of the third-party dependencies, which are managed by [Bower](https://bower.io):

    cd $HOME/codalab-worksheets/codalab/apps/web
    npm run bower

Start the web server:

    cd $HOME/codalab-worksheets/codalab
    ./manage runserver 0.0.0.0:8000

Create an account for `codalab` by navigating to `http://localhost:8000`,
clicking `Sign Up`.  Use any email address starting with
`codalab@`.  This account is the root account.
Now we can generate the OAuth credentials from the website to use in the bundle service
by running the following command and replacing `~/.codalab/config.json` with the output:

    cd $HOME/codalab-worksheets/codalab
    ./manage set_oauth_key ~/.codalab/config.json

Set the site name (for sending out sending out confirmation emails with the right links to verify login):

    ./manage set_site your_host_name.com



That is it!

## Using MySQL

By default, CodaLab is configured to use SQLite, and the database file is just a single
file in `~/.codalab`.  While this is a quick way to get started, SQLite is not a very
scalable solution (and also doesn't handle database migrations properly, so
don't put valuable stuff in a SQLite-backed database). Here are instructions to
set up MySQL:

Install the MySQL server.  On Ubuntu, run:

    sudo apt-get install mysql-server

Install the MySQL Python if it hasn't been done already:

    cd $HOME/codalab-cli
    venv/bin/pip install MySQL-python

Create a user in the `mysql -u root -p` prompt:

    CREATE USER 'codalab'@'localhost' IDENTIFIED BY '<password>';
    CREATE DATABASE codalab_bundles;
    GRANT ALL ON codalab_bundles.* TO 'codalab'@'localhost';

In the configuration file `.codalab/config.json`,
change `"class": "SQLiteModel"` to

    "class": "MySQLModel",
    "engine_url": "mysql://<username>:<password>@<host>:<port>/<database>",

For example:

    "engine_url": "mysql://codalab@localhost:3306/codalab_bundles",

#### Copying data from SQLite to MySQL

If you already have data in SQLite, you can load it into MySQL as follows:

    sqlite3 ~/.codalab/bundle.db .dump > bundles.sqlite
    python scripts/sqlite_to_mysql.py < bundles.sqlite > bundles.mysql 
    mysql -u codalab -p codalab_bundles < bundles.mysql

Once you set up your database, run the following so that future migrations
start from the right place (this is important!):

    venv/bin/alembic stamp head

You can back up the contents of the database:

    mysqldump -u codalab -p codalab_bundles > bundles.mysql

## Workers

Workers actually execute the commands to create run bundles.  First, we need to setup a job scheduling system (that manages the deployment of runs on machines).  The default one is `q` from Percy Liang's `fig` package, which can be found in `codalab-cli/scripts/q`.
Start the job scheduling system with one master and one worker:

    q -mode master   # Run in a different terminal
    q -mode worker   # Run in a different terminal

Second, [install docker](Installing-Docker), and tell CodaLab to use `q` and run things in docker (these two things are orthogonal choices).  Edit the `.codalab/config.json` as follows:

    "workers": {
        "q": {
            "verbose": 1,
            "docker_image": "codalab/ubuntu:1.9",
            "dispatch_command": "python $CODALAB_CLI/scripts/dispatch-q.py"
        }
    }

Third, you need to configure your machine so that you can ssh from the worker to the master and vice-versa without typing in your password (this is important!).  To do this, do `ssh-keygen` on the one machine and add the `.ssh/id_rsa.pub` line to `.ssh/authorized_keys` of the other machine.  This is needed because `q` relies on `rsync` to transfer files between the master and worker.

Note: the default requested memory of a job in `q` is 1.5 GB, so make sure your worker machine has at least that much free memory (so you probably need at least 2 GB total).  Otherwise, your jobs will just hang indefinitely.  To test out `q` by itself:

    q -add ls    # Create a job
    q -l -header # List the jobs
    q -w -header # List the workers

The job's status should go from `ready` to `done` (note that `ready` means `done` in CodaLab).

To test it out the worker system end-to-end:

    cl work-manager -t q                 # Run in a different terminal
    cl run 'cat /proc/self/cgroup' -t    # Should eventually print out lines containing the string `docker`