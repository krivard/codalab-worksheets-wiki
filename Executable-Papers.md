One of the principal aims of CodaLab is to make research more reproducible.  We
encourage all authors with published papers to create a worksheet which
contains the main code, data, experiments, tables, graphs associated with the
paper.  This makes it easier to extend other people's work.

## **Executable papers in CodaLab**

% wsearch tag=paper id=.sort-

## **Creating your own executable paper**

[worksheet executable-papers-tutorial]{{0xe46995c96e9e44e38922efea2c431905}}

Remember to add the `paper` tag to your worksheet so that your paper will show up here:

    cl wedit --tags paper

## **Guidelines for an executable paper**

The executable is not (currently) meant to be a replacement of your original
paper, but rather should be viewed as supplementary material.  It should
contain an explanation of the experiments as well as how they can be modified.
The full pipeline from raw data to final results (a table or graph) should be
represented.

- **Description**: Start by linking to the main paper.  Briefly describe the
  problem that you're solving and the solution.  Draw a figure if possible.
- **Data**: Upload the dataset(s) that you are using in raw format (i.e., don't
  upload a file with integers that encode some featurization of a document).
  Upload your preprocessing scripts and run your preprocessing steps as
  bundles in CodaLab.  This will make it easier to change the preprocessing
  later or have more transparency about these decisions, which while
  not part of your main algorithm, could be important for explaining
  your empirical results.
