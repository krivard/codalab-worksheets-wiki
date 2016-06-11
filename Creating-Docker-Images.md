Bundles are run inside [Docker](https://www.docker.com) containers. Containers provide an isolated Linux environment for your code containing various libraries and software packages. CodaLab uses an [Ubuntu Linux 14.04 image](https://hub.docker.com/r/codalab/ubuntu/) by default. You can specify which image to use when you are creating a run bundle using the `--request-docker-image <image>` flag. If the default image doesn't have the package you need, your options are:

1. Find an image that someone else has built. Package maintainers often release Docker images containing their packages.

2. Build your own image and upload it to Docker Hub. Instructions are given below.

## Building your own images
Detailed instructions for building images are available on the Docker website [here](https://docs.docker.com/engine/userguide/containers/dockerimages/). In the spirit of reproducibility we recommend building images using a Dockerfile so that how the image is built is documented. The steps are as follows:

1. [Download and install Docker](Installing-Docker).
2. Create a directory for your image and `cd` into it. Then start editing a file with the name `Dockerfile`.

    ```
    mkdir myimage
    cd myimage
    vim Dockerfile
    ```

3. Your image will contain everything from a base image that you will add to by running Linux commands. Good images to start from include `ubuntu:14.04`, `codalab/ubuntu:1.9` and `nvidia/cuda:7.5-cudnn4-devel` (which sets up NVIDIA CUDA in a way compatible to CodaLab, more below). Specify the image in the `Dockerfile`:

    ```
    FROM ubuntu:14.04
    ```

4. Specify a maintainer documenting who maintains the image:

    ```
    MAINTAINER My Humble Self <me@humblepeople.com>
    ```

5. Add Linux commands to run that install the packages you need and do any other setup.

    ```
    RUN apt-get -y update
    RUN apt-get -y install python2.7
    ```

6. (Optional) You can set up environment variables and so on with a `.bashrc` file. This file should go into a working directory and will be sourced when your container starts executing on CodaLab. Note that your base image may already have a working directory and `.bashrc` file that you should keep and add to. See what is inside your base image by running it: `docker run -it --rm codalab/ubuntu:1.9 /bin/bash`

    ```
    RUN mkdir -m 777 /user
    RUN printf "export PYTHONPATH=src\n" > /user/.bashrc
    WORKDIR /user
    ```

7. Create an account on [Docker Hub](https://hub.docker.com/) where you will upload the image. Now, supposing your Docker Hub ID is `humblepeople`.
8. Finish editing the `Dockerfile` and build your image, specifying your Docker HUB ID, a name and a tag:

   ```
   docker build -t humblepeople/python:1.0 .
   ```

9. (Optional) Verify your image looks good to you by running a few test commands. If it doesn't, update your Dockerfile and rerun the build command.

   ```
   docker run -it --rm humblepeople/python:1.0 /bin/bash
   root@fb586e56ac91:/user# python2.7
   >>> exit()
   root@fb586e56ac91:/user# exit
   ```

10. Upload your image to Docker Hub:

   ```
   docker push humblepeople/python:1.0
   ```

11. Use your image on Codalab by specifying the `--request-docker-image humblepeople/python:1.0` flag.

12. Make your Dockerfile available. Either upload it to your worksheet, add a link to it from the worksheet, or set up [automated builds](https://docs.docker.com/docker-hub/builds/).

### Using GPUs with CUDA
[worksheets.codalab.org](https://worksheets.codalab.org) doesn't have any machines with GPUs, but if you [run your own worker](Running Workers) or are using a worker set up on a machine with GPUs then keep reading.
