---
layout: post
author: Mark
title: "Scaling of Virtual Machines"
summary: Vertical & Horizontal Scaling of Virtual Machines. Handling growing amounts of load without losing performance.
tags: [devops, aws]
---

## Introduction
As time changes, the demand on a computer system may increase or decrease. If it increases, a point is reached where it can no longer respond quickly enough resulting in a poor customer experience (we have all been there with that headache). Conversely, it it decreases, the Service Provider is paying for extra resources that are not needed to achieve a satifactory customer experience. Scaling is defined as the process of handling this change in demand. There are two types of scaling; Vertical and Horizontal. 

#### Vertical Scaling

Vertical Scaling is the simplest and easiest to understand. The Physical Machine (and/or Virtual Machine) is simply replaced with one of larger capacity with bigger RAM, faster processor etc (or alternatively decreased for smaller demand). The disadvantage is that this scaling is manual, with a slow response to load changes.

![Alternative text for accessibility]({% link assets/images/scale-1.drawio.png %})
**Figure 1. Vertical Scaling**


#### Horizontal Scaling

In Horizontal Scaling, the number of instances is increased and the load is distributed across these instance. 
Horizontal Scaling is more complex because a controller must be provided to distribute the load among the instances and manage the increase or decrease in the number of instances according to demand.

![Alternative text for accessibility]({% link assets/images/scale-2a.drawio.png %})
**Figure 2. Horizontal Scaling**

## AWS Vertical Scaling

For scaling vertically, it is up to the user to select an EC2 instance from the range provided by AWS of a suitable size and performance for the anticipated load and to change this instance size according to demand. AWS provides instances in six categories: <i>General Purpose</i>, <i>Compute Optimized</i>, <i>Memory Optimized</i>, <i>Accelerated Computing</i>, <i>Storage Optimized</i> and <i>HPC Optimized</i>. In order to avoid overcomplication, only the first one of these will be considered here. 

#### General Purpose Instances

The AWS General Purpose instances are the most cost effective, particularly the T series for demonstration sites. According to AWS, they provide a balance of compute, memory and networking resources, and can be used for many workloads. They are good for applications such as web servers, code repositories, and small-to-medium databases. There are three types of General purpose instances to choose from.
* M Series (Mainline). The M family provides consistent, non-blended performance with a standard ratio of 4 GiB of RAM for every 1 vCPU. It is recommended by AWS as the baseline default for production environments.

* T Series (Burstable). The T family uses a CPU credit model designed for applications that sit idle most of the time but need full computing spikes occasionally. They are highly cost-effective for staging environments, low-traffic sites, and microservices.

* Mac Series (macOS Specific). Built directly on physical Apple Mac hardware inside the AWS Nitro system.

The table below shows an example of Instance Performance vs Size for the M9g series, from medium to 48xlarge and metal. They deliver the best price performance in Amazon EC2 for general purpose workloads. Note the increase in RAM in direct proportion to the vCPU (as stated by AWS).

**Table 1. Instance Size VS Performance**

| Size | n x vCPU | RAM (GiB) | BW (Gbps) | USD per hour
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| m9g.medium | 1 | 4 | Up to 15 | 0.05067 |
| m9g.large | 2 | 8 | Up to 15 | 0.10134 |
| m9g.xlarge | 4 | 16 | Up to 15 | 0.20268 |
| m9g.2xlarge | 8 | 32 | Up to 17 | 0.40536 |
| m9g.4xlarge | 16 | 64 | Up to 17 | 0.81072 |
| m9g.8xlarge | 32 | 128 | 17 | 1.62144 |
| m9g.12xlarge | 48 | 192 | 25 | 2.43216 |
| m9g.16xlarge | 64 | 256 | 34 | 3.24288 |
| m9g.24xlarge | 96 | 384 | 50 | 4.86432 |
| m9g.48xlarge | 192 | 768 | 100 | 9.72864 |
| m9g.metal-48xl | 192 | 768 | 100 | 9.72864 |

If an m9g.48xlarge is rented for one year continuously, this equates to a bill of $85,000 USD per year or $120,000 AUD. For one day's rent it is $233 USD. It's an extreme example but it gives you some idea of the costs involved in inadvertently creating unused resources. I have been burnt before, but not for anything like that amount. It's easy to do when you are playing around and get distracted. I set up an alert on my AWS account, so that if my bill exceeds a certain level, I will be notified by email.

