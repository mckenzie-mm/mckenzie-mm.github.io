---
layout: post
author: jill
title: "AWS Scaling: Practical Notes"
summary: Horizontal Scaling using Auto Scaling Group and Elastic Load Balancer from AWS.
tags: [devops, aws]
---

## A Practical Example

In the previous post, I summarised the current methods of scaling Virtual Machines and how AWS goes about doing it. This section contains notes that I have taken which document the procedure for Horizontally Scaling EC2 instances with AWS. This consists of two steps:

1. Create a launch template that will be used by the Auto Scaling Group (ASG) to create the EC2 instances as it scales in and out.

2. Create the ASG. This consists of selecting the EC2 template defined above. As scaling will be done automatically based on load, an Elastic Load Balancer (ELB) will be required. This is created at the same time as the ASG. 

## Step 1. Creating a Launch Template

Navigate to the EC2 section in the AWS console. Select "Launch Templates from the LHS menu". This will show a list of the Launch Tables that have been created, if any. 

Click on the "Create launch template" button in the upper RHS. This brings up the GUI for creating the template. 

#### # Template Name

Unlike EC2 instance, a name is required and it must be unique. Type in a name of your choice.

![Alternative text for accessibility]({% link assets/images/t1.png %})
**Figure 1. Launch template name (required)**

#### # Operating System

The Operating System is not selected by default. Click on "Quick Start" and select the "Amazon Linux" OS that was used in the previous example. 

![Alternative text for accessibility]({% link assets/images/t2.png %})
**Figure 2. Operating System**

#### # Instance Type
AWS offers the same instances that come when defining an individual EC2 instance. Scroll down to the "Instance type" and select the "t3.micro" virtual machine instance. This comes within the Free Tier classification (meaning its cheap).

#### # Firewall (Security Group)

AWS calls their Firewall "Security Group", For a template, the Security Group is created slightly differently than when creating an EC2 instance. You have to do more work. Scroll down to the "Network settings" and select "Create security group". Both the name field and description are required. Click on the "Add security group rule". From the drop down menus in the "Inbound Security Group Rules", elect HTTP for the "Type" and "Anywhere" for the "Source type". This allows http traffic from any source.

![Alternative text for accessibility]({% link assets/images/t4.png %})
**Figure 3. Firewall for http traffic**

#### # User Data Launch Script

Once again we will make use of the User Data to create a simple webserver for the EC2 instances when they are launched. I have used the bash script from Stephan directly (returns the hostname). 
Scroll down to the "Advanced details" and paste this script into the "User data" field. The template is now ready to use. Click on "Create launch template" at the bottom.

```sh
#!/bin/bash
# Use this for your user data (script from top to bottom)
# install httpd (Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```


<hr>

## Step 2. Creating the ASG

#### # ASG Name
From the same EC2 console window, select the "Auto Scaling Groups" from the LHS menu. Click on the "Create Auto Scaling group". Enter a scaling group name, and from the launch template, select the template that was defined in the first step. Click on "Next".

![Alternative text for accessibility]({% link assets/images/asg-1.png %})
**Figure 4. Auto Scaling Group name and template**

#### # Availability Zones
An Availability Zone is the name given to the data center where the physical hardware to run the EC2 instances is located (among other services). For relaibility, the Auto Scaling Group is required to be available in at least two zones (in case one goes down). From the Network Section, select all three Availability Zones that are available, scroll to the bottom and select "Next".


![Alternative text for accessibility]({% link assets/images/asg-2.png %})
**Figure 5. Availability Zones (min of 2 required by AWS)**

#### # Load Balancer
In the "Load balancing" section, you have the choice of proceeding without a load balancer, using an existing load balancer or creating a new load balancer. Select "Attach to a new load balancer". 

The example being built here will be a Web Server (http traffic). The correct Load Balancer for http traffic is the “Application Load Balancer”. Leave the default type of Load Balancer as "Application Load Balancer" and select "internet-facing", to allow http traffic into it. 

Under the Listeners section of the Load Balancer, you have to point it to the name of the ASG that is being created. From the dropdown "Default routing (forward to)", choose "my-scaling-group-1", which corresponds to that of this ASG that we are creating; AWS used the name we entered and added a 1 on the end.

![Alternative text for accessibility]({% link assets/images/asg-3.png %})



<center>*</center>
<center>*</center>
<center>*</center>



![Alternative text for accessibility]({% link assets/images/asg-4.png %})
**Figure 7. Attaching and Routing a Load Balancer to the ASG**

#### # Maximum, Minimum and Desired Capacity

The final stage of creating an ASG is to define the size of the group. From the "Group size", enter 2 for the desired capacity and for the scaling, set the maximum and minimum limits as 3 and 1 respectively. Note that after the ASG has been created these can be changed from the console window manually.


Scroll to the bottom and select "Skip to review" and at the bottom, click on Create Auto Scaling group. This completes the setup. It takes some time for everything to initialise.


![Alternative text for accessibility]({% link assets/images/asg-5.png %})
**Figure 8. Group Size**

<hr>

#### # Testing the ASG

Wait for the ASG to finish initialising. It will be slow because of the three Availability Zones and the two EC2 instances that have to be launched. 

To test the Auto Scaling Group, navigate to the Load Balancer section of the EC2 console window using the LHS menu. The Auto Scaling Group will be pointed to by the Load Balancer, which by default has the same name as the Auto Scaling Group (with a numerical number on the end, in this case 1). Click on this Load Balancer in the list of load Balancers to bring up the description of it.

![Alternative text for accessibility]({% link assets/images/test-1.png %})
**Figure 8. Load Balancer Description**

Locate the DNS name of the Load Balancer. When AWS creates a load balancer it assigns a DNS name instead of an IP address. Copy this DNS name and paste in the browser window (with https changed to http).

Refreshing the browser window will toggle the output to each of the EC2 instances in the scaling group in turn, which will return their respective IP addresses as shown below.

![Alternative text for accessibility]({% link assets/images/test-2.png %})

<center>▲</center>
<center>▼</center>

![Alternative text for accessibility]({% link assets/images/test-3.png %})
