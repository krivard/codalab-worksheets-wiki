After signing up for a CodaLab account and signing in, click on `My Home` (your
home worksheet, which is like your home directory).  Let's do a simple sorting
example.

### Step 1: Uploading bundles

Create a file on your local computer called `a.txt` with the following contents:

    foo
    bar
    baz

Create another file called `sort.py` with the following contents:

    import sys
    for line in sorted(sys.stdin.readlines()):
        print line,

Click on `Upload Bundle` on the right panel to popup a file browser to select
`a.txt` (if you want to upload a directory, you need to zip up the directory
and upload the zip file).  Do the same with `sort.py`.

Once each bundle is uploaded, it is appended to the current worksheet, and
it shows up as a row in a table (as the default view).  The bundle has a
32-character **UUID**, a globally unique identifier, which can be used to refer
to this particular bundle; its contents cannot be changed.  You can, however,
edit its metadata (name, description, etc.) by clicking on the right panel.

Note that while `a.txt` and `sort.py` are dataset and programs, respectively,
from CodaLab's perspective, these are just bundles (research assets).

### Step 2: Run a command

Having uploaded some bundles, let's run some commands.  CodaLab allows you to
run arbitrary shell commands, and each command also creates a bundle to
encapsulate the computation.

To create our first run bundle, click `New Run`.  Select `sort.py` and `a.txt`
(Step 1), and enter the following command (Step 2):

    python sort.py < input

This should append a new bundle named `sort-run` to the current worksheet.  If
you wait a few seconds, the output of the bundle will be shown on the right
panel.

CodaLab just ran your command in the following environment:

    $ ls
    sort.py       [contains contents of bundle sort.py]
    input         [contains contents of bundle a.txt]
    $ python sort.py < input

CodaLab captures all the files and directories that are written to the current
directory, as well as stdout and stderr.  These form the contents of the
newly-created run bundle.  When the command terminates, the contents of the
bundle become immutable.

The actual execution happens inside a [docker
container](https://www.docker.com) on a separate worker machine, which allows
you to specify a desired Linux environment with the requisite libraries /
software packages.

By default, the container is run using our custom [Ubuntu Linux 14.04
image](https://registry.hub.docker.com/u/codalab/ubuntu/) that includes a few
standard libraries, but you can use images created by others or [create your
own](Creating-Docker-Images), specifying the image to use with the
`--request-docker-image` flag.

You should treat the current worksheet as your current directory, where you're
running commands that depend on the existing bundles and generate new ones.
However, you must be explicit about dependencies!  CodaLab will run your
command only in the presence of the dependencies you explicitly specify.

Note that you can start off multiple runs in parallel, and even ones that
depend on previous runs that haven't yet finished.  Since CodaLab knows about
dependencies, it will wait for all the dependencies of a run to finish before
starting the run.

### Step 3: Present your results

So far, your worksheet just contain a default table with one bundle per row.
But we can customize this view to better document and present our results.  To
edit the worksheet, click `Edit Source` button (or hitting `e`) or in the CLI, type:

    cl wedit

You will be taken to a screen with a plain-text interface, where you can freely edit
the worksheet using [CodaLab markdown](https://github.com/codalab/codalab/wiki/User_Worksheet-Markdown).
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

If you prefer working from the shell, check out the [command-line interface
(CLI)](User_Install CodaLab CLI).
