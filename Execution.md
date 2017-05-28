This page describes a variety of topics related to how CodaLab executes run bundles and
manages the environment and hardware of those executions.

## Conceptual Overview: How the worker system works

This section is a conceptual overview of CodaLab's distributed worker system,
which executes run bundles in CodaLab. To begin, a worker machine connects to 
the CodaLab server and asks for run bundles to run. The CodaLab server finds 
a run that hasn't been executed yet, and assigns the worker to it. 
The worker then downloads (if not downloaded already) all the relevant
bundle dependencies from the CodaLab server and the
Docker image from Docker hub.

A quick digression about Docker: CodaLab uses Docker containers to define the 
environment of a run bundle. Each Docker container is based on a Docker image,
which specifies the full environment, including which Linux kernel
version, which libraries, etc. The next section has more details on
how to use Docker with CodaLab.

Once the worker has all of the dependencies installed, the worker then
executes the run in the Docker container, sending back status updates to the
CodaLab server (e.g., memory usage, etc.), and sees if there are any requests
to kill the worker. Any requests to download files in the bundle are forwarded
from the CodaLab server to the worker. At the end of the run, the worker sends
back all the bundle contents. See the [worker system design
doc](worker-design.pdf) for more detailed information.

## Specifying Environments with Docker

The current official Docker image is `codalab/ubuntu:1.9`, which consists of
Ubuntu 14.04 plus some standard packages (e.g., Python, Ruby, R, Java, Scala, g++).
See the entry in the [CodaLab Docker
registery](https://registry.hub.docker.com/u/codalab/ubuntu/) for more
information.

In general, when you create a run, you can specify which Docker container you want to use.

    cl run <command> --request-docker-image codalab/ubuntu:1.9

To see what Docker images are available, you can do a search on [Docker
hub](https://hub.docker.com). If nothing satisfies your needs, you can
[install Docker](Installing-Docker) and [create your own
image](Creating-Docker-Images). If you're creating a Docker image in Python,
we recommend using the [Codalab Python](https://hub.docker.com/r/codalab/python/)
image as your base image because it comes pre-installed with `python` and `pip`.

Here are some other commonly used docker images with machine learning libraries:

- TensorFlow:

        cl run 'python -c "import tensorflow"' --request-docker-image tensorflow/tensorflow:0.8.0

- Theano: [![](https://images.microbadger.com/badges/image/codalab/ubuntu.svg)](https://microbadger.com/images/codalab/ubuntu "Get your own image badge on microbadger.com")

        cl run 'python -c "import theano"' # Defaults to standard CodaLab Ubuntu image (codalab/ubuntu:1.9)

- Torch: [![](https://images.microbadger.com/badges/image/codalab/torch.svg)](https://microbadger.com/images/codalab/torch "Get your own image badge on microbadger.com")

        cl run 'th' --request-docker-image codalab/torch:1.1


## Hardware specs

On the `worksheets.codalab.org` CodaLab server, the workers are on Windows
Azure, and currently, each machine has 4 cores and 14GB of memory (but this
could change). You can always find out the exact specs by executing the command:

    cl run 'cat /proc/cpuinfo; free; df'

## Running jobs that use GPUs

CodaLab has publicly available GPUs! To use them, you'll need to 1) include the 
`--request-gpus` flag, and 2) specify a Docker image that has `nvidia-smi` installed using the `--request-docker-image` flag. For example:

    cl run --request-docker-image nvidia/cuda:8.0-runtime --request-gpus 1 "nvidia-smi"

And that's all it takes!

### GPU Docker Images

Here are some useful of Docker images with GPU support:

_Tensorflow_ Check out [Tensorflow's latest GPU Docker image](https://hub.docker.com/r/tensorflow/tensorflow/).

        cl run 'python -c "import tensorflow"' --request-docker-image tensorflow/tensorflow:0.8.0-gpu --request-gpus 1

_Theano_ Check out this [Theano GPU Docker image](https://hub.docker.com/r/nakosung/dockerfiles-1/).

        cl run 'python -c "import theano"' --request-docker-image nakosung/dockerfiles-1 --request-gpus 1

_Torch_ Check out this [Torth GPU Docker image](kaixhin/cuda-torch).

        cl run 'th' --request-docker-image kaixhin/cuda-torch:latest --request-gpus 1

## Running your own worker

By default, all bundles are run on worker machines connected to a CodaLab server. In
case you need to perform large amounts of computation or need specialized
hardware such as GPUs, you can connect your own machines to CodaLab by
running your own worker.

### Setup Instructions

**Step 1**. [Install Docker](https://github.com/codalab/codalab-worksheets/wiki/Installing-Docker), which will be
used to run your bundles in an isolated environment. 

**Step 2**. Start the worker:

    # replace <worker_tag> with your own tag name
    cl-worker --server https://worksheets.codalab.org --tag <worker_tag> 

The `--tag` flag is optional. If you use it, when you run a command, you can tell a bundle to
run on this particular worker, as we demonstrate in the next step.

**Step 3**. Run a job on your worker. We use the `--request-queue tag=<worker_tag>` using the `worker_tag`
from the previous step, which tells CodaLab to run this bundle on our machine. For non-GPU workers:

    # non-GPU workers
    # replace <worker_tag> with your own tag name
    cl run --request-queue tag=<worker_tag> date

Check the bundle's `/stdout` by running `cl cat ^1/stdout` and you should see the current date. Congrats!

**Flags**. Run `cl-worker --help` for information on all the supported flags. Aside
from the `--server`, other important flags include `--work-dir`
specifying where to store intermediate data and `--slots` controlling how many
bundles can run concurrently (generally the number of cores your machine has).

**Note**: If you want to use a **GPU** machine, follow the 
[GPU Installation Instructions](https://github.com/codalab/codalab-worksheets/wiki/Execution#gpu-installation-instructions) instructions below
after completing the worker setup instructions.

### GPU Installation Instructions

These are GPU install instructions for linux machines.

**Step 0**: Complete the worker setup instructions in the previous section.

**Step 1**: Check that the appropriate drivers are installed by running `nvidia-smi`. Check for an output similar to this one:

    Thu May 25 09:39:22 2017       
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 375.51                 Driver Version: 375.51                    |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla K80           Off  | E71B:00:00.0     Off |                    0 |
    | N/A   38C    P8    29W / 149W |      0MiB / 11439MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
                                                                               
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID  Type  Process name                               Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+

If you have not installed the drivers, here are some links that may help:

* For [Azure N Series GPUs](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/n-series-driver-setup)
* For [AWS P2 GPUs](https://aws.amazon.com/blogs/aws/new-p2-instance-type-for-amazon-ec2-up-to-16-gpus/2)
* For [Google Cloud GPUs](https://cloud.google.com/compute/docs/gpus/add-gpus)

**Step 2**: For Debian/Ubuntu users, install `nvidia-docker` by following the instructions 
[here](https://github.com/NVIDIA/nvidia-docker).

**Step 3**: Test your setup. Run:

    sudo nvidia-docker run --rm nvidia/cuda nvidia-smi

**Step 4**: Run this command, which tests that nvidia-smi is working inside of Docker:

    # GPU workers
    # replace <worker_tag> with your own tag name
    cl run --request-docker-image nvidia/cuda:8.0-runtime --request-gpus 1 --request-queue tag=<worker_tag> "nvidia-smi"

Check the bundle's `/stdout` by running `cl cat ^1/stdout` and you should see something similar to:

    Wed May 24 19:03:55 2017``
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 367.48                 Driver Version: 367.48                    |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |===============================+======================+======================|
    |   0  Tesla M60           Off  | B4B9:00:00.0     Off |                  Off |
    | N/A   35C    P8    13W / 150W |      0MiB /  8123MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                       GPU Memory |
    |  GPU       PID  Type  Process name                               Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------``------------------------------------------------------------------+

Congrats!
