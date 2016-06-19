Now let's explore some more features of CodaLab.  Specifically, how to refer to
bundles, managing bundles, create macros, manging worksheets, manage
permissions, copy between multiple CodaLab instances.  This section
is most conveniently done from the [CLI](User_Install-CodaLab-CLI),
though some of the commands work in the web terminal.

## Some examples

Here are some examples of commands you can run in CodaLab:

    cl upload news.txt                            # Uploads a bundle to CodaLab
    cl run 'grep California %news.txt% | wc -l'   # Count the number of occurrences of "California"
    cl run date --request_memory 100m             # Run jobs with resource constraints
    cl kill ^                                     # Kill the last run bundle
    cl mimic a.txt news.txt                       # Run the same commands for a.txt on news.txt
    cl macro corenlp/corenlp news.txt             # Run Stanford CoreNLP
    cl new pliang-data                            # Create a worksheet
    cl perm news.txt public (read|none)           # Make the bundle publicly readable or not
    cl wperm pliang-data public (read|none)       # Make the worksheet publicly readable or not
    cl search mnist                               # Find a bundle by name
    cl search state=running                       # Bundles that are currently running
    cl search .mine .last                         # Show my last bundles
    cl search .mine .count                        # How many bundles do I have?
    cl search .mine size=.sum                     # How much disk space are my bundles taking up?

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

### Running bundles on your own machines

By default, all bundles are run on machines backing a CodaLab server.  In case you need to perform large amounts of computation or need specialized hardware such as GPUs, then you can connect your own machines to CodaLab by running your own worker.

To run your own worker, first [install Docker](Installing-Docker) which will be used to run your bundles in an isolated environment. Then, start the worker using the code from the `codalab-cli` git repository or by downloading it from [here](https://worksheets.codalab.org/rest/worker/code.tar.gz).

    codalab-cli/worker/worker.sh --bundle-service-url https://worksheets.codalab.org

For information on all the supported flags, run the script with `--help`. Aside from the `--bundle-service-url` other important flags include `--work-dir` specifying where to store intermediate data and `--slots` controlling how many bundles can run concurrently.

If you are running multiple workers and need granular control over which workers run which bundles, you can start each worker with a tag specified using the `--tag <tag>` flag and then specify the tag when creating the run bundle with the `--request-queue tag=<tag>` flag. That bundle will be scheduled to run on any of the workers with the specified tag.

You can find more information about how you should set up your machine and Docker images on [this page](Creating-Docker-Images).

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

Additionally, note that if you run your own worker, it will upgrade itself automatically. To avoid having to type in your username and password after a worker upgrades, you can pass in a file containing your credentials using the `--password-file` flag.

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
