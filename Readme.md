# Docker Notes
Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. 
* Docker is a platform for developers and sysadmins to develop, deploy, and run applications with containers. The use of Linux containers to deploy applications
is called *containerization*. Containers are not new, but their use for easily deploying applications is. Containerization is increasingly popular because
containers are
  * flexible - even the most complex apps can be containerized
  * lightweight - containers leverage and share the host kernel
  * interchangeable - you can deploy updates and upgrades on the fly
  * portable - you can build locally, deploy to the cloud, and run anywhere
  * scalable - you can increase and automatically distribute container replicas
  * stackable - you can stack services vertically and on the fly
* Containers and virtual machines
  * A container runs natively on Linux and shares the kernel of the host machine with other containers. It runs a discrete process, taking no more memory
  than any other executable, making it lightweight.
  * By contrast, a virtual machine (VM) runs a full-blown "guest" operating system with virtual access to host resources through a hypervisor. In general, VMs
  provide an environment with more resources than most applications need.

## The Docker Platform
Docker provides the ability to package and run an application in a loosely isolated environment called a *container*. The isolation and 
security allow you to run many containers simultaneously on a given host. Containers are lightweight because they don't need the extra load of
a hypervisor, but run directly within the host machine's kernel. You can even run Docker containers within host machines that are actually virtual machines.
* Docker provides tooling and a platform to manage the lifecycle of your containers:
  * Develop your application and its supporting components using containers.
  * The container becomes the unit for distributing and testing your application.
  * When you're ready, deploy your application into your production environment, as a container or an orchestrated service. This works the
  same whether your production environment is a local data center, a cloud provider, or a hybrid of the two.

## Docker Engine
*Docker engine* is a client-server application with these major components:
  * A server which is a type of long-running program called a daemon process (the `dockerd` command)
  * A REST API which specifies interfaces that programs can use to talk to the daemon and instruct it what to do. 
  * A command line interface client (the `docker` command)
The daemon creates and manages Docker objects, such as images, containers, networks and volumes.

## What can I use Docker for?

### Fast, consistent delivery of your applications
Docker streamlines the development lifecycle by allowing developers to work in standardized envrionments using local containers which provide
your applications and services. Containers are great for CI/CD workflows.

Consider the following example scenario:
  * Your developers write code locally and share their work with their colleagues using Docker containers.
  * They use Docker to push their applications into a test environment and execute automated and manual tests.
  * When developers find bugs, they can fix them in the development environment and redeploy them to the test environment for testing and
  validation.
  * When testing is complete, getting the fix to the customer is as simple as pushing the updated image to the production environment.

### Responsive deployment and scaling
Docker's container-based platform allows for highly portable workloads. Docker containers can run on a developer's local laptop, on physical or
virtual machines in a data center, on cloud providers, or in a mixture of environments. Docker's portability and lightweight nature also make it easy
to dynamically manage workloads, scaling up or tearing down applications and services as business needs dictate, in near real time.

### Running more workloads on the same hardware
Docker is lightweight and fast. It provides a viable, cost-effective alternative to hypervisor-based virtual machines, so you can use more
of your compute capacity to acheive your business goals. Docker is perfect for high intensity environments and for small and medium deployments
where you need to do more with fewer resources.

## Docker architecture
Docker uses a client-server architecture. The docker *client* talks to the docker *daemon*, which does the heavy lifting of buidling, running, and
distributing your docker containers. The docker client and daemon can run on the same system, or you can connect a docker client to a remote
docker daemon. The docker client and daemon communicate over a REST API, oer UNIX sockets or a network interface.

* The Docker daemon
  * The Docker daemon (`dockerd`) listens for Docker API requests and manages Docker objects such as images,
  containers, networks, and volumes. A daemon can also communicate with other daemons to manage Docker services.
* The Docker client
  * The Docker client (`docker`) is the primary way that docker users interact with Docker.
