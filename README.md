# Docker

## Installation

See [this](https://docs.docker.com/engine/install/ubuntu/#installation-methods) link to install docker on your computer and clone this repository.

## Starting Docker

Once with the docker and the repository, you'll have the files `Dockerfile`, `dockerbuild.sh` and `rundocker.sh`. 

### Dockerfile
This file creates a environment image with Ubutu 18.04 and preliminar libraries. Use `RUN` to run shell commands. Take the opportunity to download the dependencies (following the template provided) and, if necessary, you can also download external libraries using commands as if you were in a terminal.

Remember, for each command, use the `RUN` tag first! 
Tip! Add new commands at the end of the file. Docker is smart and saves checkpoint images.
```
FROM ubuntu:18.04

# Dependencias
RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    qt5-default \
    sudo \
    protobuf-compiler \
    && apt-get clean

```

### dockerbuild.sh
This shell script is responsible for building the docker. please, remember to change `nomedodocker` to a name of your project (by default is `docker`), but also remember to modify it on the `rundocker.sh` file too.

```
# Evitar erro com o uso de video
xhost +local:docker

## Buildando o docker
# docker build . -f Dockerfile -t nomedodocker
docker build . -f Dockerfile -t docker
```

### rundocker.sh
This shell script is responsible for doing the entire execution. It is where the user, the environment, host, workdir, and other things are defined.

By default, we recommend that you do not modify the arguments.

Remember to modify the `DEFAULT_DOCKER_IMAGE` to the name of your docker, defined in the `nomedodocker` and `DEFAULT_CONTAINER_NAME` to `$nomedodocker$_container`, as follows.

Obs.: The `WORK_DIR` is the name of the directory of your Docker, so use it to navigate between your archives!
```
DEFAULT_DOCKER_IMAGE="docker"
DEFAULT_CONTAINER_NAME="docker_container"

WORK_DIR=`pwd`
CONTAINER_WORK_DIR=$WORK_DIR

CONTAINER_NAME=$DEFAULT_CONTAINER_NAME
DOCKER_IMAGE=$DEFAULT_DOCKER_IMAGE

# Executando o docker
docker run  -it \
            --user=$(id -u) \
            --env="DISPLAY" \
            --env="QT_X11_NO_MITSHM=1" \
            --name=$CONTAINER_NAME \
            --memory=1024g \
            --oom-kill-disable \
            --ipc="host" \
            --volume="/dev:/dev" \
            --privileged \
            --net=host \
            --workdir="${CONTAINER_WORK_DIR}" \
            --volume="${WORK_DIR}:${CONTAINER_WORK_DIR}" \
            --volume="/etc/group:/etc/group:ro" \
            --volume="/etc/passwd:/etc/passwd:ro" \
            --volume="/etc/shadow:/etc/shadow:ro" \
            --volume="/etc/sudoers.d:/etc/sudoers.d:ro" \
            --volume="/tmp/.X11-unix:/tmp/.X11-unix:rw" \
            $DOCKER_IMAGE

docker container rm $CONTAINER_NAME -f
```

## Usage
Once you have run the previous shell scripts, you will now see a window that will be in root mode.

We strongly recommend that within the Docker sent to the competition you include a shell script that can download and compile your team's repositories.

As all dependencies will be installed (process done during the compilation of `Dockerfile`) there should be no problems in the compilation.

That done, you will now be able to run the binaries and run your software =)

### Running multiple binaries
It is possible to run multiple binaries in a single terminal opened by the docker. To do this, use the `&> /dev/null &` command after run your binary, as follows:

```bash
scriptWD='pwd'    # WORK_DIR name
binFolder='myBin' # Binary folder name

cd $scriptWD
cd $binFolder
./binary &> /dev/null &
```

This will cause you to create a process that will run your binary, so you can create a shell script that runs all your binaries, if you have more than one.

