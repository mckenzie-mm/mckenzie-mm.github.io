---
layout: post
author: Mark
title: "Containers <b>&#8739;</b> Docker"
summary: From Virtual Machines to Containers and Docker. Lighter, faster to start, & much more resource-efficient
tags: [docker, devops]
---

Virtual Machines take up a lot of memory, preventing easy portability and limiting the number that can be installed on the Host OS at the same time. They are also slow to start. Both of these are a consequence of installing a full Guest OS. 

In many cases, one is only interested in obtaining a consistent development environment that can be easily shared (i.e. small size) and is fast to start. In 2008, containers were released which provide a solution. Initially, these were Linux Containers but they were very soon superceded by Docker Containers.

Container technology does not replace Virtual Machines; they complement each other. Since their introduction they have led to the widespread use of Micro Services hosted on Virtual Machines.

## Linux Operating System

The first containers were developed from the Linux Operating System and use it as the Host. This was very likely because Linux is open sourced with the code freely available. Microsoft Windows and MacOS are not open source.

An operating system like Linux is broadly split into two main conceptual layers:

#### # The Kernel:

The core engine. It directly manages hardware, CPU memory, and storage. It is not the environment itself, but the foundation underneath it.

#### # The Environment (User Space):

Everything the user interacts with above the kernel. This includes:

* The Shell: Command-line interface that interprets and executes user commands; for example Bash or Zsh.
    
* System Utilities & Libraries: Tools, daemons, and package managers that execute tasks. System libraries provide functions that help applications interact with the Linux kernel.
        
* Desktop Environment (Optional): Graphical user interfaces (GUIs) like GNOME or KDE Plasma if you are running a desktop rather than a headless server.

## Linux Containers (LXC)

Linux Containers were initially released in 2008. The technology behind Linux Containers is that it separates the Linux Environment from the Kernel. It does this in such a way that it allows multiple isolated Linux Environments (containers) to run on a single host using the same shared Linux kernel. This is called operating system-level virtualization. 

This offers advantages in speed and size. Containers start in seconds and require a fraction of the disk space and memory compared to traditional virtual machines. The small size of containers allows them to be easly shared. At the same time, a consistent development environment is maintained with code libraries, app versions etc the same as when they were built.

The issue with the Linux Container when it was first introduced was that it was not very user friendly.

## Docker & Docker Compose

The breakthrough with container technology came when Docker was introduced in 2013. It was built on top of Linux's existing container technologies and designed to enable interaction with containers in a developer-friendly way. Using Docker, developers could bundle dependencies within a container and allow them to distribute apps with ease.

Docker now dominates the market. The key features of Docker that have made it popular are:

* It invented the Dockerfile, which gave developers an easy-to-follow recipe for building container images,

* It simplified the CLI, allowing developers to skip the complex kernel stuff and use simple commands (like 'docker build') to make containers,

* And it introduced the Docker Hub, which allowed developers to share their work.

The useability of Docker was further enhanced with networking and volumes defined in a very easy way to work with.

To make it easy to built Microservices, Docker Compose was developed.  This is a tool developed by Docker for defining and running multi-container applications. It allows you to use a single YAML configuration file, typically named compose.yaml or docker-compose.yml, to configure all of your application’s services, networks, and volumes. With one command, you can spin up or tear down your entire multi-container environment.

In the next post I will give a simple example of a microservice built using Docker/Docker Compose.