* Docker registries
  * A Docker registry stores Docker images. Docker Hub is a public registry that anyone can use, and Docker is 
  configured to look for images on Docker Hub by default. You can even run your own private registry. If you use
  Docker Data Center (DDC), it includes Docker Trusted Registry (DTR).
  * When you use the `docker pull` or `docker run` commands, the required images are pulled from your configured
  registry. When you use the `docker push` command, your image is pushed to your configured registry.
* Docker objects - When you use Docker, you are creating and using images, containers, networks, volumes, plugins,
and other objects.
  * IMAGES
    * An image is an executable package that includes everything needed to run an application--the code, a runtime, libraries, environment variables,
    and configuration files.
    * An image is a read-only template with instructions for creating a Docker container. Often, an image is
    based on another image, with some additional customization. For example, you may build an image which is
    based on the `ubuntu` image, but installs the Apache web server and your application, as well as the 
    configuration details needed to make your application run.
    * You might create your own images or you might use only those created by others and published in a registry. To build your own image, you create
    a *Dockerfile* with a simple syntax for defining the steps needed to create the image and run it. Each instruction in a Dockerfile creates
    a layer in the image. When you change th Dockerfile and rebuild the image, only those layers which have changed are rebuilt. This is part of what
    makes images so lightweight, small, and fast, when compared to other virtualization technologies.
  * CONTAINERS
    * A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. You
    can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state. By default, a
    container is relatively well isolated from other containers and its host machine. You can control how isolated a container's network, storage,
    or other underlying subsystems are from other containers or from the host machine.
    * A container is defined by its image as well as any configuration options you provide to it when you create or start it.
    * When a container is removed, any changes to its state that are not stored in persistence storage disappear.
    * Example `docker run` command - the following command runs an `ubuntu` container, attaches interactively to your local command-line session,
    and runs `/bin/bash`.
    ```bash
    $ docker run -i -t ubuntu /bin/bash
    ```
      * When you run this command, the following happens (assuming you are using the default registry configuration):
        * If you don't have the `ubuntu` image locally, Docker pulls it from your configured registry, as though you had run
        `docker pull ubuntu` manually.
        * Docker creates a new container, as though you had run a `docker container create` command manually.
        * Docker allocates a read-write filesystem to the container, as its final layer. This allows a running container to create or modify files
        and directories in its local filesystem.
        * Docker creates a network interface to connect the container to the default network, since you did not specify any networking options.
        This includes assigning an IP address to the container. By default, containers can connect to external networks using the host's machine's
        network connection.
        * Docker starts the container and executes `/bin/bash`. Because the container is running interactively and attached to your terminal
        (due to the `-i` and `-t` flags), you can provide input using your keyboard while the output is logged to your terminal.
        * When you type `exit` to terminate the `/bin/bash` command, the container stops but is not removed. You can start it again or remove it.
  * SERVICES
    * Services allow you to scale containers across multiple Docker daemons, which all work together as a swarm with multiple *managers* and *workers*.
    Each member of the swarm is a Docker daemon, and the daemons all communicate using the Docker API. A service allows you to define the desired
    state, such as the number of replicas of the service that must be available at any given time. By default, the service is load balanced across
    all worker nodes. To the consumer, the docker service appears to be a single application.

## The underlying technology
Docker is written in `GO` and takes advantage of several features of the linux kernel to deliver its functionality.

### Namespaces
Docker uses a technology called `namespaces` to provide the isolated workspace called the *container*. When you run a container, Docker creates a
set of namespaces for that container. These namespaces provide a layer of isolation. Each aspect of a container runs in a separate namespace and
its access is limited to that namespace.

Docker Engine uses namespaces such as the following on Linux:
* The `pid` namespace: Process Isolation (PID: Process ID)
* The `net` namespace: Managing network interfaces (NET: Networking)
* The `ipc` namespace: Managing access to IPC resources (IPC: InterProcess Communication)
* The `mnt` namespace: Managing filesystem mount points (MNT: Mount)
* The `uts` namespace: Isolating kernel and version identifiers. (UTS: Unix Timesharing System)

