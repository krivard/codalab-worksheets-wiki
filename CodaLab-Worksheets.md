To dive in, check out the [latest features](Worksheet-Features), go through the [tutorial](User_CodaLab-Worksheets-Tutorial), [install the CLI](User_Install-CodaLab-CLI), view the [worksheet markdown reference](User_Worksheet-Markdown), or go to the [official CodaLab Worksheets instance](https://worksheets.codalab.org).

## **Why CodaLab Worksheets?**

While there has been tremendous progress in machine learning, data science, natural language processing, computer vision, and many other data- and computation-intensive fields, the research process is far from optimal.  Most of the time, the output of research is simply a PDF file (published paper).  Even when people release their data and code (which is a big step forward), it is often not obvious how to run it to obtain the results in a paper.  Simply put:

> ***Today, researchers spend excrutiating amounts of time reproducing published results.***

The goal of CodaLab Worksheets is to fix this in order to both accelerate the rate of research and make it more sound.

## **How does CodaLab Worksheets work?**

There are two important concepts in CodaLab: ***bundles*** and ***worksheets***.

- Users upload ***bundles*** (datasets in any format or programs in any programming
  language) and create new bundles by running new bundles that *depend* on
  previous bundles.  This forms a graph over bundles that captures the research
  process in an *immutable* way.
- Users create ***worksheets*** to present the information in a comprehensible
  way.  Worksheets are written in a special [markdown
  language](https://github.com/codalab/codalab/wiki/User_Worksheet-Markdown).

> ***CodaLab keeps the full provenance of an experiment, from raw data to the graph that you put in your paper.***

<a href="http://www.codalab.org"><img src="https://github.com/codalab/codalab/wiki/images/worksheets-schema.png" /></a>

## **How do I use CodaLab Worksheets?**

The easiest way to get started with CodaLab Worksheets is to use the web interface, which allows you to browse content, edit worksheets, upload bundles, and execute new commands.  For the power user, we provide a flexible command-line tool, `cl`, which can be either used from your own shell or the web interface.  The philosophy of `cl` is to give you the power to run whatever commands you want.  CodaLab is meant to be a thin layer that captures this process and allows you to present your results easily in a worksheet.

> ***Execute whatever commands you want.  CodaLab captures the full provenance and allows you to document and visualize the results.***

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

## **Where are my bundles being run?**

**Machines**.  When you create a run bundle via `cl run`, the command will be executed on a worker machine.  On the `worksheets.codalab.org` instance, the workers are on Windows Azure, and currently, each machine
has 4 cores and 14GB of memory (but this could change).  To find out the exact specs, just execute this run:

        cl run 'cat /proc/cpuinfo; free; df'

**Enviroment**.  By default, run bundles are executed in a [Ubuntu Linux 14.04 docker
container](https://registry.hub.docker.com/u/codalab/ubuntu/), which has
standard libraries (e.g., Python, Ruby, R, Java, Scala, g++) installed.

If you need other libraries, you can specify another docker image:

    cl run '...' --request-docker-image <docker image>

To create your own image with custom libraries, first [install docker](Installing-Docker).  Then you can create your custom image:

        docker run -t -i codalab/ubuntu:1.9                           # Start the existing docker container
        sudo apt-get install <custom package>                         # Install inside the docker container
        exit                                                          # Exit the docker container
        docker ps -a                                                  # Get ID of this last container
        docker commit -m <description> <container ID> <custom image>  # Save the container as an image
        docker push <custom image>                                    # Send this up to dockerhub.com

## **Where do I report bugs?**

CodaLab is still in alpha and under active development.  If you find `bugs` or have feature
requests, please file a GitHub issue:

- [for the website](https://github.com/codalab/codalab/issues/new)
- [for the command-line tool](https://github.com/codalab/codalab-cli/issues/new)

## **How do I learn more?**

* [CodaLab Worksheets Tutorial](User_CodaLab-Worksheets-Tutorial): start here to dive into CodaLab Worksheets and learn the concepts.
* [Installing the CodaLab CLI](User_Install-CodaLab-CLI): if you want to use CodaLab from the comfort of your own shell.
* [Creating Executable Papers](Executable-Papers): how to put your paper on CodaLab
* [CodaLab Worksheet Markdown Reference](User_Worksheet-Markdown): learn how to display tables of results and images in your worksheet.

### For Developers

* [Setting up a Local Server](Setup-Local-Worksheets)
* [Code Design](Dev_CodaLab-CLI-Code-Design)
* [Database Migrations](Dev_CodaLab-CLI-Database-Migrations)
* [Unit Tests](Dev_CodaLab-CLI-Unit-Tests)