# Docker

This is a quick overview over stuff I have learned about Docker. Feel free to dig around ;)

- [Why Docker?](#why-docker)
- [Linux Basics](#linux-basics)
- [Kernel Namespaces](#kernel-namespaces)
- [Images and Containers](#images-and-containers)
- [Docker Lifecycle](#docker-lifecycle)
- [Docker Files](#docker-files)
- [Docker Compose](#docker-compose)

## Why Docker?

One of the most famous sentences in computer science is "It works on my machine". The goal of Docker is, to eliminate that for good.

How? Well, the hardware you are trying to run your application on is rarely the problem. It is usually the software environment, that is missing pieces, has version conflicts, etc. -> Docker packages the entire software environment into something called an "Image" and ships it with your application. Therefore, you can spin up the application with your environment anywhere without worrying about dependencies :)

## Linux Basics

Docker is exclusively a Linux application. (If you are running Docker Desktop on Windows or MacOS, it secretly spins up a virtual machine to host Docker in.) So to understand how exactly Docker works, it is helpful to have some basic understanding of the architecture of Operating Systems.

```
+-----------+---------+
| User Apps | OS Apps |
+-----------+---------+
|      Libraries      |
+---------------------+
|       Kernel        |
+---------------------+
|      Hardware       |
+---------------------+
```

Above is printed the basic stacked landscape of a Linux system:

- **Hardware:** The physical hardware of your PC - CPU, Memory, Drives, Network Interfaces, ...
- **Kernel:** The core of the Linux Operating System that manages the hardware. It contains all drivers and is the only piece of software that has access to the full command set for the CPU, e.g. to access the full memory range. It hosts all running processes and decides which processes have access to what resources. The Kernel never acts on own behalf, it only reacts to calls from applications.
- **Libraries:** A set of common building blocks for applications. A very basic example is the C standard library: It contains code that talks to the Kernel, e.g. to allocate memory.
- **OS Applications:** This includes all basic applications that ship with the OS, ranging from most basic functionality like the login program or a shell, to full desktop environments, etc. These applications control what the PC is actually doing, by calling the Kernel to execute certain operations (and also do some calculations within the resources they have acquired).
- **User Applications:** These applications are installed by the user, for extra functionality. Similar to OS applications, this ranges from simple command line tools to behemoths like IDEs.

When someone talks about an "Operating System", they refer to the package of Kernel, Libraries and OS Applications. These form the software environment, that your application is running in.

## Kernel Namespaces

Now that we have a basic understanding of what the software environment is, we can talk about how it is possible to ship this entire environment with an application. Just from the information above, it seems like there is exactly one environment on your PC, and there is nothing you can do about that except for running a virtual machine. But the Linux kernel has a very special feature:

```
      Primary NS              Container NS
+-----------+---------+  +-----------+---------+
| User Apps | OS Apps |  | User Apps | OS Apps |
+-----------+---------+  +-----------+---------+
|      Libraries      |  |      Libraries      |
+---------------------+  +---------------------+
           ^                        ^
           |                        |
+---------------------+             |
|       Kernel        |-------------+
+---------------------+
|      Hardware       |
+---------------------+
```

When the Kernel executes processes, it does that in a certain context, which defines the following and more:

- The next process ID
- The mounted file systems
- The network interfaces
- Usage limits on CPU, Memory, ...
- ... and much more

This context is called a "namespace", and in normal operation you have exactly one: the primary namespace. However, the Linux kernel allows to create multiple secondary namespaces, that most importantly can have their own root file system, with independent system libraries. These namespaces can also have independent resource limits (CPU, Memory, ...), independent disk mounts, and may be constrained to a subset of the PC hardware (e.g. having access to only one network port).

This is essentially what docker uses to run a container: It dumps the root file system of a Linux flavor of your liking into a folder (libraries, etc.) and creates a new "namespace" that mounts this folder as root file system - é voila: you basically have two independent instances of Linux running on the same machine. They share the same kernel underneath, but since the kernel moves slowly and is backwards compatible very far back, this is very unlikely to pose a problem, as long as you don't try to reference cutting edge features from the newest kernel from your container (don't worry - you won't).

Of course, your container only needs the most basic functionality for your application - no GUI, no fancy features, ... That's why Docker containers usually run on small Linux distributions. A common choice is are Debian if you want to have all the tools you would commonly expect on a fully featured distro. Or if you really want to save storage, Alpine is widespread as minimal Linux distribution.

Btw.: Docker containers never render a GUI themselves, they only communicate to the outside world via networking. It IS possible to render a GUI through an XServer, but Docker is definitely built for network services.

## Images and Containers

When talking about Docker, you will definitely come across the two terms "image" and "container". Everyone always describes image as the construction manual and a container as one instance constructed from that manual. I think there is a more relatable analogy:

Think of an image as a ZIP file: You download it once, and it sit's on your PC readonly. If you want to access the files in that ZIP, you decompress that to a folder -> that's your container. You can decompress the ZIP as often as you like to as many folders as you like, and get arbitrarily many, perfectly equal instances. If you now alter a file in one of the folders, it is altered just there, all the other folders as well as the original ZIP are unaffected. And if you delete that folder, everything is gone and you are back to where you were before.

Docker is a little smarter than just unzipping, to save your storage space: Containers operate on the "copy on write" principle, so they all share the same image as base storage and only change differences to that image.

## Docker Lifecycle

If you just want to use an application written for Docker, the process is usually as follows:

1. Pick a **registry** that you can download the image from. For most open source software this will be the official "Docker Hub". It is possible to host your own registry though (e.g. for private images in a corporation). The command you are looking for is `docker pull`.
2. Once you have the image locally on your machine, you can create and start a container. This is done with `docker run`
3. You can start/stop the container arbitrarily while it exists.
4. When you are done with the container, you can remove it. This deletes the entire instance, including all the changed data within the container. The image stays untouched.

So... loosing all data does not sound very nice if you want to do anything more than play around. Fortunately, docker allows you to **mount** certain folders into the container. This basically means you take a folder in the container and make it a pointer, that goes somewhere in your file system. (The container will not be able to break out of this folder.)

This is done to

1. provide individual configuration to docker containers. (for example an `appsettings.json`)
2. persist created data of the container into a permanent folder on your disk, that survives the removal of the container. (for example a database file)

A very nice thing about this approach is, that you have a clear separation between reproducible build artifacts that you can download anytime from scratch, and actually valuable configuration and working data that you want to backup.

## Docker Files

If you want to write your own applications for Docker, you need to create your own image. This is done using a `Dockerfile`.

Don't worry - you don't need to start from scratch with a Linux distribution. A docker image consists of _layers_. You usually choose a base image as template, and then step by step install the things you additionally need, until you finally install your application.

Let's look at a very basic `Dockerfile`:

```docker
# use the microsoft devcontainer image as base
FROM mcr.microsoft.com/devcontainers/base:bullseye

# Copy your script into the image
COPY post-create.sh /usr/local/bin/post-create.sh

# Make sure it’s executable
RUN chmod +x /usr/local/bin/post-create.sh

# Run your script during build
RUN /usr/local/bin/post-create.sh
```

The first line in a dockerfile is usually the reference to the base image (`FROM`). This may be a plain Linux distribution, or some extended image like here.

After that, you copy external resources to the image, that you need to set up the environment (`COPY`).

And finally, with `RUN` you execute shell commands as _root_ user in the container, for example to install your compiler and then build and install your application.

This is a very minimal example. It is recommended to do things step-by-step, because each step in the Dockerfile is it's own layer, that can be cached. So if you change something, the build does not have to start from scratch, but only from the first line that actually has a change (e.g. because the copied file has changed).

## Docker Compose

Finally, docker compose is an alternative way to spin up containers. It's goal is to not manage each container individually, but to define an environment of multiple services and manage them as a group.

A docker compose file looks like this:

```yaml
# docker-compose.yaml
services:
    delta-mud:
        container_name: delta-mud
        image: ${DOCKER_REGISTRY-}delta-mud
        build:
            context: ~/git-robin/delta-mud/DELTA_MUD
            dockerfile: DELTA_MUD.UI/Dockerfile
        ports:
            - "10000:80"
            - "10001:443"
        volumes:
            - ~/hosting-environment/delta-mud/_config:/app/_config
            - ~/hosting-environment/ssl:/app/_data
        restart: always
        environment:
            # Self Signed SSL: https://learn.microsoft.com/en-us/aspnet/core/security/docker-compose-https?view=aspnetcore-7.0
            - ASPNETCORE_ENVIRONMENT=Release
            - ASPNETCORE_URLS=https://+:443;http://+:80
            - ASPNETCORE_Kestrel__Certificates__Default__Password=##REDACTED##
            - ASPNETCORE_Kestrel__Certificates__Default__Path=/app/_data/self-signed-certificate.pfx
            # - ASPNETCORE_PATHBASE=/delta-mud
        networks:
            - docker-shared

networks:
    docker-shared:
        name: docker-shared
        external: true
```

For a network service landscape, you usually need the two primary sections `networks` and `services`.

By default, containers have isolated network access, but cannot see each other. But you can define a virtual network and reference that from multiple services, to make services reach each other. A cool feature about this: In this virtual subnet, you have a DNS service that resolves IPs for container names, so you can reach the other containers by name.

In the `services` section you can define multiple containers, with their images, mounts ("volumes"), environment variables and much more. The example only shows one container, but for example you could run the web-service and it's database from one docker compose file. To start the entire landscape, you just enter the folder with the `docker-compose.yaml` and run `docker compose up`.
