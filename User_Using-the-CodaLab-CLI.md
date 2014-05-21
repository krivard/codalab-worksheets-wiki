# Using the CodaLab Bundle Service and CLI
The CodaLab Bundle Service and CLI (Command-Line Interface) provide a backend for creating, uploading and executing worksheet bundles. This guide shows you how to configure and run the CodaLab CLI on an Ubuntu 12.4 system.

## Overview
The CodaLab CLI is a client from which you can run commands to create and manage worksheets and bundles on a remote CodaLab server. The bundle service is installed on the server by default. 

## Contents
- [Install prerequisites](#install-prerequisites)
- [Get the source code](#get-the-source-code)
- [Install the CodaLab CLI](#install-the-codalab-cli)
- [Configure a remote client](#configure-a-remote-client)
- [Use the CLI tool](#use-the-cli-tool)

## Install prerequisites
- Python (Python 2.7.3 is preinstalled on most Ubuntu distributions. Open a terminal window and type `python` at the prompt to verify whether this is the case on your system.) If Python is not installed, you will need to install it.
- Virtualenv `$ sudo apt-get install python-virtualenv`

**Note:** After installing the prerequisites, be sure to restart the terminal window so that the changes take effect.

## Get the source code
In this step you will clone the source code to your local computer, set up a Python virtual environment, install some required packages and define a PATH.

1. Install the CodaLab CLI.

    `pip install codalab-cli`

1. Open a terminal window.
1. Navigate to the `codalab-cli` directory.
1. Create a virtual environment directory.

    `virtualenv codalab_env --no-site-packages`
    
1. Install the sqlalchemy package:

    `codalab_env/bin/pip-2.7 install sqlalchemy`

1. Navigate to the `codalab-cli` directory.

1. Set up the PATH:

    `export PATH=$PATH:$PWD/codalab/bin`

1. Activate the virtual environment.
pip 
    `source codalab_env/bin/activate`

## Install the CodaLab CLI
Follow these steps to install the CodaLab CLI to your local computer.

1. Open a terminal and navigate to the `codalab-cli` directory.
1. Activate the virtual environment.

    `source codalab_env/bin/activate`

1. Use the Pip `search` command to verify that the package is accessible.

    `pip search codalab`

    This should display a short description in the console.

1. Run the following command to install the CLI.

    `pip install codalab-cli`

1. Restart the terminal so that the changes take effect.

### Make sure the tool works
Before proceeding, it is helpful to verify that everything is set up properly on your local computer.
1. Activate the virtual environment.

    source codalab_env/bin/activate

1. Run the following command: `cl`.

   You should see a list of commands. If not, you will need to revisit the previous steps to ensure that everything is set up correctly.

## Configure a remote client
In the following steps, you will create an alias file to point the CodaLab CLI tool to a remote CodaLab server. 

### Add a remote server alias
1. Change to the bin directory.

    `cd codalab-cli/codalab/bin`

1. Create a new text file named `cli`. This functions as an alias.
1. Paste the following into the file:

    ```
    export CODALAB=`dirname $0`/../..

    export PYTHONPATH=$CODALAB

    exec $CODALAB/codalab_env/bin/python $CODALAB/codalab/bin/codalab_client.py $CODALAB/codalab/config/remote_client_config.json "$@"

    ```

1. Change the permissions to make the **cli** file executable by the user.

    `chmod u+x cli`

### Edit remote_client_config.json
1. Change to the config directory.

    `cd codalab-cli/codalab/config`

1. Open `remote_client_config.json`.
1. Update the `"address"` line with the URL for the remote server:

    `"address": "https://[YOUR SERVER BASE URL]/bundleservice"`

## Use the CLI tool
To run the CLI tool against the alias you have defined, run `cli`. Note that this command is just the name of the alias file you created. You could create another alias file named `ABC` pointing to a different remote server.

### Populate the CodaLab service with sample content
There are two collections of sample programs and datasets that you can use to try out the CodaLab CLI tool (one for Machine Learning, one for Natural Language Processing). Run these shell scripts to download the content to your local computer, and then upload them into your local CodaLab instance.

**For Machine Learning:**
    
    (cd examples/weka && ./download.sh)
    (cd examples/weka && ./upload.sh)
    (cd examples/uci_arff && ./download.sh)
    (cd examples/uci_arff && ./upload.sh)
    (cd examples && ./basic_ml.sh)  # Run basic ML pipeline

**For Natural Language Processing:**

    (cd examples/stanford_corenlp && ./download.sh)
    (cd examples/stanford_corenlp && ./upload.sh)
    (cd examples/lewis_carroll_poems && ./upload.sh)
    cl run stanford_corenlp lewis_carroll_poems 'program/run input output' --auto
     
### View worksheets on the remote server
1. Open a browser and go to the worksheets page of your CodaLab server.
1. In the terminal window, type the following command:

    `cli list -w`

    You should see a list of worksheets that matches the list you see on your server.

### Create a new worksheet
1. Run the following command to create a new worksheet.

    `cli new new_worksheet`

    The new worksheet is given focus in the terminal window.


### Edit a worksheet
1. Enter the following command to set the scope to a specific worksheet:

    `cli work new_worksheet`

    Now each operation will affect the worksheet you selected.

1. Enter the following command to edit the selected worksheet:

    `cli edit -w`

1. Make your edits in the terminal window, and hit ESC to exit editing mode.

## More commands
- Delete a worksheet: `cli rm -x <name_of_worksheet>`
- Stop working on a worksheet: `cli work –x`
- Start working on new worksheet: `cli new <name_of_ws>`
- Start working on existing worksheet: `cli work <name_of_ws>`
- Add a bundle to a worksheet (after setting scope to the worksheet): `cli add `
