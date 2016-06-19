The actual execution happens inside a [docker
container](https://www.docker.com) on a separate worker machine, which allows
you to specify a desired Linux environment with the requisite libraries /
software packages.

By default, the container is run using our custom [Ubuntu Linux 14.04
image](https://registry.hub.docker.com/u/codalab/ubuntu/) that includes a few
standard libraries, but you can use images created by others or [create your
own](Creating-Docker-Images), specifying the image to use with the
`--request-docker-image` flag.

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
