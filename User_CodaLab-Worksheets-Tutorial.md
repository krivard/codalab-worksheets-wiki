Welcome to CodaLab Worksheets!  This tutorial shows you how to use CodaLab
Worksheets to run experiments and explains the basic CodaLab concepts.  Running
experiments in CodaLab is quite simple but does require a slight shift in
thinking (not unlike switching from a procedural language to a functional
language).

CodaLab's philosophy is to give you full control of how you want to run your
experiments and get out of your way.  It just maintains the dependency
structure of your experiments and takes care of the actual execution.  A good
analogy is Git, which gives you total freedom in terms of what to put in your
repository, but just maintains the revision history.

One interacts with CodaLab mainly through a set of CodaLab commands for
uploading, deleting, adding, running, etc.  These commands are to be typed
either into the web terminal (at the top of the web interface at the
`CodaLab>` prompt) or into the [command-line interface (CLI)](User_Install
CodaLab CLI), if you prefer working from your shell.

Once you sign up for a CodaLab account and sign in, click on `My Home`.  Let's
get started!

## 5-minute Quick Start

After signing up for a CodaLab account and signing in, click on `My Home` (your
home worksheet, which is like your home directory).  Let's do a simple sorting
example.

### Step 1: Uploading bundles

Create a file called `a.txt` with the following contents:

    foo
    bar
    baz

Create another file called `sort.py` with the following contents:

    import sys
    for line in sorted(sys.stdin.readlines()):
        print line,

**Web interface.**

Click on `Upload Bundle` on the right panel to popup a file browser to select
`a.txt` (if you want to upload a directory, you need to zip up the directory
and upload the zip file).  Do the same with `sort.py`.

Once each bundle is uploaded, it is appended to the current worksheet, and
it shows up as a row in a table (as the default view).  The bundle has a
32-character **UUID**, a globally unique identifier, which can be used to refer
to this particular bundle; its contents cannot be changed.  You can, however,
edit its metadata (name, description, etc.) by clicking on the right panel.

**CLI.**

You can alternatively use the [CLI](User_Install CodaLab CLI).
To upload local files `a.txt` and `sort.py`:

    cl upload a.txt
    cl upload sort.py

To edit the metadata (you can also type these commands in the web terminal):

    cl edit a.txt

To list the bundles (one so far) you've uploaded, type:

    cl ls

You can see the metadata of a bundle:

    cl info -v a.txt

Note that while `a.txt` and `sort.py` are dataset and programs, respectively,
from CodaLab's perspective, these are just bundles (research assets).

### Step 2: Run a command

Having uploaded some bundles, let's run some commands.  CodaLab allows you to
run arbitrary shell commands, and each command also creates a bundle to
encapsulate the computation.

To create our first run bundle, type the following into the `CodaLab>` prompt
(hit `c` to get focus) or your shell if you're using the CLI:

    cl run sort.py:sort.py input:a.txt 'python sort.py < input' -n sort-run

This should append a new bundle named `sort-run` to the current worksheet.  If
you wait a few seconds, the output of the bundle will be shown on the right
panel.  You might need to refresh the worksheet by typing `shift-r`.

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

In general, a CodaLab run can depend on zero or more dependencies.  Each
dependency has the form

    <key>:<target>
    
The target can be a bundle (e.g., `a.txt`), or if the bundle is a directory, a
file/directory inside a bundle (e.g., `a.txt/file1`).  When the command (e.g.,
`python sort.py < input`) is executed, it is done so in a (temporary)
directory which with a read-only file/directory named `<key>` whose contents
are `<target>`.  The run should write to the current directory to populate the
contents of the bundle.

