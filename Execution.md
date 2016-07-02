The actual execution happens inside a [docker
container](https://www.docker.com) on a separate worker machine, which allows
you to specify a desired Linux environment with the requisite libraries /
software packages.

By default, the container is run using our custom [Ubuntu Linux 14.04
image](https://registry.hub.docker.com/u/codalab/ubuntu/) that includes a few
standard libraries, but you can use images created by others or [create your
own](Creating-Docker-Images), specifying the image to use with the
`--request-docker-image` flag.

A **docker image** specifies the full environment, including which Linux kernel
version, which libraries, etc.
The current official docker image is `codalab/ubuntu:1.9`, which consists of
Ubuntu 14.04 plus some standard packages (see the entry in the [CodaLab docker
registery](https://registry.hub.docker.com/u/codalab/ubuntu/).

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

[worker system design doc](worker-design.pdf)

## Running your own worker

By default, all bundles are run on worker machines behind a CodaLab server.  In
case you need to perform large amounts of computation or need specialized
hardware such as GPUs, you can connect your own machines to CodaLab by
running your own worker.

To run your own worker, first [install Docker](Installing-Docker) which will be
used to run your bundles in an isolated environment. Then, start the worker
using the code from the `codalab-cli` git repository or by downloading it from
[here](https://worksheets.codalab.org/rest/worker/code.tar.gz).

    codalab-cli/worker/worker.sh --server https://worksheets.codalab.org

For information on all the supported flags, run the script with `--help`. Aside
from the `--server`, other important flags include `--work-dir`
specifying where to store intermediate data and `--slots` controlling how many
bundles can run concurrently (generally the number of cores your machine has).

If you are running multiple workers and need granular control over which
workers run which bundles, you can start each worker with a tag specified using
the `--tag <tag>` flag and then specify the tag when creating the run bundle
with the `--request-queue tag=<tag>` flag. That bundle will be scheduled to run
on any of the workers with the specified tag.

