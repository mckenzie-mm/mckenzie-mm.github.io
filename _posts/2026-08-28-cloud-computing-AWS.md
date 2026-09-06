---
layout: post
author: jill
title: "Cloud Computing: EC2"
summary: Renting Virtual Machines over the internet; Amazon, AWS and EC2.
tags: [devops, aws]
---

## Historical Context

With the advent of the World Wide Web in 1993, it was just a matter of time before someone got the bright idea of renting Virtual Machines on demand over the internet. Amazon did just that in 2006. They were the first major provider to offer Virtual Machines as a commercial web service over the internet when they launched the public beta of Amazon EC2 (Elastic Compute Cloud) in August of that year. 

This was shortly after Amazon Web Services (AWS) was created at the beginning of the year as one of the first Cloud Service providers. Initally Amazon was simply an online book store created by Jeff Bezos, which diverged to an online store. I recall buying books from them and still do sometimes, though nowadays, I only buy electronic books, such as Kindle books. The days of paper copies have long since gone, at least for me, and I got rid of most of mine a few years ago.  Since those early days, Jeff has gone from selling books to cruising the mediterranean on a mega yacht. 

Missing from the original EC2 Virtual Machines was a persistent storage and static IP address. In 2008 AWS addressed this issue by providing static IP address, persistent storage and user selectable kernels. After this the beta label was dropped and EC2 became a viable alternative to physical machines. It has now replaced these in most organisations except for Defence and some Government departments, where security is a priority and the network is limited to secure, internal fiber optic.

## Elastic Cloud Computing (EC2)

One of the most confusing aspects of AWS is the plethora of multiple cloud services with weird names that are quite often three letters long. It seems to be a hallmark of AWS. For example Elastic Beanstalk (ELB), Simple Storage Service (S3), the list goes on. EC2 is one of these, and when I first started using AWS, it took me a while to work out that they were just Virtual Machines and not real machines. The name, Elastic Compute Cloud, is not exactly self explanatory. Other cloud providers adopt conventional naming that makes sense. For example Azure simply calls them Virtual Machines.

To buy an EC2 instance, you first define what you want to use as an image and then launch it. This is done online from your AWS account using a GUI (Graphical User Interface). AWS allows you to choose from a range of EC2 instances of various performance and prices and with the Operating System of your choice. Usually Linux for conveninence, but Windows Operating systems can also be used. You can then access the instance after it launches remotely using SSH, to run terminal commands, install software, transfer files, etc, just as if you were running the machine locally. 

![Alternative text for accessibility]({% link assets/images/fig-4.drawio.png %})
**Figure 1. Elastic Cloud Computing (EC2). Virtual Machines for hire from AWS.**

## A Practical Example 

All of this is best understood by way of example. This example was based on that from the Udemy AWS Certification Training course of Stephane Maarek. It shows how to launch a Guest OS and VM with a simple App, using AWS EC2. We will follow that order in setting it up:

#### 1. Select the Guest OS

#### 2. Select the VM

#### 3. Define the App


From the AWS console, navigate to the EC2 window. This show a table listing any EC2 instance that you might have. In the upper RHS, select Launch Instances to bring up the GUI for defining a new instance. 

#### # Select the Guest OS
There are a range of Operating System images available including macOS, Windows and multiple versions of Linux that can be used as a Guest OS. Leave the default image selected (Amazon Linux). 

![Alternative text for accessibility]({% link assets/images/ec2-1.png %})
**Figure 2. Operating system**

#### # Select the Virtual Machine (VM)

The VM that you want to use, is selected from the Instance type section. AWS provides for a huge range of instances of various performances, from very slow to extremely quick (and expensive). The default VM is "t3-micro" which is an entry level machine with only 1 GByte of RAM. It is one of the cheapest. Leave it selected in case you accidently choose an expensive one, forget to delete it afterwards, and get hit with a huge bill that you have no hope of paying.

![Alternative text for accessibility]({% link assets/images/ec2-2.png %})
**Figure 3. Virtual Machine Instance**

#### # Firewall - Select the ports for Network traffic 

