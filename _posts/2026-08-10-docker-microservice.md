---
layout: post
author: Mark
title: "Docker Microservice"
summary: A simple example of a microservice built with docker demonstrating a producer/consumer application.
tags: [docker, devops, nodejs]
index: 1
---
## 1. Getting Started

This setup demonstrates Docker networking  using microservices. It consists of two independent Node.js containers communicating over a virtual network: an Order container that requests product details from a Product container. 

It assumed that you have installed and setup Docker on your work station. If you are using Ubuntu or one of the other Linux distros, setup is easy.

## 2. Project Structure

Create a directory named microservices with the following files:

```
microservices/
├── product/
│   ├── index.js
│   └── Dockerfile
└── order/
    ├── index.js
    └── Dockerfile
```


## 3. Product Container (The Dependency)

This container manages product data and listens on port 5001. The code details are as shown below.

<b>product/index.js</b>

```js
const http = require('http');

const products = {
  "1": { name: "Laptop", price: 999 },
  "2": { name: "Mouse", price: 25 }
};

const server = http.createServer((req, res) => {
  const urlParts = req.url.split('/');
  const id = urlParts[urlParts.length - 1];
  
  res.setHeader('Content-Type', 'application/json');
  if (products[id]) {
    res.writeHead(200);
    res.end(JSON.stringify(products[id]));
  } else {
    res.writeHead(404);
    res.end(JSON.stringify({ error: "Product not found" }));
  }
});

server.listen(5001, () => console.log('Product container running on port 5001'));
```

<b>product/Dockerfile</b>

```docker
FROM node:18-alpine
WORKDIR /app
COPY index.js .
EXPOSE 5001
CMD ["node", "index.js"]
```

Open a terminal in the product folder and build and run the product container.

```sh
$ docker build -t product .
$ docker run -d -p 127.0.0.1:5001:5001 product
```

Get the IP address of the Product container by inspecting the bridge network.

```sh
 $ docker network inspect bridge

"Containers": {
  "ca3f2670b144": {
      "Name": "elegant_clarke",
      "EndpointID": "d26765545cd689058c40429ca8e950fd87b230ecf8e9e63907b6da263df8667e",
      "MacAddress": "02:42:ac:11:00:02",
      "IPv4Address": "172.17.0.2/16",
      "IPv6Address": "" 
  }
}
```

## 4 Order Container (The Consumer)

This container creates orders, listening on port 5002. Normally an order would be created through a POST request, but for simplicity we are using http GET to avoid having to use a CURL command. The ProductIP in this code, 172.17.0.2, was assigned to the product container by docker (which we retrieved with the network inspect command).

<b>order/index.js</b>

```js
const http = require('http');

const productIP = "172.17.0.2";

const server = http.createServer((req, res) => {
  res.setHeader('Content-Type', 'application/json');
  
  if (req.url === '/order') {
    // Hardcoded request to look up Product ID 1 from the other microservice
    http.get(`http://${productIP}:5001/1`, (response) => {
      let data = '';
      response.on('data', chunk => data += chunk);
      response.on('end', () => {
        const product = JSON.parse(data);
        res.writeHead(201);
        res.end(JSON.stringify({
          message: "Order created successfully!",
          item: product.name,
          total: product.price
        }));
      });
    }).on('error', (err) => {
      res.writeHead(500);
      res.end(JSON.stringify({ error: "Failed to reach Product Container" }));
    });
  } else {
    res.writeHead(404);
    res.end(JSON.stringify({ error: "Route not found" }));
  }
});

server.listen(5002, () => console.log('Order container running on port 5002'));
```

<b>order/Dockerfile</b>

```docker
FROM node:18-alpine
WORKDIR /app
COPY index.js .
EXPOSE 5002
CMD ["node", "index.js"]
```
#### # Start the Order Container

Open a terminal in the order folder and build and run the order container from the Dockerfile.

```sh
$ docker build -t order .
$ docker run -d -p 127.0.0.1:5002:5002 order
```
#### # Call the Order Service

Navigate to the localhost on port 5002 on your PC to place an order.
```
http://localhost:5002/order
```

#### # Expected Response:
```
{"message":"Order created successfully!","item":"Laptop","total":999}
```

## 5 Docker Name Server 

A name server is a specialized server that translates easy-to-read web addresses (like google.com) into computer-friendly IP numbers. It acts like a phone book, holding the records that point to the correct IP address.

Using the IP address directly in the Order container has obvious drawbacks. It is dependent on the product container IP address, which is not fixed between builds. Every time a new product container is built the code must be updated with the product container IP address. To address this, Docker has built-in Name Server capability. This is managed with the docker network command. Although not exactly a name server it behaves very similar.

To see this in action, start by stopping and removing the order and product containers.

```sh
$ docker stop <container_id_or_name>
$ docker rm <container_id_or_name>
```

Create a new network called service, using the docker network command. This will be the service for the name mapping.

```sh
$ docker network create service
```

Although Docker creates this as a network, it will have an associated name server with it. By default, the bridge network does not have this, which is why we need to create one. Run the product container again and attach it to the service with

```sh
$ docker run -d --network service --network-alias product product
```
The network flags '--network service' and '--network-alias product', attaches the product container to the 'service' network and assigns the network alias 'product' to it. Docker will now map the name 'product' to the IP address.
```sh
product | 172.17.0.2
```


In the order container, index.js, replace the hard coded IP address with

```js
const productIP = "product";
```
and rebuild the docker image for it
```sh
$ docker build -t order .
```

Run the order container again and this time point it to the name server associated with 'service' by attaching it to this network.

```sh
docker run -dp 127.0.0.1:5002:5002 --network service order
```

The network flag '--network service', attaches the order container to it without a network alias. Navigate to the localhost on port 5002 on your PC again to place an order.
```
http://localhost:5002/order

{"message":"Order created successfully!","item":"Laptop","total":999}
```

If you do a network inspect of both 'service' and 'bridge' networks, both the order and product containers now run in the 'service' network and have been removed from the 'bridge' network.

## 6 Microservices

To use docker containers as microservices it is necessary to have a 'service' network to map the container names instead of their IP addresses.

The commands to set up a microservice using docker alone are quite tedious and involved and open to error. In the next post, I describe Docker Compose, which is designed specifically for managing multiple containers.
