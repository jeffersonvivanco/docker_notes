# Docker Commands

* `--help`
* `docker ps` - list of running containers
* `docker info`
* `docker run` - to run an image
  * `IMAGE_NAME` - to run image called IMAGE_NAME
  * `-p 4000:80` - mapping port 4000 to 80
  * `-d` - in detached mode. Your container runs in the background.
  * `username/repository:tag` - run image from a registry
* `docker image`
  * `ls` - list images
    * `-a` - list all images
  * `rm IMAGE_ID`
  * `rm $(docker image ls -a -q)` - remove all images from this machine
* `docker build` - create image
  * `--tag=TAG` - add a tag to the image, like `latest`
  * `-t` - name and optionally a tag in the 'name:tag' format. 
  * `.` - create image using this directory's Dockerfile
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
* `docker inspect TASK_OR_CONTAINER` - inspect task or container
* `docker node`
  * `ls` - run this on the manager to view the nodes in this swarm
  * `inspect <node ID>` - inspect a node