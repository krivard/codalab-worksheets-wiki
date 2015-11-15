Follow these instructions to setup an instance of CodaLab Worksheets on your local machine.

Check out the [codalab](https://github.com/codalab/codalab) repository for the website and the [codalab-cli](https://github.com/codalab/codalab-cli) repository for the bundle service:

    git clone https://github.com/codalab/codalab
    git clone https://github.com/codalab/codalab-cli

In the following, `$HOME` will refer to the directory where `codalab` and `codalab-cli` reside.

Run the following setup script to set up the bundle service.  It will ask you whether you want to set up MySQL and docker.

    cd $HOME/codalab-cli
    ./setup.sh

Now let us set up the website.  Install all the required Python packages:

    cd $HOME/codalab
    ./dev_setup.sh

Install the standard configuration file.  Edit DATABASES if want to use MySQL
instead of sqlite (need to create a MySQL database separately):

    cd $HOME/codalab/codalab
    cp codalab/settings/local_sample.py codalab/settings/local.py

Modify the following lines in `codalab/settings/local.py`:

    ENABLE_WORKSHEETS = True
    ENABLE_COMPETITIONS = False

Update the database schema and generate all the configuration files:

    cd $HOME/codalab/codalab
    ./manage syncdb --migrate
    ./manage config_gen

If you want to use CodaLab in offline mode, run the following to download
MathJax:

    cd $HOME/codalab/codalab
    ./manage prep_for_offline

and add this to your `codalab/settings/local.py` file:

    LOCAL_MATHJAX = True

Start the web server:

    cd $HOME/codalab/codalab
    ./runserver 0.0.0.0:8000

Create an account for `codalab` by navigating to `http://localhost:8000`,
clicking `Sign Up`.  Use any email address starting with
`codalab@`.  This account is the root account.
Now we can generate the OAuth credentials from the website to use in the bundle service
by running the following command and replacing `~/.codalab/config.json` with the output:

    cd $HOME/codalab/codalab
    ../venv/bin/python scripts/set-oauth-key.py ~/.codalab/config.json

Start the bundle server:

    cd $HOME/codalab/codalab
    ../../codalab-cli/codalab/bin/cl server

That is it!