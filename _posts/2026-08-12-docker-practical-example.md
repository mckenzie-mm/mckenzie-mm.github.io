---
layout: post
author: ted
title: "Docker Commands & Notes"
summary: Docker commands; running, stopping and deleting containers by way of an example.
tags: [docker, devops, nodejs]
index: 1
---
## 1. Introduction

Here is a complete, minimal example of a simple Docker container. It assumed that you have installed and setup Docker on your work station. If you are using Ubuntu or one of the other Linux distros, setup is easy.

## 2. Project Structure

Create a directory named my-container with the following files:

```
my-container/
  ├── index.js
  └── Dockerfile
```


## 3. Code Details

The code contained in index.js is a simple http network router written in NodeJS and listening on port 5000. It routes GET requests to: <i>'/'</i> , <i>'/about'</i> & <i>'/api/data'</i> with the response shown in the code.


<b>my-container/index.js</b>

```js
const http = require('http');

// Define route handlers
const routes = {
  '/': (req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Welcome to the Home Page!');
  },
  '/about': (req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('About Us: This is a simple Node.js router network.');
  },
  '/api/data': (req, res) => {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify({ status: 'success', data: 'Hello World' }));
  }
};

// Create the router server
const server = http.createServer((req, res) => {
  // Parse URL path
  const urlPath = req.url;

  // Check if route exists
  if (routes[urlPath]) {
    routes[urlPath](req, res);
  } else {
    // 404 Not Found handler
    res.writeHead(404, { 'Content-Type': 'text/plain' });
    res.end('404 Page Not Found');
  }
});

// Start listening on port 5000
server.listen(3000, () => {
  console.log('Router network running at http://localhost:3000/');
});
```

## 4. Docker File

Docker creates images from Dockerfiles and runs a container from these images as a process. This is done using a command line tool called "docker". 

To build an image, you need a Dockerfile. A Dockerfile is a text-based file with no file extension that contains a script of instructions, similiar to a bash script. Docker uses this script to build a container image.

<b>my-container/Dockerfile</b>

```docker
FROM node:18-alpine
WORKDIR /app
COPY index.js .
EXPOSE 5000
CMD ["node", "index.js"]
```

Make sure you have a terminal window open in the my-app folder containing the Dockerfile. To build the image:

```sh
$ docker build -t my-container .
```
The "." tells docker to look for a file called <i>'Dockerfile'</i> in the current folder. It's the number one omission from this command by 99.99% of developers (Docker, can we please have a default without the "."). The first image created takes some time because Docker must download any libraries that are listed. In this case node:18-alpine. 

Every image built is given a unique ID, The "-t" flag on the command line, tags the image with the name "my-container" to make it easier to work with. Otherwise you need to use the Image ID.

After it has finished, you can list all of the images with:

```sh
$ docker images

| REPOSITORY   | TAG    | IMAGE ID      | CREATED        | SIZE   
| my-container | latest | 7f0196e8ec9e  | 11 seconds ago | 127MB
```

Then to run your container from its image use the command:

```sh
 $ docker run -d my-container
 ```

The -d flag (short for --detach) runs the container in the background. This means that Docker starts your container and returns you to the terminal prompt. Also, it does not display logs in the terminal.

To see all the running (and stopped) containers 

```sh
 $ docker ps -a

| CONTAINER ID | IMAGE        | CREATED        | STATUS       | PORTS    | NAMES              
| 8620e25d41fe | my-container | 10 seconds ago | Up 9 seconds | 5000/tcp | exciting_williams 
```

The "ps" is shorthand for "process" and the flag "-a" means "all". Docker automatically assigns a name and id to the container.



When the container is launched it is added to a "bridge network", and allocated a private IP address. The bridge network is isolated from the external Host OS. However containers can communicate between each other if they know their respective IP addresses. 

![Alternative text for accessibility]({% link assets/images/docker-network.drawio.png %})
**Figure 1. Docker container running on bridge private network.**

Docker has a network command for investigating the network. To see all the containers in the bridge network:


```sh
 $ docker network inspect bridge

"Containers": {
  "8620e25d41fe": {
      "Name": "exciting_williams",
      "EndpointID": "d26765545cd689058c40429ca8e950fd87b230ecf8e9e63907b6da263df8667e",
      "MacAddress": "02:42:ac:11:00:02",
      "IPv4Address": "172.17.0.2/16",
      "IPv6Address": "" 
  }
}
```

Note the private IP address, 172.17.0.2/16, which was automatically assigned to the container. 

To enable a container it to be accessed externally, Docker has a "host network", which performs port mapping from an External IP address to the Internal IP address. We did not perform this mapping when we launched the container and it will be isolated.

## 6. Port Mapping

Using the host network to map the container to an external source is easy. First stop and remove the running container with

```sh
$ docker stop <container_id_or_name>
```

```sh
$ docker rm <container_id_or_name>
```

Then re-launch a container with port mapping:

```sh
$ docker run -d -p 127.0.0.1:3000:5000 my-container
```
Navigate to the localhost on port 3000 on your PC
```
http://localhost:3000/about
```
```
About Us: This is a simple Node.js router network.
```


![Alternative text for accessibility]({% link assets/images/docker-network-port-map.drawio.png %})
**Figure 2. Mapping the container to an external source.**

