## **Why CodaLab Worksheets?**

While there has been tremendous progress in machine learning, data science, natural language processing, computer vision, and many other data- and computation-intensive fields, the research process is imperfect.  Most of the time, the product of the research is simply the published paper.  Even when people release their data and code (a big step forward), it is often not obvious how to run it to obtain the results in a paper.  Simply put:

> Today, people spend excrutiating amounts of time reproducing published results.

## **How does CodaLab Worksheets work?**

There are two important concepts: ***bundles*** and ***worksheets***.

- Users upload ***bundles*** (datasets in any format or programs in any programming
  language) and create new bundles by running new bundles that *depend* on
  previous bundles.  This forms a graph over bundles that captures the research
  process in an *immutable* way.
- At the same time, users create ***worksheets*** to present the information in a comprehensible
  way.  Worksheets are written in a special [markdown
  language](https://github.com/codalab/codalab/wiki/User_Worksheet-Markdown).

% display image / width=800
[dataset worksheets-schema.png]{0x466cd19aeb204b59a13b213289de795a}

Here are some examples of commands you can run in CodaLab:

    cl upload dataset news.txt                    # Uploads a dataset bundle to CodaLab
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

## **What environment are my bundles being run in?**

Whenever you create a run bundle (e.g., `cl run 'echo hello'`), that run will
be performed in well-defined [Ubuntu Linux 14.04 docker
container](https://registry.hub.docker.com/u/codalab/ubuntu/), which has
standard libraries installed.  You can also specify your own image if you have
special needs.

On the `worksheets.codalab.org` instance, these docker containers are currently hosted on
a small cluster of machines on Microsoft Windows Azure.  Each machine
has 8 cores and 14GB of memory.  But this could change.

## **Where do I report bugs?**

CodaLab is still in alpha and under active development.  If you find `bugs` or have feature
requests, please file a GitHub issue:

- [for the website](https://github.com/codalab/codalab/issues/new)
- [for the command-line tool](https://github.com/codalab/codalab-cli/issues/new)

### For Users

* [CodaLab Worksheets Tutorial](User_CodaLab Worksheets Tutorial)
* [Creating an Executable Paper in CodaLab](https://www.codalab.org/worksheets/0xe46995c96e9e44e38922efea2c431905/)
* [Installing the CodaLab CLI](User_Install CodaLab CLI)
* [CodaLab Worksheet Markdown Reference](User_Worksheet-Markdown)

### For Developers

* [Host Your Own Bundle Service](User_CodaLab CLI Server)
* [Code Design](Dev_CodaLab CLI Code Design)
* [Database Migrations](Dev_CodaLab CLI Database Migrations)
* [Execution in Docker](Dev_CodaLab CLI Execution in Docker)
* [Unit Tests](Dev_CodaLab CLI Unit Tests)
