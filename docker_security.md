# Docker Security

There are 4 major areas to consider when reviewing Docker security:
* the intrinsic security of the kernel and its support for namespaces and cgroups;
* the attack surface of the Docker daemon itself;
* loopholes in the container configuration profile, either by default, or when customized by users;
* the "hardening" security features of the kernel and how they interact with containers

## Kernel Namespaces
Docker containers are very similar to LCX containers, and they have similar security features. When
you start a container with `docker run`, behind the scenes Docker creates a set of namespaces and
control groups for the container.

**Namespaces provide the first and most straightforward form of isolation**: processes running within
a container cannot see, and even less effect, processes running in another container, or in the host
system.

