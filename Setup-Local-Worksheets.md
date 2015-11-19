Follow these instructions to setup an instance of CodaLab Worksheets on your local machine.

Check out the [codalab](https://github.com/codalab/codalab) repository for the website and the [codalab-cli](https://github.com/codalab/codalab-cli) repository for the bundle service:

    git clone https://github.com/codalab/codalab
    git clone https://github.com/codalab/codalab-cli

In the following, `$HOME` will refer to the directory where `codalab` and `codalab-cli` reside.

Run the following setup script to install the necessary packages and set up the bundle service:

    cd $HOME/codalab-cli
    ./setup.sh

Now let us set up the website.  Install all the required Python packages:

    cd $HOME/codalab
    ./dev_setup.sh

Next, install the standard configuration file.  Edit DATABASES if want to use MySQL
instead of SQLite (need to create a MySQL database separately):

    cd $HOME/codalab/codalab
    cp codalab/settings/local_sample.py codalab/settings/local.py

Modify the following lines in `codalab/settings/local.py`:

    ENABLE_WORKSHEETS = True
    ENABLE_COMPETITIONS = False

Update the database schema and generate all the configuration files:

    cd $HOME/codalab/codalab
    ./manage syncdb --migrate
    ./manage config_gen
    ./manage set_site your_domain_name.com

If you want to use CodaLab in offline mode, run the following to download
MathJax:

    cd $HOME/codalab/codalab
    ./manage prep_for_offline

and add this to your `codalab/settings/local.py` file:

    LOCAL_MATHJAX = True
    LOCAL_ACE_EDITOR = True

Start the web server:

    cd $HOME/codalab/codalab
    ./runserver 0.0.0.0:8000

Create an account for `codalab` by navigating to `http://localhost:8000`,
clicking `Sign Up`.  Use any email address starting with
`codalab@`.  This account is the root account.
Now we can generate the OAuth credentials from the website to use in the bundle service
by running the following command and replacing `~/.codalab/config.json` with the output:

    cd $HOME/codalab/codalab
    ./manage set_oauth_key ~/.codalab/config.json

Set the site name (for sending out sending out confirmation emails with the right links to verify login):

    ./manage set_site your_host_name.com

Start the bundle server:

    cd $HOME/codalab/codalab
    ../../codalab-cli/codalab/bin/cl server

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

    venv/bin/pip install MySQL-python

Create a user in the `mysql -u root -p` prompt:

    CREATE USER '<username>'@'localhost' IDENTIFIED BY '<password>';
    CREATE DATABASE codalab_bundles;
    GRANT ALL ON codalab_bundles.* TO 'codalab'@'localhost';

In the configuration file `.codalab/config.json`,
change `"class": "SQLiteModel"` to

    "class": "MySQLModel",
    "engine_url": "mysql://<username>:<password>@<host>:<port>/<database>",

For example:

    "engine_url": "mysql://codalab@localhost:3306/codalab_bundles",

### Copying data from SQLite to MySQL

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
            "docker_image": "codalab/ubuntu:1.9"
            "dispatch_command": "python $CODALAB_CLI/scripts/dispatch-q.py"
        }
    }

To test it out:

    cl work-manager -t q                 # Run in a different terminal
    cl run 'cat /proc/self/cgroup' -t    # Should eventually print out lines containing the string `docker`