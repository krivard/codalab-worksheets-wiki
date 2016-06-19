This document will in five minutes show you how to upload a program, a dataset,
and execute your first run in CodaLab.

After signing up for a CodaLab account and signing in, click on `My Home` in
the top-right navigation bar.  This takes to your home worksheet (named
something like `home-pliang`, which behaves like your home directory.
directory).

### Step 1: Upload files

Create a file on your local computer called `a.txt` with the following contents
(with your favorite editor):

    foo
    bar
    baz

Create another file called `sort.py` with the following contents:

    import sys
    for line in sorted(sys.stdin.readlines()):
        print line,

Click on the `Upload` button on the right panel to popup a file browser to select
`a.txt`.  Do the same with `sort.py`.

When a file is uploaded, it is appended to the current worksheet as a **bundle**
and shows up as a row in a table (as the default view).  Each bundle has a
32-character **UUID**, a globally unique identifier, which can be used to
unambiguously refer to that bundle; its contents cannot be changed.
You can, however, edit its metadata (name, description, etc.) by clicking on
the fields in the right panel.

While `a.txt` and `sort.py` are dataset and programs, respectively,
from CodaLab's perspective, these are just bundles.  Bundles can also be
directories; to upload a directory, you need to zip up the directory and upload
the zip file.

### Step 2: Run a command

Having uploaded some bundles, let's do something with them.  CodaLab allows you
to run arbitrary shell commands.  Each each command also a **run bundle** that
encapsulates the computation.

To create our first run bundle, click `New Run` on the right panel.
Select `sort.py` and `a.txt` as the dependencies (Step 1), and enter the
following command (Step 2):

    python sort.py < a.txt

Click `Run`.  This should append a new bundle to the current worksheet.  If you
wait a few seconds, the state will turn from `created` to `staged` to `running` to `ready`.
You should see the `stdout` on the right panel show the result:

    bar
    baz
    foo

To explain what just happened, CodaLab just ran your command in a [sandbox
environment](Execution) where the current directory contains only the
dependencies you specified, not other bundles in the worksheet.  It's like you
did this:

    $ ls
    sort.py
    a.txt
    $ python sort.py < a.txt

All the files and directories that are written to the current directory
(including stdout and stderr) are the contents of the newly-created run bundle.
When the command terminates, the contents of the bundle become immutable.
Files written elsewhere are not stored.  If you wish to install a system-level
package, you have to [build a new docker image](Execution).

**Parallelism**.  You can start off multiple runs in parallel, and even ones
that depend on previous runs that haven't yet finished.  Since CodaLab knows
about dependencies, it will wait for all the dependencies of a run to finish
before starting the run.

**Dependencies**.  You should treat the current worksheet as your current
directory, where you're running commands that depend on the existing bundles
and generate new ones.  However, you must be explicit about dependencies!
CodaLab will run your command only in the presence of the dependencies you
explicitly specify, not all the bundles in the current worksheet.

**Operations**.  If you made a mistake, you can kill the process for
your bundle by right-clicking on the corresponding row in the table and
selecting "Kill".  You can remove non-running bundles by selecting "Remove".

### Step 3: Present your results

So far, your worksheet just contains a table with one bundle per row.
But we can customize this view to better document and present our results.  To
edit the worksheet, click `Edit Source` button (or hitting `e`).

You will be taken to an editor with plain-text markdown source of the
worksheet.  [CodaLab markdown](Worksheet-Markdown) is an extension of markdown
that allows you to interleave bundles and formatting directives.

You can edit the source freely.  For example, you might change your worksheet to
the following (your worksheet will have different UUIDs):

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
Note that deleting a reference to a bundle does not actually delete the bundle;
it merely detaches it.

### Summary

Congratulations - you have successfully used CodaLab to upload programs, data,
and performs runs!  In practice, you might have more [complex
workflows](Workflow), running commands to preprocess the data, running
algorithms with different settings, using CodaLab to
manage your runs.  [CodaLab markdown](Worksheet-Markdown)
is a powerful way to create worksheets that document your
experiments, either for a private research log or a public executable paper.
There are many possibilities!

If you prefer working from the shell, check out the [command-line interface
(CLI)](CLI-Basics).
