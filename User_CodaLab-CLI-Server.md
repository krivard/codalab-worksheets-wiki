### Starting your own server

Setting up a CodaLab instance for you or your group, you can do the following
in another shell:

    cl server

By default, the server is running at `http://localhost:2800`.  You can change
this in `~/.codalab/config.json`.

For security reasons, the server is only accessible from localhost.  To make
the server accessible from anywhere, under "server" / "host" in
`~/.codalab/config.json`, change "localhost" to "".  In this case, you should
change the authentication from `MockAuthHandler` to `OAuthHandler` or else you
will have a bad security vulnerability.  Setting this requires have an OAuth
endpoint, which the CodaLab web server happens to provide (see the README
there).

Now we can connect to this server:

    cl work http://localhost:2800::

or

    cl alias localhost http://localhost:2800
    cl work localhost::

To switch back to the `local` instance, type:

    cl work local::

Note that when you are on `localhost`, you are accessing the same CodaLab
instance as when you are on `local`, but all requests go through the network,
which means that this CodaLab instance can be accessed from another machine.

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

If you already have data in SQLite, you can load it into MySQL as follows:

    sqlite3 ~/.codalab/bundle.db .dump > bundles.sqlite
    python scripts/sqlite_to_mysql.py < bundles.sqlite > bundles.mysql 
    mysql -u codalab -p codalab_bundles < bundles.mysql

Once you set up your database, run the following so that future migrations
start from the right place (this is important!):

    venv/bin/alembic stamp head

You can back up the contents of the database:

    mysqldump -u codalab -p codalab_bundles > bundles.mysql

## Authentication

If you want to make your server public, then you need to set up OAuth
authentication.  Follow the instructions in the [Linux Quickstart tutorial](Dev_CodaLab Linux Quickstart).