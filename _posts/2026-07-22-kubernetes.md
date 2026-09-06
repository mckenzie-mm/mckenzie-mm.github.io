---
layout: post
author: ted
title: "Kubernetes"
summary: Automates the deployment, scaling, & management of containerized (Docker) applications.
tags: [kubernetes, docker, devops]
---
## Introduction

Scaling in response to load is the key difference between a production and a development environment. In earlier posts, I looked at the scaling of Virtual Machines using Load Balancers and Auto Scaling Groups. Scaling containers (docker) is more complex. Containers can scale vertically within a Virtual Machine, and then when it's compute capacity is used up, additional Virtual Machines can be added/and or removed.

For scaling of containers, Kubernetes dominates the market. A simplistic view describing the main concepts of Kubernetes is shown below. The code behind this simplistic view is far more complex. Fortunately we don't need to look at it deeply to work with Kubernetes.

![Alternative text for accessibility]({% link assets/images/kubernetes.drawio.png %})
**Figure 1. Simplified Kubernetes Schematic. Main components for scaling of Docker Containers**

Kubernetes was announced by Google on June 6, 2014. (June 6, is my birthday). The project was conceived and created by Google employees Joe Beda, Brendan Burns, and Craig McLuckie. Others at Google soon joined to help build the project including Ville Aikas, Dawn Chen, Brian Grant, Tim Hockin, and Daniel Smith. Other companies such as Red Hat and CoreOS joined the effort soon after, with notable contributors such as Clayton Coleman and Kelsey Hightower. It is now open source for anyone brave enough to look.

## Nomenclature

As mentioned, I have simplied the technical explanation of Kubernetes. Kubernetes uses its own nomenclature. This together with highly technical documentation, gives the impression of it being far more complicated than what it really is. It's written for an Engineer working on Kubernetes code at a low level rather than in layman's terms. To see what I mean, consider this quote from Wiki:

<i>"The design and development of Kubernetes was inspired by Google's Borg cluster manager and based on Promise Theory. Many of its top contributors had previously worked on Borg; they codenamed Kubernetes "Project 7" after the Star Trek ex-Borg character Seven of Nine and gave its logo a seven-spoked ship's wheel (designed by Tim Hockin). Unlike Borg, which was written in C++, Kubernetes is written in the Go language."</i>

Amazing. An Engineer working with Kubernetes to deploy their microservice (or application) doesn't need to know this level of detail, or the level of detail that the documents go into. There are too many vague terms with weird names that one has to look up when first starting. For example, cluster, node, control plane, pods. My explanation of the main components is as follows.

<b>Cluster:</b> The name given to the overall system.

<b>Control Plane:</b> A virtual machine running the code for controlling the scaling of containers and the virtual machines they sit on.

<b>Node:</b> A virtual machine for running containers.

<b>Pods:</b> A container (the name pods, is to account for the fact that you might want two or more running side by side).

## Kubernetes Control (kubectl)

Kubernetes does not have a Graphical User Interface (GUI), like those we saw earlier with AWS. Instead Kubernetes provides a command line tool for communicating with a Kubernetes cluster's control plane, using the Kubernetes API. This tool is called 'kubectl' and is (usually) installed on your local machine. 

The kubectl tool is the primary interface for creating, inspecting, updating, and deleting Kubernetes objects. It complements the Kubernetes Components that run inside your cluster and the Kubernetes API that those components implement. Whether you run kubectl from your laptop or from a Pod inside the cluster, it sends requests to the API server. 

![Alternative text for accessibility]({% link assets/images/kubectl.large.drawio.png %})
**Figure 1. Local or Onsite Control of Kubernetes using Kubectl**

## Environments

The kubectl tool connects to the API server and authenticates using the cluster, user, and context defined in your kubeconfig file. When you run kubectl (from outside a cluster), it uses the kubeconfig file to find the API server address and credentials. 

The kubectl tool communicates with your cluster through the Kubernetes API. For configuration, kubectl looks for a file named config in the $HOME/.kube directory. You can specify other kubeconfig files by setting the KUBECONFIG environment variable or by setting the --kubeconfig flag.

When you run a command, kubectl translates your intent into one or more HTTP requests to the Kubernetes API. The API server validates each request, applies it to the cluster state stored in etcd, and returns the result. This means every kubectl action, whether creating a Deployment or reading logs, follows the same API-driven path.

Because your kubeconfig can define multiple clusters, users, and contexts, you can use kubectl to switch between clusters without reconfiguring your environment. Run kubectl config use-context to change the active context.

![Alternative text for accessibility]({% link assets/images/cloud.drawio.png %})
**Figure 1. Local (Development) versus Cloud (Production) Environments of Kubernetes.**



