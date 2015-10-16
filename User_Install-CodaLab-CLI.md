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

Now you are ready to start using CodaLab!
