---
layout: post
author: Mark
title: "Docker Compose"
summary: Simplifying microservices with Docker Compose.
tags: [docker, devops, nodejs]
---
## 1. Orchestration with Docker Compose

Instead of manually building and linking individual containers, Docker Compose coordinates everything via a single file. To see how this works, the previous microservice was deployed and built using Docker Compose.

To revise, this setup demonstrates two independent Node.js services communicating over a virtual network: an Order Service that requests product details from a Product Service.

It assumed that you have installed and setup Docker on your work station. If you are using Ubuntu or one of the other Linux distros, setup is easy.

Docker Compose uses a yaml file for building/deploying/removing containers.

<b>docker-compose.yml</b>

```yaml
version: '3.8'

services:
  product:
    build: ./product
    ports:
      - "5001:5001"

  order:
    build: ./order
    ports:
      - "5002:5002"
    depends_on:
      - product
```

## 2. Networking

In the previous section, we had to create a network and attach the containers to it ourselves, manually from the command like. Docker Compose does this automatically for us. It creates a network with a name derived from the root folder that the Docker Compose file sits in. The containers are then attached to this network and given network alias names based on the names given in the Docker Compose file, in this case 'product' and 'order'.

## 3. Testing 

To test the microservice, follow these steps:

Open your terminal in the root microservices/ folder.

#### # Start the Containers 
Run the build and start command:

```sh
docker compose up 
```

#### # Call the Order Service 
Open a terminal window and test the microservice interaction by sending a POST request to the Order Service:

```sh
http://localhost:5002/order
```

#### # Expected Response:

```json
{"message":"Order created successfully!","item":"Laptop","total":999}
```

#### # Stop the Containers 

To stop the entire stack, press Ctrl + C in the main terminal window, or type docker-compose down.
