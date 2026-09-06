---
layout: post
author: ted
title: "Kubectl with yaml files"
summary: An extract of the transcript from a course by Mischa van den Burg on Kubectl with yaml files.
tags: [kubernetes, docker, devops]
---
<i>This post continues the explanation of the operation of the kubectl command. Again, I have taken an extract from the transcript of one of Mischa's talks and made minor additions, ommisions and edits. Mischa van den Burg (https://mischavandenburg.com/) is a dev ops engineer from the Netherlands who makes a living from his video/udemy courses.</i>

<i>"How to Make Your First Kubernetes Deployment in Minutes!"</i>
<br><br>
<hr>
## Transcript (Mischa van den Burg)
#### How to create your first Kubernetes pod. 

So you have your Kubernetes cluster up and running. Now what? If you want to use Kubernetes, you need to deploy applications. And pods are one of the ways that we can do that. Pods are the fundamental building blocks of Kubernetes. And they're surprisingly simple to create once someone shows you how to do it. In this video, I'll demonstrate exactly what pods are and the three easiest ways to deploy them.

#### Quickest Method
Let's start with the quickest way to get a pod running. So the simplest way to create pods is using the kubectl run command. This is perfect for quick testing or when you're just getting started. So what we're going to do is we're going to run a quick nginx pod called nginx-demo. So when I run this command
```text
$ kubectl run nginx-demo --image=nginx
---
pod/nginx-demo created
```
we now see that a pod called nginx-demo has been created. Now, if you want to check out which pods are running here currently, I can run the command 
```text
$ kubectl get pods
---
NAME         READY   STATUS    RESTARTS   AGE
nginx-demo   1/1     Running   0          5m43s
```
So, if I check out these pods, nginx-demo, we see already that the pod is already running. So this is the fastest way to get a pod up and running if you're just starting starting out if you're testing out things and you just want to get something up and running. 

So the the cubectl run command is actually quite expansive. You can do quite a lot of things with it. So if you do 
```
$ kubectl run --help 

# provides a long list of examples showing how to apply the run command
```

then you see that it gives you a few very interesting examples here. So you can expose ports, you can override things, you can add labels, you can change the images. There are all sorts of things that you can do. Set environment variables just from this kubectl run command. So for example you can set environment variables. You can expose a port on the container and you can add labels to the pod just from this command. So if I wanted to expose a port 80 and set an environment variable, I can use this command. 

```text
$ kubectl run nginx-env --image=nginx --port=80 --env="NGINX_HOST=example.com"
---
pod/nginx-env created
```
So that's a new pod that's going to be created with the image nginx and then I add the flag port 80 and then the NGINX_HOST is example.com. So if I copy that and I run this. So now if I do 
```text
kubectl get pods 
---
NAME         READY   STATUS    RESTARTS   AGE
nginx-demo   1/1     Running   0          22m
nginx-env    1/1     Running   0          2m36s
```
I see I have a second pod running called nginx-env. Now the command to to find more information of pods is kubectl describe. So if I do 
```text
$ kubectl describe pod nginx-env 
---
# provides a detailed description of the pod
```
here we see some more information. So you can see the Name, which Namespace it is in when it was started, which labels it has etc. But here we also see that it has this port property and here we see it's exposing port 80. So this is very useful. You can just do this from the run command and you can expose the ports like that. So, I'm not going to go into all of the information just yet, but for now it's it's enough to know that we have set a port and also our environment variable over here. We see that the environment is NGINX_HOST: example.com, just like we specified. So there's a lot that you can do from this CLI command kubectl run. That is the fastest way how to run a pod.

#### What are Pods
Now that we've seen how to quickly create a pod, let's understand what pods actually are. A pod is the smallest deployable unit in Kubernetes. It's the basic building block for running your applications. But here's the important part. A pod is not a container. Instead, it's a collection of containers and their associated resources that are deployed together as a single packaged unit.

The word pod in Kubernetes comes from a pod of whales, which is just a group of whales. Just like multiple whales swim together in the ocean, multiple containers can run together in a pod. This nautical theme is consistent throughout Kubernetes. In fact, the word Kubernetes itself means helmsman in Greek. It's the person who steers a ship.

So a pod is not a container but a pod is a collection of containers. A pod can have one or more containers and these containers will share the same network namespace, meaning that they can communicate via local host. They share the same storage volumes and they have the same life cycle. They are created and destroyed together. Think of a pod as a logical host or group for your containers. In traditional deployments, you might run multiple related processes on a single VM or physical server. In Kubernetes, those related processes would run as containers within a pod. Pods are also ephemeral by nature. This means that they're temporary and they can be terminated at any time. So when a pod dies, Kubernetes doesn't automatically bring back that exact pod. Instead, it will recreate a new version of the pod, which might even be placed on a different node or a different server. 

#### Components of a Pod
A pod definition typically includes several key components.

<b>- Containers.</b> The actual application containers that will run inside the pod. As mentioned, most pods have just one container, but multi-container pods are used for tightly coupled applications. 

<b>- Init Containers.</b> Next, we have init containers. These are specialized containers that run and complete before your application containers start. For example, you might use an init container to check if a database is ready before starting your application. 

<b>- Networking.</b> Third is networking. Every pod gets its own unique IP address. All containers within a pod share this IP address and can communicate with each other using local host. 

<b>- Storage.</b> Fourth is storage. Pods can have volumes attached to them and these volumes can be shared among all containers in the pod allowing them to share data. 