### Control groups
Docker Engine on linux also relies on another technology called *control groups* (`cgroups`). A cgroup limits an application to a specific set of
resources. Control groups allow Docker Engine to share available hardware resources to containers, and optionally enforce limits and constraints.
For example, you can limit the memory available to a specific container.

### Union file systems
Union file systems, or UnionFS, are file systems that operate by creating layers, making them very lightweight and fast. Docker Engine
uses UnionFS to provide the building blocks for containers. Docker Engine can use multiple UnionFS variants, including AUFS, btrfs, vfs,
and DeviceMapper.

### Container Format
Docker Engine combines the namespaces, control groups, and UnionFS into a wrapper called a container format. The default container format
is `libcontainer`. In the future, Docker may support other container formats by integrating with technologies such as BSD Jails or
Solaris Zones.

## Docker commands
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

## Docker Hierarchy (top -> bottom)
* **Stack**
A stack is a group of interrelated services that share dependencies, and can be orchastrated and scaled together. A single is capable of defining
and coordinating the functionality of an entire application (though very complex applications may want to use multiple stacks).
  * Add a new service and redeploy by modifying the `docker-compose.yml` file.
  1. 
  ```Dockerfile
  version: "3"
  services:
    web:
      # replace username/repo:tag with your name and image details
      image: username/repo:tag
      deploy:
        replicas: 5
        restart_policy:
          condition: on-failure
        resources:
          limits:
            cpus: "0.1"
            memory: 50M
      ports:
        - "80:80"
      networks:
        - webnet
    visualizer:
      image: dockersamples/visualizer:stable
      ports:
        - "8080:8080"
      volumes:
        - "/var/run/docker.sock:/var/run/docker.sock"
      deploy:
        placement:
          constraints: [node.role == manager]
      networks:
        - webnet
  networks:
    webnet:
  ```
  The only thing new here is the peer service to `web`, named `visualizer`. Notice 2 new things here: a `volumes` key, giving the visualizer
  access to the host's socket file for Docker, and a `placement` key, ensuring that this service only ever runs on a swarm mananger--never a worker.
  That's because this container, built from an open source project created by Docker, displays Docker services running on a swarm in a diagram.

  2. Make sure your shell is configured to talk to `myvm1`.
  3. Re-run the `docker stack deploy` command on the manager, and whatever services need updating are updated.
  4. Take a look at the visualizer
  5. Adding redis to list of services in `docker-compose.yml` file above
  ```Dockerfile
  redis:
  image: redis
  ports:
    - "6379:6379"
  volumes:
    - "/home/docker/data:/data"
  deploy:
    placement:
      constraints: [node.role == manager]
  command: redis-server --appendonly yes
  networks:
    - webnet
  ```
  * There are a couple of things in the `redis` specification that make data persist between deployments of this stack:
    * `redis` always runs on the manager, so it's always using the same filesystem.
    * `redis` accesses an arbitrary directory in the host's file system as `/data` inside the container, which is where redis stores data.
  * Together, this is creating a "source of truth" in your host's physical filesystem for the Redis data. Without this, Redis would store
  its data inside `/data` inside the container's filesystem, which would get wiped out if that container were ever redeployed.
  * Create a `./data` directory on the manager `docker-machine ssh myvm1 "mkdir ./data"`. Run `docker stack deploy` one more time.
  Run `docker service ls` to verify that the three services are running as expected.  
