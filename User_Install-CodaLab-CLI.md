While one can perform most things on the CodaLab web interface via the web terminal, a power-user might seek a command-line interface (CLI), which allows you to do additional things like copy bundles between CodaLab instances and use your own editor to edit worksheets.  You will also need the CLI to run your own local server.

## Installation

Skip this section if your administrator has already installed CodaLab for you.

1. Make sure you have the dependencies (Python 2.7 and virtualenv).  If you're running Ubuntu:

        sudo apt-get install python2.7 python2.7-dev python-virtualenv

2. Clone the CodaLab repository:

        git clone https://github.com/codalab/codalab-cli
        cd codalab-cli

3. Run the setup script (will install things into a Python virtual environment):

        ./setup.sh

4. Set your path to include CodaLab (add this line to your `.bashrc`):

        export PATH=$PATH:<path to codalab-cli>/codalab/bin

5. Optional: include some handy macros (add this line to your `.bashrc`):

        . <path to codalab-cli>/rc

Now you are ready to start using the CodaLab CLI!

## Usage

All commands start with `cl`.  Type the following to learn more:

    cl help