- **Code**: You should release your code (e.g., using
  [GitHub](https://github.com) or [mloss](http://mloss.org)).  Upload a
  snapshot version of your code to CodaLab and create a separate run bundle to
  compile it.  This ensures that someone who wants to modify the code can
  recompile it.  If you use third-party libraries, make them separate bundles
  that the compilation depends on.
- **Experiments**: Run the main experiments with the code on the data.
  If possible, output the predictions (at least on the test set) to a file,
  so that further analysis can be performed easily.  Ideally, you would
  use different run bundles to generate the results in a TSV or JSON file,
  or generate a graph.  You might find it useful to output HTML files to
  customize your output.

Here are some general tips:
- Check if the dataset you're using already exists on CodaLab to avoid
  uploading duplicate copies.
- Make sure that your run deletes large temporary files (so that you're not
  using unnecessary disk space).
- Try to break up your code/data into modules if it is useful to swap out
  different versions of a module.  This makes the pipeline more transparent,
  but also don't overdo it.
- Try to output your experimental results in a JSON or TSV file, so that you
  can use CodaLab's table formatting features to display this structured
  output.
- To copy someone's executable paper, simply copy the contents of the worksheet
  (you can use `cl wadd`) to your own worksheet, and then start using `cl mimic`
  to make modifications!

Finally, there are probably many ways to use CodaLab, so be creative!

This tutorial walks through how to prepare an executable paper for CodaLab.

# **Setup**

Note: in the following, replace `pliang` with your username.

1. Create an account by going to [CodaLab website](http://codalab.org),
clicking 'Sign Up' in the upper right corner and then 'Sign up for a new
account'.  You will get an email confirmation; click on the link to activate
your account.  Now you can sign in through the browser.

1. By default, you can enter commands via the web terminal.
   You can also download the [Codalab command-line interface
   (CLI)](https://github.com/codalab/codalab-cli/) to use the `cl` command from
   the comfort of your own shell.  The CLI gives you additional some additional
   functionality.

1. If you're using the CLI, CodaLab is initially pointing to the local instance of CodaLab
on your laptop.  To point to the main CodaLab server:

        cl work main::

  This will print out:

        Switched to worksheet https://codalab.org/bundleservice::pliang(0x58df10bc8ea04c84845141d551ca0999).

  Note that `main` refers to the CodaLab instance and `pliang` is the worksheet
  name (which defaults to your username, like a home directory).

1. For your new executable paper, create a new worksheet (you should use a
different worksheet name):

        cl new magic-acl2015

  This should print out the UUID of your worksheet.  For the name of
  worksheets, try to follow the convention of having a brief description
  followed by the conference and the year.  Note that these names are like
  identifier names in programs and cannot contain spaces or other weird
  characters.

  You can easily switch back and forth between worksheets (like `cd`):

        cl work pliang          # Go to your home worksheet
        cl work magic-acl2015   # Go to the worksheet you just created

1. Here are some commands to orient yourself:

        cl help         # Print out all possible commands
        cl ls           # List the bundles in your worksheet
        cl print        # Pretty-print your worksheet (bundles, subworksheets, text)
        cl wls .mine    # List your worksheets

1. Add a description of your worksheet:

        cl wedit

  This will popup an editor.  Add a description of your paper:

        # Language Understanding via Magic

        This worksheet contains the experiments from the following paper:

        > Percy Liang.
        >
        > [Language Understanding via Magic](http://www.cs.stanford.edu/~pliang/work_in_progress.pdf)
        >
        > Association for Computational Linguistics (ACL), 2015.

        # Background

        (explain the problem)

  You can look at the markdown source of another executable paper here for reference:

        cl wedit main::sempre-tables-acl2015

1. You can now go to `http://codalab.org/worksheets/<uuid>` to view your worksheet.

# **Uploading your executable paper**

Now you are ready to actually upload content.  The plan is to upload your
source code, libraries, datasets, evaluation program as separate bundles.  You
then compile the source code, run your algorithm, and run the evaluation
program.

1. From the CLI, you can upload bundles (with meaningful descriptions):

        cl upload program src -d "Source code for magic."
        cl upload program lib -d "Libraries needed for magic."
        cl upload program evaluation.py -d "Magical evaluation program."
        cl upload dataset data -d "Magical data."

        cl ls    # Make sure that everything is uploaded correctly

    From the web terminal, you can upload bundles by first zipping them up and
    then simply typing the following and selecting the file from your local
    file system:

        cl upload

1. Compile the source code.  The details of this depend on how you have your
code setup.  As an example, suppose `src` contains your source code and it has
a `Makefile` inside it.  Compilation also depends on some libraries `lib`.
In this case, we will run a command that first copies `src` to `build` and runs
`make`.  This is needed because `src` is an input bundle which is immutable,
and `make` presumably creates new files (e.g., `bin`) in the directory from
which it's run.

        cl run :src :lib 'cp -a src build && cd build && ln -s ../lib && make' -n compile
        cl make compile/build/bin -n bin  # Copy the output of the build (say it's called bin)

  The `cl run` runs the bash command in a temporary directory with only access
  to the dependent bundles `src` and `lib`.  The resulting bundle is called `compile`.

  You can get information about any bundle:

        cl info compile     # Look at metadata of a bundle
        cl cat compile      # Look inside a bundle

  If your run fails, you can delete it:

        cl rm compile

1. Run your program with different settings:

        cl run :bin :data 'bin/learn --input-dir data --output-dir . --num-iters 100' -n learn-baseline
        cl run :bin :data 'bin/learn --input-dir data --output-dir . --num-iters 100 --magic' -n learn-magic

  These runs run the given command in a temporary directory with access to
  `bin` and `data`.  Note that we simply output any files to the current
  directory.

1. Rendering your results:

  Suppose your program outputs a JSON or TSV file `stats.json` describing the
  statistics of the run (including command-line options).  Then you can
  customize the rendering of these bundles by editing (via `cl wedit`) and
  putting the following directives before the two bundles:

        % schema custom
        % add uuid uuid [0:8]
        % add command
        % add time time duration
        % add trainError /stats.json:trainError %.3f
        % add testError /stats.json:testError %.3f
        % display table custom

  If your program outputs images you can display them as follows:

        % display image /graph.png

  You can have two instances of the same bundle on a worksheet, so you can have
  both a graph and a table.  See the [worksheet markdown
  documentation](http://github.com/codalab/codalab-cli/worksheet-syntax.md) for
  full details.

1. Run the official evaluation script on the predictions output by your system,
so if other people use your dataset, they can use the exact same evaluation
script to make sure you have comparable numbers:

        cl run :evaluation.py true:data pred:learn-magic/predictions.txt 'python evaluation.py true pred' -n run-eval

  Note that you can name the dependencies (`true` and `pred`) that the program sees.

# **FAQ**

1. **What environment is my program running in?** By default, Ubuntu Linux
14.04 is used (inside a [docker](http://www.docker.com) container).  There are
many workers, some with 4 cores, 14 GB ram, and some with less.  For a more
detailed answer, you can do run a command to find out:

        cl run 'cat /proc/cpuinfo; free; df'

  If you want to request particular specs, do the following:

        cl run ... --request_memory 10g --request_cpus 4

  However, if no machine matches the spec, your job will just wait forever.

1. **How do I get packages installed?**  If you think this is a generic package
that many people will use, contact Percy and he can install it as part of the
default CodaLab installation.  Otherwise, you can create your own docker image
(please build it off of the default CodaLab image `codalab/ubuntu:1.9`) and run with that:

        cl run ... --request_docker_image <custom image>

  Read about how to setup docker [under "Execution using
  docker"](https://github.com/codalab/codalab-cli).  Then do this:

        docker run -t -i codalab/ubuntu:1.9                           # Start the existing docker container
        sudo apt-get install <custom package>                         # Install inside the docker container
        exit                                                          # Exit the docker container
        docker ps -a                                                  # Get ID of this last container
        docker commit -m "<description> <container ID> <custom image> # Save the container as an image
        docker push                                                   # Send this up to docker.com