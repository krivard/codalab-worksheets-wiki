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
[install Docker](Installing Docker) and [create your own
image](Creating-Docker-Images). If you're creating a Docker image in Python,
we recommend using the [Codalab Python](https://hub.docker.com/r/codalab/python/)
image as your base image because it comes pre-installed with `python` and `pip`.

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

**Setup Instructions**

**Note**: If you want to use a **GPU** machine, first follow the "GPU Installation Instructions" instructions below.

**Step 1**. [Install Docker](Installing Docker), which will be
used to run your bundles in an isolated environment. 

**Step 2**. Start the worker:

    cl-worker --server https://worksheets.codalab.org --tag unique_worker_tag

The `--tag` flag is optional but allows you to have granular control over which
workers run which bundles. The next step will show how to run a job specifically
on this worker.

**Step 3**. Run a job on your worker. We use the `--request-queue tag=<worker's tag>` 
to specify our worker's tag. For non-GPU workers:

    # non-GPU workers
    cl run --request-queue tag=unique_worker_tag date

Check the bundle's `/stdout` by running `cl cat ^1/stdout` and you should see the current date. Congrats!

For GPU workers, run this command, which tests that nvidia-smi is working inside of Docker:

    # GPU workers
    cl run --request-docker-image nvidia/cuda:8.0-runtime --request-gpus 1 "nvidia-smi" --request-queue tag=unique_worker_tag

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

**Step 4**. Run `cl-worker --help` for information on all the supported flags. Aside
from the `--server`, other important flags include `--work-dir`
specifying where to store intermediate data and `--slots` controlling how many
bundles can run concurrently (generally the number of cores your machine has).

### GPU Installation Instructions

**Step 0**: Check that Docker is [installed](Installing-Docker).

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

**Step 2**: Install `nvidia-docker`. Download the package:

    wget https://github.com/NVIDIA/nvidia-docker/releases/download/v1.0.1/nvidia-docker_1.0.1-1_amd64.deb

Then install by running: 

    sudo dpkg -i nvidia-docker*.deb

**Step 3**: Test your setup. Run:

    sudo nvidia-docker run --rm nvidia/cuda nvidia-smi

Now head back to the worker installation instructions to start using your GPU on CodaLab!

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