#### Creating pods with YAML
So now that we understand what pods are conceptually, let's look at a more powerful way to create them. So let's dive into creating pods using YAML. While kubectl run is convenient for simple things, and when you're testing out things you want to get something up and running quickly, in the real world you typically will use code to talk to the API server and to provision resources on the Kubernetes cluster. And you do that through what is called YAML manifests. So let's take a look at this simple YAML manifest for a pod: 
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: web-app
  labels:
    app: web
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```
We see that it has a kind of pod. It has a label with of app web. It is the name of the pod is web app. And then here we give it a list of containers that we want to provision. So in this case we are only doing one container with the name nginx and the image nginx:latest, and we are also exposing the port 80 again. So this what you see here this ports container port 80 is equivalent to running the kubectl run with the port 80 flag. So the way we can use this is I will copy this over and let me just delete all of the pods that we had. So you can do 

```text
$ kubectl delete pods --all
``` 
So now it is deleting all of the pods. And if I now do 
```text
$ kubectl get pods 
---
No resources found in default namespace.
```
then we see there are no resources left. So I will just create a new pod.yaml file by writing nvim pod.yaml. And here is my pod.yaml file where I pasted in the exact same YAML that I had here in my little document. So now we have a YAML manifest to create a pod and how are we going to deploy this? 

Well, for this we use the kubectl apply command. You do 

```text
$ kubectl apply -f pod.yaml
---
pod/web-app created
```
<b>Apply vs Create:</b> you can also do kubectl create with the -f flag, but apply is better because if you use apply and you make changes to the file, it will actually diff the resources. It will pick up the differences and then it will apply them accordingly. So always use the apply command. 

So if I now do 
```text
$ kubectl get pods 
---
NAME      READY   STATUS    RESTARTS   AGE
web-app   1/1     Running   0          14s

```
we see that my we have our web-app pods here. Now if I do 
```text
$ kubectl get pods --show-labels 
---
NAME      READY   STATUS    RESTARTS   AGE   LABELS
web-app   1/1     Running   0          63s   app=web
```

for example then we see that this label that it has gotten is app is equals web just like we put in our yaml manifest. 

So that is the second way how we can deploy pods on kubernetes. So the advantage of yaml is that it gives you complete control over every aspect of your pod and you can version control these files and re-use them across different environments. And this is where you can get into git ops, which I have created videos about on my channel as well that you can look up. 

#### Multiple Containers

So let's look at an example with multiple containers and a shared volume. So this is a more elaborate one. So we have again the list of containers here but here instead of just one we have an extra container here. 
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: nginx
    image: nginx:latest
    volumeMounts:
    - name: shared-data
      mountPath: /usr/share/nginx/html

  - name: content-generator
    image: alpine
    command: ["/bin/sh", "-c"]
    args:
      - while true; do
          echo "$(date) - Content updated" > /data/index.html;
          sleep 30;
        done
    volumeMounts:
    - name: shared-data
      mountPath: /data
  volumes:
  - name: shared-data
    emptyDir: {}
```
In this Multi-container pod with shared volume case, it's a content generator that will create some content for the index html for the nginx pod. So you are basically giving it the content that the nginx web server should serve to the user. And in order to have the data being shared across these pods, we are giving it a volume and volume mount. So this way these containers can share the same storage. 

#### Generate YAML automatically with --dry-run

Now we have been creating YAML files just from a text editor. But the cool thing is with cubectl we can also generate YAML from the commands. So it's very helpful when you're learning Kubernetes that you can use the dry run command and then output things as YAML. So let's check out what that looks like. 
```yaml
$ kubectl run nginx-pod --image=nginx --dry-run=client -o yaml > pod.yaml
```

So I have this command here that I copied over and I will just delete the pods again and I will remove my pod.yml. YAML and if I now do this so what we see here is kubectl run again we're going to run nginx pod with the image nginx but here there is this dry run is client. So it will dry run this command. It won't actually apply it to the cluster it will just do as if. And then we output this as yaml into a file. So let's check out what this looks like. 
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: nginx-pod
  name: nginx-pod
spec:
  containers:
  - image: nginx
    name: nginx-pod
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
```
And here we see that we have generated the the YAML object. We didn't have to type all of this out. It has generated for us. And here we see it has a few extra things that we didn't have in our first YAML. So this also means that they are not required. So I can just delete this to clean it up a little bit. But here here we see that we have a very simple pod. That we generated from the cubectl command. 

And you can do the same with services with deployments etc. You can do so many things. You can generate so much YAML just from the kubectl command, which is extremely useful. You can generate the YAML structure and then customize it to your needs before applying it. 

#### Why kubectl create doesn’t work for pods 

And you might be wondering why don't we just use the cubectl create command. So there are there is also the kubectl create command that we can check out help. So why were we using cubectl run if there is a cubectl create command because creating pods is what we're doing right? Well let's check it out. Let's see what happens if I run 

```text
$ kubectl create pod nginx-pod --image=nginx
---
error: unknown flag: --image
See 'kubectl create --help' for usage.
```
So kubectl does not accept the the pod object. It is only accepting things like cluster roles, cron jobs, deployments, jobs but not pods. Pods are separate from that and they have their own command called kubectl run. Now the truth is in real production environments you rarely create standalone pods directly. Instead you typically use deployments, which manage pods for you and provide additional features like scaling and rolling updates. However, when you are just starting out or testing applications, running pods is where you can begin. Understanding pods is essential since they are the foundation of all workloads in Kubernetes. 