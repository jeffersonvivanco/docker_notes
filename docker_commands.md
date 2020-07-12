# Docker Commands

* `--help`
* `docker ps` - list of running containers
* `docker info`

## `docker run` - run a command in a new container
`docker run [OPTIONS] IMAGE [COMMAND] [ARG...]`

The `docker run` command first creates a writeable container layer over the specified image, and then
starts it using the specified command. A stopped container can be restarted with all its previous
changes intact using `docker start`. Ex running an image from a registry: `docker run username/repository:tag`

* `-a` - attach to STDIN, STDOUT, or STDERR
* `--cidfile` - write the container id to the file
* `-d` - in detached mode. Your container runs in the background.
* `--entrypoint` - overwrite the default ENTRYPOINT of the image.
* `-e` - set environment variables ex: `-e PROFILE`, or `-e PROFILE=DEV`
* `--env-file` - load variables from a file env.list
* `-expose` - expose a port or a range of ports
* `--help` - print usage
* `-i` - interactive, keep STDIN open even if not attached
* `-m` - memory limit
* `--mount` - attach a filesystem mount to the container
  * ex: `docker run --read-only --mount type=volume,target=/icanwrite busybox touch /icanwrite/here`
  * ex: `docker run -t -i --mount type=bind,src=/data,dst=/data busybox sh`
* `--name` - assign a name to the container
* `-l` - set meta data on a container, similar to environment variables
* `--label-file` - read in a line delimited file of labels ex: `docker run --label-file ./labels ubuntu bash`
* `-p 4000:80` - mapping port 4000 to 80, publish a container's port(s) to the host
* `--read-only` - mount the container's root filesystem as read only
* `--rm` - automatically remove the container when it exits
* `--sysctl` - sysctl options.
  * **Configure namespaced kernel parameters [sysctls] at runtime**

    The `--sysctl` sets namespaced kernel parameters (sysctls) in the container. For example,
    to turn on ip forwarding in the containers network namespace, run this command.
    `docker run --sysctl net.ipv4.ip_forwarding=1 someimage` 

    Note: Not all sysctls are namespaced. Docker does not support changing sysctls inside of
    a container that also modify the host system. As the kernel evolves we expect to see more
    sysctls become namespaced.

    Currently supported sysctls

    IPC Namespace:
    * `kernel.msgmax`, `kernel.msgmnb`, `kernel.msgmni`, `kernel.sem`, `kernel.shmall`, `kernel.shmmax`, `kernel.shmmni`, 
      `kernel.shm_rmid_forced`.
    * Sysctls beginning with `fs.mqueue.*`
    * If you use the `--ipc=host` option these sysctls are not allowed

    Network Namespace:
    * Sysctls beginning with `net.*`
    * If you use the `--network=host` option using these sysctls are not allowed. 
* `-t` - allocate a pseudo-TTY
* `-v` - bind mount a volume ex: ``docker run -v `pwd`:`pwd` ...``
  * When the host directory of a bind-mounted volume doesn't exist, Docker will automatically create this directory on the host for you.
  * mounted directory must be in the home directory not in the root directory in linux
* `-w` - working directory inside the container

## `docker image` - manage images
* `ls` - list images
  * `-a` - list all images
  * `-q` - only show numeric ids
* `rm IMAGE_ID`
* `rm $(docker image ls -a -q)` - remove all images from this machine

## `docker build` - build an image from a Dockerfile
`docker build [OPTIONS] PATH | URL | -`

The `docker build` command builds Docker images from a Dockerfile and a "context". A build's context is the set of
files located in the specified `PATH` or `URL`. The build process can refer to any of the files in the context.
For example, your build can use a *COPY* instruction to reference a file in the context. The `URL` parameter can
refer to 3 kinds of resources: GIT repos, pre-packaged tarball contexts and plain text files.

Ex: To build with PATH - `docker build .`

* `--tag=TAG` - add a tag to the image, like `latest`
* `-t` - name and optionally a tag in the 'name:tag' format. 
* `-f` - name of the Dockerfile
* `--force-rm` - always remove intermediate containers
* `--pull` - always attempt to pull a newer version of the image.

* `docker-machine`
  * `ip` - to find the ip address
  * `create --driver virtualbox VM_NAME` - creates a VM with name VM_NAME
  * `ls` - list the machines. Note: You need to run the following as administrator or else you don't get any reasonable output
  * `start <machine-name>` - to restart a machine that's stopped run
  * `ssh <machine> "<command>"` - to send commands to your VMs but doesn't give you immediate access to files on your local host.
  * `ssh <machine>` - open a SSH session with the VM; type "exit" to end
  * `scp <file> <machine>:~` - On mac and linux use to copy files accross machines. Copy file to node's home dir (only required if you use ssh to connect to manager and deploy the app)
  * `env <machine>` - show environment variables and to get the command to configure your shell to talk to `<machine>`
  * `eval $(docker-machine env <machine>)` - Mac/Linux command to connect shell to `<machine>`
  * `eval $(docker-machine env -u)` - Disconnect shell from VMs, use native docker
  * `stop $(docker-machine ls -q)` - Stop all running VMs
  * `rm $(docker-machine ls -q)` - Delete all VMs and their disk images
* `docker container`
  * `ls` - list all running containers, not filtered by service
    * `--all` - to list all containers (running or not running)
    * `-aq` - all in quiet mode
    * `-a` - even those not running 
  * `stop CONTAINER_ID`
  * `start CONTAINER_ID`
    * `-a` - attach STDOUT/STDERR and forward signals
    * `-i` - attach container's STDIN
  * `kill CONTAINER_ID` - force shutdown of the specified container
  * `rm CONTAINER_ID` - remove specified container from this machine
    * `$(docker container ls -a -q)` - remove all containers
* `docker service`
  * `ls` - list services
  * `ps SERVICE_NAME` - list the tasks for your service
* `docker stack`
  * `deploy -c <compose-file> <app>` - Deploy an app; command shell must be set to talk to manager ex:(myvm1), uses local Compose file
  * `services NAME_OF_STACK` - let's you view all services associated with that stack
  * `ps NAME_OF_STACK` - to view all tasks of a task
  * `rm NAME_OF_STACK` - take the app down
  * `ls` - list stacks or apps
* `docker swarm`
  * `init` - to enable swarm mode and make your current machine a swarm machine
  * `join` - run on other machines to have them join the swarm as workers
    * Note: Always run `init` and `join` with port 2377 (the swarm management port), or no port at all and let it take the default
    * Having trouble using SSH? Try the `--native-ssh` flag
  * `leave -f` - if you want to start over, run this on each node, make the master leave, kill the swarm
  * `leave` - make the worker leave the swarm
  * `join-token -q worker` - view join token
* `docker login`
* `docker tag IMAGE_NAME username/repository:tag`
* `docker push username/repository:tag`
* `docker pull [options] NAME:[:TAG|@DIGEST]` - pull an image or a repo from a registry
  * `-a` - download all tagged images in the repository
* `docker inspect TASK_OR_CONTAINER` - inspect task or container
* `docker node`
  * `ls` - run this on the manager to view the nodes in this swarm
  * `inspect <node ID>` - inspect a node