* **Services**
  * In a distributed application, different pieces of the app are called "services". Services are really just "containers in production."
  A service only runs one image but it codifies the way that image runs--what ports it should use, how many replicas of the container should
  run so the service has the capacity it needs, and so on. Scaling a service changes the number of container instances running that piece of
  software, assigning more computing resources to the service in the process.
  * It's very easy to define, run, and scale services with the Docker platform, just write a `docker-compose.yml` file.
  * `docker-compose.yml` - is a YAML file that defines how Docker containers should behave in production.
    * Be sure you have pushed your image to a registry
    ``` yml
    version: "3"
    services:
      web:
        image: username/repo:tag
        deploy:
          replicas: 5
          resources:
            limits:
              cpus: "0.1"
              memory: 50M
          restart_policy:
            condition: on-failure
        ports:
        - "4000:80"
        networks:
        - webnet
    networks:
    webnet:
    ```
    * This file tells Docker to do the following:
      * Run 5 instances of that image as a service called `web`, limiting each one to use, at most, 10% of a single core of CPU time (this could
      also be e.g. "1.5" to mean 1 and a half core for each) and 50MB of RAM.
      * Instruct `web`'s containers to share port 80 via a load-balanced network called `webnet`. (Internally, the container's themselves publish to
      `web`'s port 80 at an ephemeral port.)
      * Define the `webnet` network with the default settings (which is a load-balanced overlay network).
  * Run your new load-balanced app
    * Before we can use `docker stack deploy` command we first run: `docker swarm init`
    * Now let's run it. You need to give your app a name. Here it's set to `getstartedlab`
    ```bash
    docker stack deploy -c docker-compose.yml getstartedlab
    ```
    * Our single service stack is running 5 container instances of our deployed image on one host.
  * A single container running in a service is called a *task*. Tasks are given unique IDs that numerically increment, up to the number of replicas
  you defined in `docker-compose.yml`. Since we are using a load balanced network, with each request, one of the 5 tasks (container) is chosen, in
  a round-robin fashion, to respond.
  * Scale the App - You can scale the app by changing the `replicas` value in `docker-compose.yml`, saving the change, and re-running the 
  `docker stack deploy` command we used earlier. Docker performs an in-place update, no need to tear the stack down first or kill any containers.
* **Container**
  * Your new development environment
    * In the past, if you were to start writing a Python app, your first order of business was to install a Python runtime onto your machine.
    But, that creates a situation where the environment on your machine needs to be perfect for your app to run as expected, and also needs to
    match your production environment.
    * With Docker, you can just grab a portable Python runtime as an image, no installation necessary. Then, your build can include the base
    Python image right alongside your app code, ensuring that your app, its dependencies, and the runtime, all travel together.
    * These portable images are defined by the `Dockerfile`.
  * `Dockerfile` - defines what goes on in the environment inside your container. Access to resources like networking interfaces and disk drives
  is virtualized inside this environment, which is isolated from the rest of your system, so you need to map the ports to the outside world, and be
  specific about what files you want to "copy in" to that environment.
    * Sample `Dockefile`
    ```Dockerfile
    # Use an official Python runtime as a parent image
    FROM python:2.7-slim

    # Set the working directory to /app
    WORKDIR /app

    # Copy the current directory contents into the container at /app
    COPY . /app

    # Install any needed packages specified in requirements.txt
    RUN pip install --trusted-host pypi.python.org -r requirements.txt

    # Make port 80 available to the world outside this container
    EXPOSE 80

    # Define environment variable
    ENV Name World

    # Run app.py when the container launches
    CMD ["python", "app.py"]
    ```
    * Note: Accessing the name of the host when inside a container retrieves the container ID, which is like the process ID for a running executable.
    * Share your image
      * To deploy containers to production, you have to push your image to a registry.
      * A *registry* is a collection of repositories, and a *repository* is a collection of images--sort of like a Github repository, except the
      code is already built. An account on a registry can create many repositories. The `docker` CLI uses Docker's public registry by default.
      * Steps
        * Login - make an account at hub.docker.com
        * Tag the image - the notation for associating a local image with a repository on a registry is `username/repository:tag`. The tag is
        optional, but recommended since it is the mechanism that registries use to give Docker images a version.
        * Publish the image - Upload your tagged image to the repository. Once complete, the results of this upload are publicly available. If you
        log in to Docker Hub, you see the new image there, with its pull command.
        * Pull and run the image from the remote repository - from now on, you can use `docker run` and run your app on any machine with this command.
        If the image isn't available locally on the machine, Docker pulls it from the repository.

    
