You can perform basic things in the CodaLab web terminal, but the command-line
interface (CLI) gives you the ability to use CodaLab more effectively.  For
example, you can copy bundles between CodaLab servers, use your own editor to
edit worksheets, and write scripts that interact with CodaLab.

## Installation

1. Make sure you have the dependencies (Python 2.7 and virtualenv).  If you're
running Ubuntu 14.04, you can just install them:

        sudo apt-get install python2.7 python2.7-dev python-virtualenv

2. Clone the CodaLab repository:

        git clone https://github.com/codalab/codalab-cli
        cd codalab-cli

3. Run the setup script (this will install things into a Python virtual
environment in the `codalab-cli/venv` directory):

        ./setup.sh client

4. Set your path to include the CodaLab CLI (add this line to your `.bashrc`):

        export PATH=$PATH:<path to codalab-cli>/codalab/bin

5. Include some handy macros (add this line to your `.bashrc`):

        . <path to codalab-cli>/rc

6. To complete the configuration, type the following:

        cl

  This will bring up an interactive prompt and ask you which CodaLab server you
  want to connect to (the default is the main one, `worksheets.codalab.org`).
  You should create an account through the CodaLab website if you haven't done
  so already.

Now you are ready to start using the CodaLab CLI!

## 5-minute Quick Start

After having installed the CLI, let's upload a program, a dataset, and perform
a run to see how it works.

### Step 0: Orienting yourself

At any point in time, `cl` has a current CodaLab server and a current worksheet
(think of it like a current working directory).  This state is stored in
`~/.codalab/state.json`.

To see where you're pointed to, type:

    cl work

Most likely, you'll see something like:

    Currently on worksheet https://worksheets.codalab.org/bundleservice::home-pliang(0x2a0a3904a3e04840823aa9c7dd44f7e1).

Here, `https://worksheets.codalab.org/bundleservice` is the CodaLab server and
`home-pliang` is the worksheet (this acts like a home directory).

You can switch to the main CodaLab server by typing any of the following
equivalent commands:

    cl work main::
    cl work main::home-pliang
    cl work https://worksheets.codalab.org/bundleservice::home-pliang

### Step 1: Uploading bundles

Create a file on your local computer called `a.txt` with the following contents:

    foo
    bar
    baz

Create another file called `sort.py` with the following contents:

    import sys
    for line in sorted(sys.stdin.readlines()):
        print line,

To upload local files `a.txt` and `sort.py`:

    cl upload a.txt
    cl upload sort.py

To edit the metadata (to change the name, description, etc.):

    cl edit a.txt

To list the bundles you've uploaded, type:

    cl ls

You can see the metadata of a bundle:

    cl info -v a.txt

Note that while `a.txt` and `sort.py` are dataset and programs, respectively,
from CodaLab's perspective, these are just bundles (research assets).

To get more information about a command:

    cl upload --help
    cl edit --help
    ...

### Step 2: Run a command

Having uploaded some bundles, let's run some commands.  CodaLab allows you to
run arbitrary shell commands, and each command also creates a bundle to
encapsulate the computation.

To create our first run bundle, type:

    cl run sort.py:sort.py input:a.txt 'python sort.py < input' -n sort-run

This should append a new bundle named `sort-run` to the current worksheet
(verify this by typing `cl ls` and see the state go from `created` to `staged`
to `running` to `ready`).

Let's study what happened here.  The first two arguments of `cl run` specify the
dependencies and the third is the command, which is run in the presence of the
dependencies.  In this specific example, it is as if you ran the command in the
following environment:

    $ ls
    sort.py       [contains contents of bundle sort.py]
    input         [contains contents of bundle a.txt]
    $ python sort.py < input

CodaLab captures all the files and directories that are written to the current
directory, as well as stdout and stderr.  These form the contents of the
newly-created run bundle.  When the command terminates, the contents of the
bundle become immutable.

To look at the output of the run (you can do this while the run is in progress):

    cl cat sort-run         # Lists the files in the run bundle
    cl cat sort-run/stdout  # Shows the stdout (which CodaLab redirects to a file)
    cl info -v sort-run     # Shows metadata and a preview of the contents

You should treat the current worksheet as your current directory, where you're
running commands that depend on the existing bundles and generate new ones.
There are two differences:

1. You must be explicit about dependencies.  CodaLab will run your command only
in the presence of the dependencies you explicitly specify.

2. When your command outputs to the current directory, these files/directories
end up in the run bundle.  Therefore subsequent commands must specify the run
bundle to refer to them.  For example:

        cl run 'echo hello > message' -n run1
        cl run :run1/message 'cat message'     # right
        cl run :message      'cat message'     # wrong

Note that you can start off multiple runs in parallel, and even ones that
depend on previous runs that haven't yet finished.  Since CodaLab knows about
dependencies, it will wait for all the dependencies of a run to finish before
starting the run.

### Step 3: Present your results

So far, we have been typing `cl ls` to see what's in our worksheet.  To get a nicer view,
we can type the following (or go to the web interface):

    cl print

So far, your worksheet just contain a default table with one bundle per row.
But we can customize this view to better document and present our results.  To
edit the worksheet, type:

    cl wedit

You will open up your favorite editor, where you can freely edit the worksheet
using [CodaLab
markdown](https://github.com/codalab/codalab/wiki/User_Worksheet-Markdown).
CodaLab markdown is an extension of markdown that allows you to interleave
bundles and formatting directives.

For example, you might edit your worksheet to the following (your worksheet
will show different UUIDs):

    This is my **first** [CodaLab worksheet](https://worksheets.codalab.org).
    I uploaded some bundles:
    [dataset a.txt]{0x34a1fa62acc840ec96da98f17dbddf66}
    [dataset sort.py]{0xf9fc733b19894eb2a97f6b47f35d7ea0}
    Here's my first CodaLab run:
    % display table name command /stdout time
    [run sort-run -- :sort.py,input:a.txt : python sort.py < input]{0x08908cc6cb594b9394ed7ba6a0bd25f6}

The directive `% display table ...` tells CodaLab to render the bundle as a table
with certain columns.  For example, the `/stdout` column tells CodaLab to display
the contents of the `stdout` file inside the bundle.  This custom formatting
is extremely useful if you are monitoring multiple runs, and you want to print
out various metrics such as time, accuracy, number of iterations, etc.

Remember that worksheets are just views on the underlying bundle graph, and the
lines that look like `[run sort-run ...]` are just pointers to a bundle.
Therefore, you can re-order, remove, duplicate the bundles in the worksheet,
and even move/copy bundles across worksheets as easily as text editing.

Note that deleting a reference to a bundle does not actually delete the bundle.
To delete an actual bundle, type the following command:

    cl rm sort.py

See the [CodaLab markdown
documentation](https://github.com/codalab/codalab/wiki/User_Worksheet-Markdown)
for more information about the formatting.

### Summary

So there you have it: Upload code and data as bundles.  Run commands to
preprocess the data, run algorithms with different settings, using CodaLab to
manage your runs.  Use CodaLab markdown to create worksheets that document your
experiments, either for a private research log or a public executable paper.
There are many possibilities!

For more information:

    cl help     # Print out the list of all commands
    cl help rm  # Print out usage for a particular command