## AWS Horizontal Scaling

AWS handles Horizontal Scaling with two configurable components; Auto Scaling Group (ASG) and Elastic Load Balancer (ELB). Note the use of the three letter naming convention again.

The Auto Scaling Group is responsible for scaling the EC2 instances, from a set minimum to a maxium number of instances. It works in conjunction with the Elastic Load Balancer. The Elastic Load Balancer points to the Auto Scaling Group. It divides the incoming network traffic amongs the EC2 instances of the Auto Scaling Group, the default being in a round robin manner.

![Alternative text for accessibility]({% link assets/images/aws-alb.drawio.png %})
<br>
**Figure 3. AWS Horizontal Scaling**


#### Auto Scaling groups

Auto Scaling Groups (ASG) is how AWS handles Horizontal Scaling. Scaling may be done manually without an Elastic Load Balancer by manually changing the settings. Alternatively it can be done by an Elastic Load Balancer. Instead of defining a single EC2 instance with the AWS GUI, now you define a group of instances based on a template. The template defines the type of EC2 instance that you want to use, in a similar way to that done previously in the last post. For the group of instances, you define a minimum number to use and a maximum number that are allowed.

![Alternative text for accessibility]({% link assets/images/asg.drawio.png %})
**Figure 3. AWS Auto Scaling Group**

The size of an Auto Scaling group depends on the number of instances that you set as the desired capacity. You can adjust its size to meet demand, either manually or by using automatic scaling.

An ASG starts by launching just enough instances to meet its desired capacity. It maintains this number of instances by performing periodic checks on the instances in the group. The ASG continues to maintain a fixed number of instances even if an instance crashes. If this happens, the group launches another instance to replace it.

#### Elastic Load Balancers

The second component of AWS Horizontal Scaling is the Elastic Load Balancer (ELB). In addition to distributing the incomming traffic across the instances of the ASG based on load, AWS combines routing as well with the ELB. Because of the routing, it can be used independently of an Auto Scaling Group and simply point to groups of EC2 instances of fixed size based on a routing rule.

The way in which the traffic is routed defines the type of load balancer to use. AWS offers three alternatives:

1. Application Load Balancer (ALB)

2. Network Load Balancer (NLB)

3. Gateway Load Balancer (GLB)

![Alternative text for accessibility]({% link assets/images/app-lb.drawio.png %})
**Figure 4. AWS Horizontal Scaling**

The classification of these load balancer is complex (unless you regularly program low level network packets in C-language). They are defined by the layer of the Open Systems Interconnection (OSI) model of the network packets that they operate on; the packet headers. This post will only look at the first one of these, Application Load Balancers, which is the one I am most familiar with, the easiest to understand and in my opinion, the most useful. To quote from AWS:

<i>"ALBs, NLBs, and GLBs operate at different layers of your network communication. An ALB operates on OSI layer 7 and allows for application-level traffic manipulation and routing. An NLB operates on layer 4 for network-level traffic management based on ports and IP addresses. A GLB works across layers 3 and 7, providing balancing and routing services at the network level along with gateway functionality."</i>

![Alternative text for accessibility]({% link assets/images/load-balancer.png %})

By operating on Layer 7, the Application Load Balancer (ALB) has access to HTTP and HTTPS headers. This allows you to route requests to different backend services based on the URL path. For example, you might want to send requests that include "/api" in the URL path to one group of servers (AWS calls these target groups) and requests that include "/mobile" to another. Routing requests in this fashion allows you to build applications that are composed of multiple microservices that can run and be scaled independently.

As another example, consider an ecommerce application with multiple target groups, that include a product directory, a shopping cart, and checkout functions. The ALB sends requests for browsing products to servers that contain images and videos but don’t need to maintain open connections. By comparison, it sends shopping cart requests to servers that maintain many client connections and save cart data for a long time.

The ALB has a listener component that checks for connection requests from clients. You can define rules for a listener that determine how the load balancer routes requests to its registered targets. A target group sorts registered targets into groups. You can define rules to route common traffic to an entire group. For example, you can create a target group for general requests and other target groups for requests to the microservices for your application.