## Docker Swarms
You deploy this application onto a cluster, running it on multiple machines. Multi-container, multi-machine applications are made possible
by joining multiple machines into a "Dockerized" cluster called a **swarm**. A swarm is a group of machines that are running Docker and joined
into a cluster. After that has happened, you continue to run the Docker commands you're used to, but now they're executed on a cluster by a
**swarm manager**. The machines in a swarm can be physical or virtual. After joining a swarm, they are referred to as **nodes**.

* Swarm managers can use several strategies to run containers, such as "emptiest node"--which fills the least utilized machines with containers.
Or "global", which ensures that each machine gets exactly one instance of the specified container. You instruct the swarm manager to use these
strategies in the Compose file.
* Swarm managers are the only machines in a swarm that can execute your commands, or authorize other machines to join the swarm as **workers**.
Workers are just there to provide capacity and do not have the authority to tell any other machine what it can and cannot do.
* To enable the use of swarms, you can switch Docker to **swarm mode**. Enabling swarm mode instantly makes the current machine a swarm manager.
From then on, Docker runs the commands you execute on the swarm you're managing, rather than just on the current machine.
* A swarm is made up of multiple nodes, which can be either physical or virtual machines.

### Setup
* Set up your swarm (We use VMs to quickly create a two-machine cluster and turn it into a swarm)
  * `docker swarm init`
  * `docker swarm join`
* Create a cluster (You need a hypervisor that can create virtual machines (VMs), so install Oracle VirtualBox)
  * Create a couple of VMs using `docker machine`, using the VirtualBox driver
* Initialize the swarm and add nodes - the first machine acts as the manager, which executes management commands and authenticates workers to
join the swarm, and the second is a worker.
  * Instruct `myvm1` to become a swarm manager with `docker swarm init`
  ```bash
  $ docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1 ip>"
  ```
* Done. Run `docker node ls` on the manager to view the nodes in this swarm

### Deploy your app on the swarm cluster
* Configure a `docker-machine` shell to the swarm manager
  * So far, you've been wrapping Docker commands in `docker-machine ssh` to talk to the VMs. Another option is to run `docker-machine env <machine>`
  to get and run a command that configures your current shell to talk to the Docker daemon on the VM. This method works better for the next step
  because it allows you to use your local `docker-compoase.yml` file to deploy the app "remotely" without having to copy it anywhere.
* Docker machine shell environment on mac/linux
  * Run `docker-machine env myvm1` to get the command to configure your shell to talk to `myvm1`
  ```bash
  $ docker-machine env myvm1
  export DOCKER_TLS_VERIFY="1"
  export DOCKER_HOST="tcp://192.168.99.100:2376"
  export DOCKER_CERT_PATH="/Users/sam/.docker/machine/machines/myvm1"
  export DOCKER_MACHINE_NAME="myvm1"
  # Run this command to configure your shell:
  # eval $(docker-machine env myvm1)
  ```
  * Run the given command `$(docker-machine env myvm1)`
  * Run `docker-machine ls` to verify that `myvm1` is now the active machine
* Deploy the app on the swarm manager
  * Now that you have `myvm1`, you can use its powers as a swarm manager to deploy your app by using the same `docker stack deploy` command we used
  earlier, and your local copy of `docker-compose.yml`. This command may take a few seconds to complete and the deployment takes some time to be
  available. Use the `docker service ps <service_name>` command on a swarm manager to verify that all services have been redeployed.
  * Just like before, run the following command to deploy the app `docker stack deploy -c docker-compose.yml getstartedlab`
* And thats it, the app is deployed on a swarm cluster
* Note: if your image is stored on a private registry instead of Docker Hub, you need to be logged in using `docker login <your-registry>` and then you
need to add the `--with-registry-auth` flag to the above command.
* Notice that the services (and associated containers) have been distributed between both `myvm1` and `myvm2`
* Accessing your cluster - you can access your app from the IP address of either `myvm1` or `myvm2`. The network you created is shared between them
and load-balancing.
* Iterating and scaling your app - scale the app by changing the `docker-compose.yml` file. Change the app behavior by editing code, then rebuild, and
push the new image. Then simply run `docker stack deploy` again to deploy these changes. You can join any machine, physical or virtual to this swarm,
using the same `docke swarm join` command you used on `myvm2`, and capacity is added to your cluster. Just run `docker stack deploy` afterwards, and your
app can take advantage of the new resources.

