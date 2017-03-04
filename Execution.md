## Docker

The execution of a run bundle happens inside a [Docker
container](https://www.docker.com) on a worker machine.
Each docker container is based on a **docker image**,
which specifies the full environment, including which Linux kernel
version, which libraries, etc.
The current official docker image is `codalab/ubuntu:1.9`, which consists of
Ubuntu 14.04 plus some standard packages (e.g., Python, Ruby, R, Java, Scala, g++).
See the entry in the [CodaLab Docker
registery](https://registry.hub.docker.com/u/codalab/ubuntu/) for more
information.

In general, when you create a run, you can specify which docker container you want to use.

    cl run <command> --request-docker-image codalab/ubuntu:1.9

To see what Docker images are available, you can do a search on [Docker
hub](https://hub.docker.com).  If nothing satisfies your needs, you can
[install Docker](Installing-Docker) and [create your own
image](Creating-Docker-Images).

Here are some examples of docker images:

- TensorFlow:

        cl run 'python -c "import tensorflow"' --request-docker-image tensorflow/tensorflow:0.8.0
        cl run 'python -c "import tensorflow"' --request-docker-image tensorflow/tensorflow:0.8.0-gpu

- Theano:  [![](https://images.microbadger.com/badges/image/codalab/ubuntu.svg)](https://microbadger.com/images/codalab/ubuntu "Get your own image badge on microbadger.com")

        cl run 'python -c "import theano"'  # Defaults to standard CodaLab Ubuntu image (codalab/ubuntu:1.9)

- Torch: [![](https://images.microbadger.com/badges/image/codalab/torch.svg)](https://microbadger.com/images/codalab/torch "Get your own image badge on microbadger.com")

        cl run 'th' --request-docker-image codalab/torch:1.1


## Running on worksheets.codalab.org

On the `worksheets.codalab.org` CodaLab server, the workers are on Windows
Azure, and currently, each machine has 4 cores and 14GB of memory (but this
could change).  You can always find out the exact specs by executing the command:

    cl run 'cat /proc/cpuinfo; free; df'

## Running your own worker

By default, all bundles are run on worker machines behind a CodaLab server.  In
case you need to perform large amounts of computation or need specialized
hardware such as GPUs, you can connect your own machines to CodaLab by
running your own worker.

To run your own worker, first [install Docker](Installing-Docker) which will be
used to run your bundles in an isolated environment. Then, start the worker
using the code from the `codalab-cli` git repository or by downloading it from
[here](https://worksheets.codalab.org/rest/workers/code.tar.gz) (to inflate the
file, run `tar -xvzf code.tar.gz` in an empty directory).

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

**Note for GPU users:** If you want your workers to support jobs that use NVIDIA GPUs (e.g. CUDA, cuDNN, etc.), you should install the `nvidia-docker` plugin on your machine as well, and make sure that `nvidia-docker-plugin` is listening at `localhost:3476`. Instructions for installation are found here:
https://github.com/NVIDIA/nvidia-docker 

## How the worker system works

In brief, a worker machine connects to the CodaLab server and asks for run
bundles to run.  The CodaLab server finds a run that hasn't been executed yet,
and assigns the worker to it.  The worker then downloads all the relevant
bundle dependencies from the CodaLab server (if not downloaded already), the
Docker image from Docker hub (if not downloaded already).  The worker then
executes the run in the Docker container, sending back status updates to the
CodaLab server (e.g., memory usage, etc.), and sees if there are any requests
to kill the worker.  Any requests to download files in the bundle are forwarded
from the CodaLab server to the worker.  At the end of the run, the worker sends
back all the bundle contents.  See the [worker system design
doc](worker-design.pdf) for more information.
