## **Why CodaLab Worksheets?**

While there has been tremendous progress in machine learning, data science,
natural language processing, computer vision, and many other data- and
computation-intensive fields, the research process is far from optimal.  Most
of the time, the output of research is simply a PDF file (published paper).
Even when people release their data and code (which is a big step forward), it
is often not obvious how to run it to obtain the results in a paper.  Simply
put:

> ***Today, researchers spend excrutiating amounts of time reproducing published results.***

The goal of CodaLab Worksheets is to fix this in order to both accelerate the
rate of research and make it more sound.

## **How does CodaLab Worksheets work?**

> ***CodaLab keeps the full provenance of an experiment, from raw data to the
> final performance numbers that you put in your paper.***

There are two important concepts in CodaLab: ***bundles*** and ***worksheets***.

***Bundles*** are immutable files/directories that represent the code, data, and results of an experimental pipeline.  There are two ways to create bundles.  First, users can ***upload*** bundles, datasets in any format or programs in any programming language.
Second, users can create ***run bundles*** by executing
shell commands that *depend* on the contents of previous bundles.
A run bundle is specified by a set of bundle dependencies and an arbitrary shell command.
This shell command is executed in a [docker container](https://www.docker.com) in a directory
with the dependencies.  The contents of the run bundle are the files/directories which are
written to the current directory by the shell command:
<img src="https://github.com/codalab/codalab-worksheets/wiki/images/execution.png" />
In the end, the dependency graph over bundles precisely captures the research
process in an *immutable* way.

***Worksheets*** organize and present an experimental pipeline in a comprehensible
way, and can be used as a lab notebook, a tutorial, or an executable paper.
Worksheets contain references to bundles, and are written in a custom [markdown
language](https://github.com/codalab/codalab-worksheets/wiki/Worksheet-Markdown).

As an example, the figure below shows the dependency graph over four bundles, along with two worksheets,
which contain both text and pointers to the bundles:
<img src="https://github.com/codalab/codalab-worksheets/wiki/images/worksheets-schema.png" />

CodaLab's philosophy is to give you full control of how you want to run your
experiments and get out of your way.  It just maintains the dependency
structure of your experiments and takes care of the actual execution.  A good
analogy is Git, which just maintains the revision history,
but gives you total freedom in terms of what to put in your repository.

## **How do I learn more?**

* [Quickstart](Quickstart): learn how to create bundles and worksheets (start here).
* [CLI Basics](CLI-Basics): learn how to use CodaLab from the comfort of your own shell.
* [Workflow](Workflow): learn how to use CodaLab in your daily research.
* [Executable Papers](Executable-Papers): learn how to put your research paper on CodaLab.
* [CLI Reference](CLI-Reference): learn how to be an expert CodaLab user.
* [Worksheet Markdown](Worksheet-Markdown): learn how to display tables of results and images in your worksheet.
* [Execution](Execution): learn how bundles are executed in docker.
* [Server Setup](Server-Setup): if you want to run a CodaLab server for your own group.
* [Latest Features](Worksheet-Features): what features have been recently added CodaLab lately?
* [worksheets.codalab.org](https://worksheets.codalab.org): the official CodaLab server.
* [About](About): who's behind CodaLab?

## **Where do I report bugs?**

CodaLab is under active development.  If you find bugs or have feature
requests, please file a GitHub issue:

- [for the website](https://github.com/codalab/codalab-worksheets/issues/new)
- [for the command-line interace](https://github.com/codalab/codalab-cli/issues/new)