### Cleanup and Reboot
* you can tear the stack with `docker stack rm`
  * Keep the swarm or remove it?
    * At point some later, you can remove this swarm if you want to `docker-machine ssh myvm2 "docker swarm leave"` on the worker and
    `docker-machine ssh myvm1 "docker swarm leave --force"` on the manager.
* Unsetting docker-machine shell variable settings
  * You can unset the `docker-machine` environment variables in your current shell with the given command.
  `eval $(docker-machine env -u)`.
  * This disconnects the shell from `docker-machine` created virtual machines, and allows you to continue working in the same shell, now using native
  `docker` commands.
* Restarting docker machines - if you shut down your local host, Docker machines stop running.
  * To restart a machine that's stopped run, run: `docker-machine start <machine-name>`

## Deploy your App
1. Create your swarm. Run `docker swarm init` to create a swarm on the node.
2. Deploy your app. Run `docker stack deploy -c docker-compose.yml getstartedlab` to deploy the app on the cloud hosted swarm. Your app is now running
on your cloud provider.

## Spring Boot & Docker
* Sample Dockerfile
```Dockerfile
FROM openjdk:12.0.2

# We added a VOLUME pointing to "/tmp" because that is where a spring boot application creates working directories for
# tomcat by default. The effect is to create a temporary file on your host under "/var/lib/docker" and link it to the
# container under "/tmp". This step can be necessary for apps that need to write in the filesystem.
VOLUME /tmp

WORKDIR /javaspringnotes

COPY build/libs/javaspringnotes-1.0-SNAPSHOT.jar app.jar

EXPOSE 8443

CMD ["java", "-jar", "app.jar"]

```
* To take advantage of the clean separation between dependencies and application resources in a Spring Boot fat jar file, we
will use a slightly different implementation of the Dockerfile.
```Dockerfile
 VOLUME /tmp
 ARG DEPENDENCY=target/dependency
 COPY ${DEPENDENCY}/BOOT-INF/lib/ /app/lib
 COPY ${DEPENDENCY}/META-INF /app/META-INF
 COPY ${DEPENDENCY}/BOOT-INF/classes /app
 ENTRYPOINT ["java", "-cp", "app:app/lib/*", "hello.Application"]
```
* The DEPENDENCY parameter is pointing to a directory where we have unpacked the fat jar. If we get that right, it already
contains a BOOT-INF/lib directory with the dependency jars in it, and a BOOT-INF/classes directory with the application
classes in it. Notice that we are using the application's own main class hello.Application (this is faster than using the
indirection provided by the fat jar launcher).
* Using Spring Profiles
  * docker run -e "SPRING_PROFILES_ACTIVE=prod" -p 8080:8080 -t springio/gs-spring-boot-docker

### Build a Docker Image with Gradle
```gradle
buildscript {
    ...
    dependencies {
        ...
        classpath('gradle.plugin.com.palantir.gradle.docker:gradle-docker:0.13.0')
    }
}

group = 'springio'

...
apply plugin: 'com.palantir.docker'

task unpack(type: Copy) {
    dependsOn bootJar
    from(zipTree(tasks.bootJar.outputs.files.singleFile))
    into("build/dependency")
}
docker {
    name "${project.group}/${bootJar.baseName}"
    copySpec.from(tasks.unpack.outputs).into("dependency")
    buildArgs(['DEPENDENCY': "dependency"])
}
```
The configuration specifies 4 things
* a task to unpack the fat jar file
* the image name (or tag) is set up from the jar file properties, which will end up here as springio/gs-spring-boot-docker
* the location of the unpacked jar file, which we could have hard coded in the Dockerfile
* a build argument for docker pointing to the jar file
You can build a tagged docker image and then push it to a remote repository with Gradle in one command: `$ ./gradlew build docker`


