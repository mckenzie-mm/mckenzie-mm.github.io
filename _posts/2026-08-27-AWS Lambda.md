---
layout: post
author: jill
title: "Cloud Computing: Lambda"
summary: "Functions as a service, aka Serverless."
tags: [devops, aws]
---

## Lambda Functions

AWS introduced Lambda Functions in 2014 as a potential alternative to EC2 for many applications. Their selling point is that they allow significantly lower costs to be achieved. Lambda Functions are classified as Functions as a Service, whereas EC2 are Servers on demand. For this reason, the term "Serverless" has been coined to describe them and is frequently used in place of Lambda.

The naming hides what they actually are. Lambda Functions are Virtual Machines that run a function that you define in the language of your choice. They are launched and run only when the function is called, which is why they are much cheaper. With an EC2 instance, you pay for the idle time of the Virtual Machine, whereas with Lambda Functions you don't. 

Lambda creates the Virtual Machines on a fleet of EC2 instances. These EC2 instances are bare metal Nitro instances which are launched in a seperate inaccessible AWS account. You may recall from the post on EC2 that bare metal instances are the actual physical CPUs.

The achilles heel of Lambda Functions is the delay that occurs when the function is first called due to the time it takes for the Virtual Machine to be launched. This is called a cold start, because there is no warm execution environment already available to take on the work. After the Virtual Machine has been launched, it stays active for a short period of time (up to 15 mins) during which subsequent calls to the function are considerably quicker. 

In an effort to overcome cold starts, AWS developed Micro Virtual Machines (MicroVM), which are now open sourced and available under the name Firecracker. These are a type of Virtual Machine designed with a minimum of operating system code to enable fast launch times (booting) of the order of milli-seconds. Initially cold starts with Lambda were over ten seconds long using ordinary Virtual Machines. When Lambda migrated to Firecracker MicroVMs in 2019, cold starts dropped to under a second; an indication of the amount of time and research that AWS has invested in them. 

![Alternative text for accessibility]({% link assets/images/fig-3.drawio.png %})
**Figure 3. Lambda Function on Host OS with Firecracker virtualization technology.**

## Firecracker

A quote from the Firecracker website describes it way better than what I can: 

<i>"Firecracker is a virtual machine monitor (VMM) that uses the Linux Kernel-based Virtual Machine (KVM) to create and manage microVMs. Firecracker has a minimalist design. It excludes unnecessary devices and guest functionality to reduce the memory footprint and attack surface area of each microVM. This improves security, decreases the startup time, and increases hardware utilization"</i>.

![Alternative text for accessibility]({% link assets/images/firecracker-4.png %})
**Figure 4. Firecracker virtualization technology.**

## Lambda MicroVMs

Just when things were starting to become clear to me, in June of this year (2026) AWS introduced Lambda MicroVMs. These offer near instant startup. They are apparently not the same as Lambda Functions, but they are also powered by the same Firecracker virtualization technology which underpins Lambda Functions. From what I understand, the main difference is in the duration (run time per call) and the management. For Lambda Functions, they run for up to 15 minutes per call, whereas for Lambda MicroVMs, it is 8 hours. This might be the reason for the near instant startup. In addition, Lambda MicroVMs are managed by the developer who is responsible for the scaling, whereas Lambda Functions are fully managed by AWS, including automatic scaling. 

AWS has invested a lot in the Firecracker technology and no doubt Lambda MicroVMs will become more prevalent in the future, perhaps even replacing Lambda Functions.

## An Example of a simple Lambda Function

The best way to understand Lambda functions is by actually creating one yourself. The following is based on the AWS documentation and creates a simple function online, that calculates the area, using the AWS GUI. AWS provides excellent documentation for their products, which no doubt contributes to their success.

#### # First create the function

1. Open the Functions page of the Lambda console. This shows a table of all the functions that you have created and is how you access these functions for editing or deleting.

2. Choose "Create function" from the upper RHS of this screen.

3. In the Basic information pane, for Function name, enter "myLambdaFunction" (or leave it at the default name if you don't have a function with this name). Lambda Function names must be unique. 

4. Leave the remainder fields in the default state. Note that NodeJS is the default runtime code.

5. Choose Create function at the bottom.

#### # Default code

After executing the above steps, AWS creates a function with an initial default code shown below (written in JavaScript, with NodeJS compiler). This is a simple function that returns a response "Hello from Lambda", formatted as a JSON object similar to that which you would get from a network call. The status code 200 indicates it was created without error. You don't have to follow this response format; you can return the response in any format that you like.
```js
export const handler = async (event) => {
    const response = {
        statusCode: 200,
        body: JSON.stringify('Hello from Lambda!');
    };
    return response;
};
```
The handler function is always the entry point to your code. When your function is invoked, Lambda runs this method using the event argument that you provide. In this case the event argument is empty and not used.

#### # Replacing the default code

Use the console's built-in code editor to replace the default function code with your own function code. Choose the Code tab. This displays the code editor window. Paste the following code, replacing the code that Lambda created.
```js
export const handler = async (event) => {
    const length = event.length;
    const width = event.width;
    let data = {
        "area": length * width,
    };
    return JSON.stringify(data);
};
```
Choose Deploy to update your function's code. It is now ready to be invoked. Note that in the updated function, the event argument is not empty. It contains the width and length for your function to calculate the area (as a JSON object). 

#### # Invoking (testing) the function
To invoke and test the updated Lamba function you need a test event. The Test Events section contains an editor to create a JSON event. To invoke your function, configure the JSON event to that shown below, then choose Test. 
```js
{
  "length": 6,
  "width": 7
}
```
This invokes the function and displays the results in a card above the Test Events editor. If the card is coloured green, the function was invoked successfully. By clicking on the details tab within the card, the results are shown, including the Response returned by the function and a Summary of its execution, with the duration and amount the you were charged. Note that you also have the option to save the test event with a name if desired.

## Serverless Framework and Cloud Formation

The above function was created directly online in the AWS GUI (console). Alternatively, for convenience, the entire process of creating a function can be done remotely using either AWS's own Cloud Formation or with one of the third party frameworks (the Serverless Framework is a widely used example) on your local CPU.

## Suggested Resources

This post was compiled from the following sources:

[ https://en.wikipedia.org/wiki/AWS_Lambda ](https://en.wikipedia.org/wiki/AWS_Lambda)

[ https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html ](https://docs.aws.amazon.com/lambda/latest/dg/getting-started.html)

[ https://firecracker-microvm.github.io/ ](https://firecracker-microvm.github.io/)

[ https://www.allthingsdistributed.com/2026/04/the-invisible-engineering-behind-lambdas-network.html ](https://www.allthingsdistributed.com/2026/04/the-invisible-engineering-behind-lambdas-network.html)

[ https://www.serverless.com/ ](https://www.serverless.com/)