This is usually optional, however since the App will be a Web Server it must be configured. Navigate down to the Network settings. Notice that by default the SSH port 22 is selected. This is to allow a secure connection console window from your local CPU. For a Web Server, the network port for HTTP must also be opened to allow the outside world to interact with it. Click the checkbox next to "Allow HTTP traffic from the internet" to open this port.

![Alternative text for accessibility]({% link assets/images/ec2-3.png %})
**Figure 4. Allowed ports for Network traffic**

#### # Define an App

Navigate to the Advanced details section and open it. A nice idea from AWS is the User Data field that is found within this section. This is where you can paste and edit a bash script (if you want to), that is run when the instance launches. For example a basic Web Server App, or anything else that you want to run after launch. Copy the script shown below into the user data field. This is the code for installing a simple Apache Web Server from the command line. The instance is now configured and ready to go for launching.


![Alternative text for accessibility]({% link assets/images/ec2-4a.png %})

<center>*</center>
<center>*</center>

![Alternative text for accessibility]({% link assets/images/ec2-4b.png %})
**Figure 5. User data field**


#### # Instance Summary

Launch the Instance. It usually takes around 10 seconds to launch. When it finishes, the new instance will be given a unique ID and assigned an arbitrary Public IP address. It will be listed in the table of instances. Clicking on the instance ID in the table opens a summary card describing it in extraordinary detail as shown below. For this simple example, the Public IP address from this summary is all that is required to interact with it (as shown outlined in red).

 ![Alternative text for accessibility]({% link assets/images/ec2-5.png %})
**Figure 6. Instance summary**

When you open the Public IP address, you will be greeted with the message from the Apache server that was defined in the user data field: <strong>"Hello World from EC2 instance"</strong> 

(you need to ensure that the browser is pointing to http and not https, otherwise you will not see it and will see only a spinning icon). This completes the practical example. Since AWS charges per hour for instances, it is recommended to delete this instance from the table of instances after you have finished playing with it. 

#### # Additional Settings

Its obvious from the detail given in the Instance Summary that there are a lot of parameters that can be configured besides those already mentioned. In order to keep it simple, several details were ommitted from this insallation, including SSH access keys for a remote console and name field. 

Since AWS have now introduced a console window directly in the GUI, access keys are less important than they once were. It's quicker an easier to use the online console. 

The name field is something descriptive that you use to describe what it is used for, such as "My Web Server" or "My Apache Server". An ID of "i-00813e75e6f5dbdd8" does not achieve this. For tutorials and workshops, a name makes sense, but when the instance is part of an auto-scaling group, where there are multiple instances managed by AWS, it is not something that anyone really cares about.

## EC2 Bare Metal Instances

In addition to Virtual Machines, AWS offers customers the choice of using Bare Metal Instances. These provide you with the actual physical CPUs, not virtualized CPUs. They do not run under a hypervisor, like non-Metal instances do. However I mention them in this post because they are accessed and managed over the internet in the same way as non-metal instances, through the AWS, EC2, GUI console. They are more expensive than non-metal instance because you are paying for the direct physical CPU. I guess "bare metal" is a reasonable description of a physical CPU, but the guy that coined the name must have been into heavy metal (rock music). I would have preferred if they were called physical CPU.

## Suggested Resources

This post was compiled and written using the following on-line resources; Wikipedia, AWS and the Udemy course of Stephane Maarek. In particular, the Udemy course offers a great overview of all of the AWS services and is well worth watching even if you don't plan on sitting for the Certification exam. It is regularly updated and very well researched. In addition, AWS themselves, provide excellent documentation for all of their services.

[ https://en.wikipedia.org/wiki/Amazon_Elastic_Compute_Cloud ](https://en.wikipedia.org/wiki/Amazon_Elastic_Compute_Cloud)

[ https://www.udemy.com/course/aws-certified-developer-associate-dva-c01/ ](https://www.udemy.com/course/aws-certified-developer-associate-dva-c01/) 

[ https://docs.aws.amazon.com/ ](https://docs.aws.amazon.com/)

