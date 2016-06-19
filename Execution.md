## **Where are my bundles being run?**

**Machines**.  When you create a run bundle via `cl run`, the command will be executed on a worker machine.  On the `worksheets.codalab.org` instance, the workers are on Windows Azure, and currently, each machine
has 4 cores and 14GB of memory (but this could change).  To find out the exact specs, just execute this run:

        cl run 'cat /proc/cpuinfo; free; df'

You can also run your bundles on your own machines, as described in the [tutorial](User_CodaLab Worksheets Tutorial).

**Enviroment**.  By default, run bundles are executed in a [Ubuntu Linux 14.04 docker
container](https://registry.hub.docker.com/u/codalab/ubuntu/), which has
standard libraries (e.g., Python, Ruby, R, Java, Scala, g++) installed.

If you need other libraries, you can specify another docker image:

    cl run '...' --request-docker-image <docker image>

You can also [create your own images with custom libraries](Creating-Docker-Images).