The actual execution happens inside a [docker
container](https://www.docker.com) on a separate worker machine, which allows
you to specify a desired Linux environment with the requisite libraries /
software packages.  If you're curious about the exact environment, you can
always run diagnostic commands to find out:

    cl run 'python --version'
    cl run 'uname -a'
    cl run 'cat /proc/cpuinfo'
    ...

By default, the container is run using our custom [Ubuntu Linux 14.04 image](https://registry.hub.docker.com/u/codalab/ubuntu/), but you can use images created by others or [create your own](Creating-Docker-Images), specifying the image to use with the `--request-docker-image` flag.

A run bundle has a `state` metadata field, which goes through the following sequence:

- `created`: initial state
- `staged`: dependencies of the bundle are now in the `ready` state
- `running`: the worker is setting up the execution environment or the command is executing
- `ready`: the run is complete and the exit code was 0 (success)
- `failed`: the run is complete and the exit code was not 0 or something else went wrong (failure)

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

To get more information:

    cl help     # Print out the list of all commands
    cl help rm  # Print out usage for a particular command

## Delving deeper

Now let's explore some more features of CodaLab.  Specifically, how to refer to
bundles, managing bundles, create macros, manging worksheets, manage
permissions, copy between multiple CodaLab instances.

### Continuing the sorting example

Recall that we last run the following command to sort a simple file:

    cl run sort.py:sort.py input:a.txt 'python sort.py < input' -n sort-run

To look at the metadata of the run bundle:

    cl info -v sort-run

We can look at individual files inside the bundle:

    cl cat sort-run/stdout

To make things more convenient, we can define a bundle whose contents are set
to be a target inside a bundle (note that `cl make` copies the contents rather
than just creating a simple link):

    cl make sort-run/stdout -n a-sorted.txt
    cl cat a-sorted.txt

On the CLI, we can download the results to local disk (in the web interface,
just click the download button):

    cl download a-sorted.txt

If you messed up somewhere, you can always remove a bundle:

    cl rm sort-run

You'll see that the above command threw an error, because `a-sorted.txt`
depends on `sort-run`.  To delete both bundles, you can remove recursively:

    cl rm -r sort-run

Note: be *very careful* with `rm -r` because it might delete a lot of bundles!

#### Sugar

You can also include bundle references directly in your run command, which
might be more natural than listing the dependencies ahead of time:

    cl run 'python %sort.py% < %a.txt%' -n sort-run
    cl run 'python %arg1:sort.py% < %arg2:a.txt%' -n sort-run
    cl run 'python %:sort.py% < %:a.txt%' -n sort-run

These are equivalent to the following, respectively:

    cl run b1:sort.py b2:a.txt 'python b1 < b2' -n sort-run
    cl run arg1:sort.py arg2:a.txt 'python arg1 < arg2' -n sort-run
    cl run sort.py:sort.py a.txt:a.txt 'python sort.py < a.txt' -n sort-run

Note that the last line is also equivalent to:

    cl run :sort.py :a.txt 'python sort.py < a.txt' -n sort-run

### Macros

Once we produce a run, we might want to do it again with slightly different
settings (e.g., sort another example).  CodaLab macros allow you to do this,
although understanding this concept requires us to take a step back.

In CodaLab, bundles form a directed acyclic graph (DAG), where nodes are
bundles and a directed edge from A to B means that B depends on A.  Imagine we
have created some runs that produces some output bundle O from some input
bundle I; I is an ancestor of O in the DAG.  Now suppose we have a new input
bundle I', how can we produce the analogous O'.  The *mimic* command does
exactly this.

First, recall that we have created `a.txt` (I) and `sort-run` (O).  Let us
create another bundle called `b.txt`:

    6
    3
    8

and upload it:

    cl upload b.txt

Now we can apply the same thing to `b.txt` that we did to `a.txt`:

    cl mimic a.txt a-sorted.txt b.txt -n b-sorted.txt

We can check that `b.txt.sorted` contains the desired sorted result:

    cl cat b-sorted.txt

Normally, in a programming language, we define macros as abstractions.  In
CodaLab though, notice that we've started instead by creating a concrete
example, and then used an analogy to re-apply this.  A positive side-effect is
that every macro automatically comes with an example of how it is used!

We can make the notion of a macro even more explicit.  Let's rename `a.txt` to
`sort-in1` and `a-sorted.txt` to `sort-out`:

    cl edit a.txt -n sort-in1
    cl edit a-sorted.txt -n sort-out

Then we can use the following syntactic sugar:

    cl macro sort b.txt -n b-sorted.txt

In CodaLab, macros are not defined ahead of time, but are constructed on the
fly from the bundle DAG.

### Worksheets

So far, we've focused on creating (and deleting) new bundles, and these bundles
automatically added to your home worksheet (e.g., `home-pliang`).  You can
always type the following to see what worksheet you're on:

    cl work

In this way, a worksheet is like a directory.  But unlike a directory, it
remembers the order of the items (which can be changed) and a worksheet can
contain other items besides bundles.

We can add items (text or bundles) to the current worksheet, which appends to
the end:

    cl add text "Here's a simple bundle:" .  # '.' refers to the current worksheet
    cl add bundle sort.py .

A worksheet just contains pointers to bundles, and unlike bundles, they are
mutable.  We can display the contents of a worksheet as follows:

    cl print    # Shows all items (bundles, text, and worksheets)
    cl ls       # Only shows the bundles

We can create another worksheet:

    cl new pliang-scratch

This creates a new worksheet called `pliang-scratch`.  We can switch back and
forth between worksheets (analogous to switching directories using `cd`):

    cl work pliang-scratch      # Use the <username>-<name> convention
    cl work /                   # '/' refers to your home worksheet

At this point, it is important to note that a worksheet only contains
*pointers* to bundles and other worksheets.  Their existence is independent of
the container worksheet (think symlinks or hyperlinks), although CodaLab keeps
track of the reverse to avoid broken links.

With this in mind, we can edit the contents of this worksheet in a text editor.

    cl wedit

On the CLI, in the popped up editor (determined by the `EDITOR` environment variable),
you'll see markdown interleaved with bundles, worksheets, and formatting directives.
See documentation of the [markdown syntax](https://github.com/codalab/codalab/wiki/User_Worksheet-Markdown).
For example, the editor might look like this:

    Arbitrary text describing a bundle.
    [run run-echo-hello : echo hello]{0xa113e342f21347e4a65d1be833c3aaa8}
    Arbitrary text describing a worksheet.
    [worksheet pliang-scratch]{{0xfdd5d68b64c9450da918b24ce7708f34}}

You can leverage the full power of the editor to change the list of worksheet
items: you can add and remove text, bundles, worksheets.  You can switch the
order.  You can list a bundle multiple times.  You can do edit another
worksheet (e.g., `cl wedit scratch`) and transfer contents between the two.
This all works because the worksheet contents are merely pointers.  When you
remove a bundle or worksheet item, the item still exist; only the references to
them are removed.  At the end, you can display the fruits of your labor in the CLI:

    cl print

In the web interface, you can view and edit the worksheet.

To remove the worksheet, you need to first remove all the items by opening a
text editor and deleting all its contents and then running:

    cl wrm scratch

### Referencing bundles

So far, we have referred to bundles by their names.  In
a large CodaLab system with many users, names are not unique, and possibly not
even within the same worksheet.  A *bundle_spec* refers to the string that
identifies a bundle, importantly given the context (address, current
worksheet).

There are a number of ways to reference bundles:

- UUID (`0x3739691aef9f4b07932dc68f7db82de2`): this should match at most one
  bundle.  You can use a prefix (an error is thrown if the prefix doesn't
  resolve uniquely).
- Name (`foo`): matches the *last* bundle on the current worksheet with the
  given name.  You can use `foo%` to match bundles that begin with `foo` or
  `%foo%` to match bundles that contain `foo` (SQL LIKE syntax).
  You can use `w1/foo` to refer to a bundle by name on worksheet `w1`.
- Ordering (`^, ^2, ^3`): returns the first, second, and third bundles from the end
  of the current worksheet.
- Named ordering (`foo^, foo^2, foo^3`): returns the first, second, and third
  bundles from the end with the given name.
- You can refer to a range of bundles: `^1-3` resolves to `^1 ^2 ^3`.
- In the worksheet interface, if you press 'u', then this will paste the UUID
  of the current bundle into the command.  This is a very convenient way of mixing
  command-line and graphical interfaces.

In practice, `^` and `^2` are used frequently because future operations tend to
depend on the bundles you just created.

Warning: ordering references are not stable.  For example,
if you run:

    cl ls
    cl rm ^1
    cl rm ^2

This *does not* delete the first and second last bundles, but rather the first
and third!  The intended behavior is:

    cl rm ^1 ^2

Also, if someone else is adding to your worksheet while you're editing it, you
might end up referring to the wrong bundle.

### CodaLab instances (CLI only)

When you're using the web interface, you are connected to one particular
CodaLab instance (e.g., `worksheets.codalab.org`).  If you're using the CLI,
you can connect to multiple CodaLab instances and copy information between
them.

By default, there are two instances:

    local
    https://worksheets.codalab.org/bundleservice

To save typing, you can create an alias for instances:

    cl alias  # shows all aliases
    cl alias main https://worksheets.codalab.org/bundleservice

At any point in time, your CodaLab session (identified usually by your shell)
is pointing to a particular worksheet on a particular instance.

    cl work                     # Show the current instance and worksheet
    cl work local::
    cl work local::home-pliang
    cl work main::
    cl work main::home-pliang

The general form is:

    cl work <instance>::<worksheet>
    cl work <instance>::             # Defaults to <worksheet>=/

Just as in Git, sometimes you want to work locally and then once things are
ready, push things to the main server.  You can do the same with CodaLab.  The
difference is that bundles are atomic and mutable, so there is no merging.

Suppose we are on the home worksheet of `local`:

    cl work local::

To copy a bundle `a.txt` from `local` to `main`, do the following:

    cl add bundle a.txt main::

If the bundle `a.txt` (identified by UUID) already exists on `main`, then
nothing will be copied.  Otherwise, the contents of the bundle are copied from `local` to `main`.
In either case, a reference to the bundle is appended to the home worksheet on `main`.

You can also copy a bundle from `main` to `local`:

    cl add bundle main::a.txt .

By default, `cl add bundle` does not copy the dependencies of a bundle.  If you want to
copy the dependencies (for example, to reproduce a run on another machine),
then use `cl add bundle -d`.  The dependencies of the dependencies are not copied,
since only the immediate dependencies are required to execute a run.

In general, the `add bundle` command is as follows:

    cl add bundle [<address>::]<bundle> [<address>::]<worksheet>

To copy all the items from a worksheet (except nested worksheets) to another:
    
    cl wadd [<address>::]<worksheet> [<address>::]<worksheet>

Note that worksheets themselves are not copied, just the items within a
worksheet.  Any bundles that don't exist on the destination CodaLab instance
are copied over.

### Permissions

CodaLab implements the following permissions model:

- Users belong to groups.
- Each group has access to some bundles and worksheets.

There are three levels of access or permission:

- `none`: You can't even see that the worksheet exists.
- `read`: You can read/download, but not edit.
- `all`: You can do anything (edit/delete/etc.).

Notes:

- There is a designated `public` group to which all users implicitly belong.  If
  you want to make a worksheet world-readable, give the `public` group read
  permission.
- There is a designated root user (`codalab`) that has `all` permission to
  all bundles and worksheets.
- Each user has `all` permission to all bundles and worksheets that he/she owns.

To grant/revoke permissions:

    cl perm <bundle> <group> <(n)one|(r)ead|(a)ll>
    cl wperm <bundle> <group> <(n)one|(r)ead|(a)ll>

For example:

    cl perm bundle1 public r  # grant read permission
    cl perm bundle1 public a  # grant all permission
    cl perm bundle1 public n  # revoke permissions

We can transfer ownership (and therefore permissions) of bundles and
worksheets:

    cl chown <username> <bundle-1> ... <bundle-n>
    cl wedit <worksheet> -o <username>

To make a worksheet `w1` mutually-writable with your research group, first
create a group `g1`, add users `u1` and `u2` to it, and then give the group all
access:

    cl gnew g1
    cl uadd u1 g1
    cl uadd u2 g1
    cl wperm w1 g1 all

All bundles created on `w1` will initially inherit the permissions of that
worksheet, but these permissions can be changed independently.
    
To list the groups that you've created or belong to:

    cl gls

To look more into a given group `g1`:

    cl ginfo g1

### Search

The `cl search` command allows us to find bundles and compute various
statistics over them.  The search performs a conjunction over keywords.

    cl search <keyword-1> ... <keyword-n>

Some initial examples:

    cl search mnist                        # bundles whose name or uuid contains `mnist`
    cl search e342f                        # bundles whose name or uuid contains `e342f`
    cl search type=program                 # program bundles
    cl search name=mnist                   # bundles whose names is exactly `mnist`
    cl search state=running                # all running bundles
    cl search command=%python%             # bundles whose command contains `python`
    cl search dependency=0xa11%            # bundles that depends on the given bundle
    cl search worksheet=0xfdd%             # bundles that are on the given worksheet
    cl search owner=codalab                # bundles that are owned by the given user name
    cl search =%python%                    # match any field

You can combine search terms:

    cl search type=program owner=codalab   # programs owned by user `codalab`

You can change the number and ordering of results:

    cl search .offset=50 .limit=100        # bundles 50-99
    cl search size=.sort                   # sort by increasing size
    cl search size=.sort-                  # sort by decreasing size

There are some special commands:

    cl search .mine                        # show bundles that the current user owns
    cl search .last                        # bundles in reverse order of creation
    cl search .floating                    # bundles that aren't on any worksheet

Operations that return a single number rather than a list of bundles:

    cl search .count                       # return total number of bundles in the system
    cl search size=.sum                    # return total number of bytes (nominal)
    cl search size=.sum data_hash=0x%      # return total number of bytes (actual, where we only count bundles with data)

We can combine these keywords to yield the following handy queries:

    cl search .mine .last                  # bundles that you just created
    cl search .mine .floating              # bundles that are floating (probably want to delete these periodically)
    cl search .mine size=.sort-            # what are the biggest bundles I own?

The search returns a list of bundles.  We can use `-u` to just get the uuids.
This can be piped into other commands:

    cl search .mine .floating -u | xargs cl rm  # delete the floating bundles
    cl search mnist -u | xargs cl add           # add mnist to the current worksheet

We can list and search worksheets in a similar fashion:

    cl wls                       # all worksheets
    cl wls .mine                 # my worksheet
    cl wls .last .limit=3        # last worksheets created
    cl wls name=.sort            # worksheets sorted by name
    cl wls bundle=0x3bb%         # worksheets containing this bundle
    cl wls owner=codalab         # worksheets owned by `codalab`
    cl wls =%Hello%              # worksheets containing 'Hello'

### User tips

The following describes some common tip and tricks to make the most out of CodaLab.

Delete the last five bundles (remember this also removes all other instances of
these bundles on the current worksheet):

    cl rm ^1-5

To kill the last bundle:

    cl kill ^

Most CodaLab commands generate one or more bundle UUIDs.  These can be piped to
further commands.  To kill all running bundles (be careful!):

    cl search state=running -u | xargs cl kill

To delete all *floating* bundles that do not appear on a worksheet (be careful!):

    cl search .floating -u | xargs cl rm

To run a bundle and create another bundle that depends on it:

    cl make $(cl run date)/stdout -n stdout

To wait for the last bundle to finish and then print out its output:
    
    cl run 'sleep 10; date'
    cl cat $(cl wait ^)/stdout

To find out what happened to the last bundle (e.g., why it failed):

    cl info -v ^

To rerun the last bundle (`-f args` prints out the command that was used to
generate the bundle):

    cl info -f args ^ | xargs cl

To put the command of a bundle back on the command-line for editing, create
this handy alias in bash:

    clhist() {
      history -s cl $(cl info -f args $1)
    }

Dependent bundles are read-only during a run, so to change files or
add to a dependent directory, everything must first be copied. Example
of compiling a source tree as a run bundle:

    cl run :src 'cp -r src src-build && cd src-build && make'

To compare two worksheets:

    vimdiff <(cl print -r worksheet1) <(cl print -r worksheet2)

To replace the contents of worksheet2 with worksheet1 (be careful when we do
this, since all the contents of worksheet1 are removed, although the bundles
themselves are not removed and will be floating):

    cl wedit -f /dev/null -w worksheet2
    cl wadd worksheet1 worksheet2

To change the metadata of a worksheet (e.g., rename or change the owner):

    cl wedit <worksheet> -n <new name>
    cl wedit <worksheet> -o <new owner>

To change the metadata of a bundle (e.g., rename or change the description):

    cl edit <bundle> -n <new name>
    cl edit <bundle> -d <new description>

### Editing worksheets

By default, you will use `cl wedit` to edit worksheets.  However, it is
convenient to just keep a text editor open.  Here's one way to do this.

1. Save the contents of a worksheet to a local file:

        cl print -r codalab > codalab.ws

2. Edit `codalab.ws`.

3. Save the worksheet back into CodaLab:

        cl wedit codalab -f codalab.ws

It is useful to define editor macros to execute the first and third commands.
For example, in vim, you could define a *save* and *load* command by adding the
following two lines to your `.vimrc`:

    map mk :wa<CR>:!cl wedit % -f %<CR>
    map mr :wa<CR>:!cl print -r % > %<CR>

The file that you load is in general not identical to the one you save (because
references get interpreted and commands get executed), so it's a good idea to
load right after you save.

Also, if you add bundles to the worksheet on the CLI, then you should reload
the worksheet before you make edits or else you will lose those changes.

### Updating CodaLab CLI

To update to the newest version of CodaLab, run:

    git pull

When you do this, the database schema might have changed, and you need to
perform a *database migration*.  To be on the safe side, first backup your
database.  Then run:

    venv/bin/alembic upgrade head

### Where things are stored

For reference, your CodaLab settings here:

    ~/.codalab/config.json

The session state and authentication tokens are stored here:

    ~/.codalab/state.json

By default, the metadata is stored in a SQLite database (you should switch to a
real database such as MySQL if you're going to do anything serious):

    ~/.codalab/bundle.db

All the bundles corresonding to the `local` address are stored here:

    ~/.codalab/partitions
