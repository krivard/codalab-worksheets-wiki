Assume the [codalab-cli repository](https://github.com/codalab/codalab-cli) has
been checked out at the same level as this directory and is called
`../codalab-cli`.

Install all the required Python packages:

    ./dev_setup.sh

Install the standard configuration file.  Edit DATABASES if want to use MySQL
instead of sqlite (need to create a MySQL database separately):

    cp codalab/codalab/settings/local_sample.py codalab/codalab/settings/local.py

Update the database schema:

    cd codalab
    ./manage syncdb --migrate
    ./manage config_gen

If you want to use CodaLab in offline mode, run the following to download
MathJax:

    ./manage prep_for_offline

and add this to your `codalab/codalab/settings/local.py` file:

    LOCAL_MATHJAX = True

Start the web server:

    cd codalab
    ./runserver 0.0.0.0:8000

Create an account for `codalab` by navigating to `http://localhost:8000`,
clicking `Sign In`, and Sign Up.  Use any email address starting with
`codalab@`.  This account is just used so we can run the following script:

    source venv/bin/activate
    cd codalab
    python scripts/sample_cl_server_config.py

This script should print out a fragment of a JSON file with the appropriate
keys, which should be added to the codalab-cli config file (usually
`~/.codalab/config.json`).  This allows the bundle service to authenticate
against the website.  The JSON file looks something like this:

    "auth": {
        "address": "http://localhost:8000",
        "app_id": "...",
        "app_key": "...",
        "class": "OAuthHandler"
    },

Then start the bundle server:

    ../codalab-cli/codalab/bin/cl server

That is it!