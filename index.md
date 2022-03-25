---
title: Install Teradici PCoIP Marketplace Image on AWS EC2 Mac instance
description: Learn how to deploy PCoIP Ultra on AWS EC2 Mac instance with a AWS marketplace offering.
author: chad-m-smith
tags: Teradici, AWS, MAC, EC2 Dedicated Hosts
date_published: 2022-03-01
---

Chad Smith | Technical Alliance Architect at Teradici | HP

<p style="background-color:#CAFACA;"><i>Contributed by Teradici employees.</i></p>

This guide shows you how to install Teradici PCoIP agent on a Mac instance running in AWS. This guide is intended for customers that want to procure Teradici from AWS marketplace on an hourly consumption rate and apply the image to a pre-allocated intel-based MAC-mini. For customer that have Teradici annual subscriptions refer to the [Teradici-PCoIP-Ultra-on-Mac-EC2-instance-in-AWS](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-EC2-instance-in-AWS) instead. 

EC2 Mac instances are available for purchase as Dedicated Hosts through On Demand and Savings Plans pricing models. Billing for EC2 Mac instances is per second with a 24-hour minimum allocation period to comply with the Apple macOS Software License Agreement. With 'On Demand' procurement, you can launch an EC2 Mac host and be up and running within minutes. At the end of the 24-hour minimum allocation period, the host can be released at any time without further commitment.

More Information on EC2 MAC Instance can be found [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-mac-instances.html).

## Objectives

+ Allocate a AWS EC2 Mac Instance from AWS Console.
+ Procure Teradici CAS for MAC in AWS marketplace.
+ Launch MAC Instance in AWS EC2 console
+ SSH into MAC Instance and create password for 'ec2-user'
+ Connect to EC2 Mac Instance via PCoIP client

## Costs

This guide uses billable components of AWS Cloud infrastruture and Teradici software on a hourly consumption rate, including the following:

+   [Teradici CAS marketplace offering](https://aws.amazon.com/marketplace/pp/prodview-isaghmqny2wr6?sr=0-5&ref_=beagle&applicationId=AWSMPContessa), Teradici PCoIP subscriptions through marketplace.
+   [AWS EC2 Mac Instance](https://aws.amazon.com/ec2/instance-types/mac/), including vCPUs, memory, disk, and GPUs as a dedicated host.
+   [Internet egress and transfer costs](https://aws.amazon.com/blogs/architecture/overview-of-data-transfer-costs-for-common-architectures/), for PCoIP and other applications communications

Use the [AWS pricing calculator](https://calculator.aws/#/) to generate a cost estimate based on your projected usage.

## Before you begin

In this section, you set up some basic resources that the tutorial depends on.

1. Instructions in this guide assume that you have a [AWS account](https://aws.amazon.com/free/) 

1. Ensure you have [Service Quotas](https://console.aws.amazon.com/servicequotas) for **'Running Dedicated mac1 Hosts'**.

1. Familiarize yourself with [AWS network](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Networking.html) topology and best practices.

1. Ensure that you have the correct [IAM permission to procure through AWS marketplace](https://docs.aws.amazon.com/marketplace/latest/userguide/marketplace-management-portal-user-access.html)

## Set up the virtual workstation

In this section, you create and configure a virtual workstation, including setting up networking and installing utilities. 

### Procure the EC2 Mac Instance

In this section, you procure a mac1 type dedicated host in your region

1. Select a AWS region that has [EC2 Mac Instances available](https://aws.amazon.com/ec2/instance-types/mac/) with a understanding of consumption rate.

1. Allocate a Mac Dedicated Host within the [EC2 Dashboard](https://console.aws.amazon.com/ec2). Choose **Dedicated Hosts**, then choose **Allocate Dedicated Host**.

    ![image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/Dedicated_1.png)
    
1.  On the **Allocate Dedicated Host** page, make the following selections:
    + For **Name tag**, type EC2 Mac Dedicated Host
    + For **Instance family**, choose **mac1**
    + For **Support multiple instance** types, clear the **Enable** check box
    + For **Instance type**, choose **mac1.metal**.
    + For **Availability Zone**, choose any zone in your Region.

    Keep the remaining default selections and choose **Allocate**.
    
     ![image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_1.png)
    
1. Once allocated, the Dedicated Host appears with a status of **Available**. 

    ![Image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/Dedicated_2.png)

### Subscribe to Teradici CAS for macOS

Now that a mac1.metal dedicated Instance has been allocated in your AWS account, you can now associate an AWS marketplace image to that instance. 

**Note** It is helpful to remember what region you had allocated your host in the previous step because the marketplace listing has to be allocated in the same region as well.

1. In your web browser, navigate to the [AWS Marketplace: Teradici CAS for macOS](https://aws.amazon.com/marketplace/pp/prodview-isaghmqny2wr6?sr=0-5&ref_=beagle&applicationId=AWSMPContessa) offering to subscribe. Select **Continue to Subcribe** to continue.

    ![image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_0.png)

1. Before continuing, you must continue to the terms and conditions of the offering.

    ![image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_2.png)

1. Once the EULA is agreed upon, the AMI will be granted to your AWS account. This may take a couple of minutes for this process to finish. When complete the **Continue to Launch** button will appear. 

    ![image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_3.png)
    
1. The next-step is where you define what region where the AMI wil be deployed too. It must align to the same region where you deployed your dedicated host **mac.metal** system as well as any pre-configured VPC and its associated network infrastrucuture.

    ![image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_4.png)
    
 1. In most senerios you will want to continue the deployment from the EC2 console, You also have the option to deploy into your organizations service catalog instead.

    ![Image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_5.png)
    
 1. The EC2 console will pre-select compatiable EC2 instance types for the available AMI. It has choosen the **mac1.metal** as the defualt instance type. Click **Next** to proceed.

    ![Image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_6.png)

 1. In the Instance Details step, pay special attention to the Network and Subnet portions of the configurator. **You must have a network and subnet that is in the same AZ as your provisioned dedicated mac1metal instance.** Otherwise, it won’t be accessible.

    ![Image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_7.png)

 1. The default storage capacity is too small for a practical deployment, adjust the amount in configurator.

    ![Image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_8.png)
    
 1. As a part of the marketplace deployment, you have the option of choosing the auto-generated security group that opens all necessary communications for PCoIP protocol and SSH (or) you can assign an existing Security Group to the deployment.

    ![Image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_11.png)       
 1. (Optional) you have the option to assign- a custom metadata field to the instance.

 1. The last step is to associated a key pair key to the instance. You will need access to this PEM key from you local machine because you will need to SSH into the mac instance to finsih the configuration in the next step.

    ![Image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_10.png)

 1. As a launch promotion, Teradici is offering a free 30-day trial of CAS on MAC launched from AWS marketplace. This promotion does not include a discount on the underlying mac1.metal instance.

    ![Image](https://github.com/ChadSmithTeradici/Teradici-PCoIP-Ultra-on-Mac-instances-from-AWS-marketplace/blob/main/images/MACML_9.png)

1. On the **Select an existing key pair or create a new key pair** dialog, verify your existing key pair (if you do not have a key pair, select the option to create a new key pair). Then, select the acknowlegement check box and choose **Launch Instances**.

1. On the **Instances** page, wait for the **Status Check** column of your instance to show 2/2 checks passed before continuing.

### SSH into Teradici CAS for macOS and assign a password

## Create user by ssh connection to macOS Instance
In this section, you will establish a connection to your instance using SSH, to install VNC temporary GUI access to the Mac. (some Teradici prerequisites configurations that can only be accomplished within the Mac GUI) Finally, PCoIP will be installed and configured via VNC session into the Mac GUI.

1. On the **EC2 Dashboard**, select your **EC2 Mac Instance** and choose **Connect**.

1. On the **Connect to an instance** dialog, choose **SSH client**. Follow the instructions in the dialog for SSH client to connect to your mac1.metal instance


    

   
    



## Install PCoIP Client and connect to EC2 Mac Instance
In this section, you will establish a connection to your instance using PCoIP. You will need to install a PCoIP client on your client system that will be used to initiate the session to the EC2 Mac Instance in AWS. Depending on your network topology, use will either connect to the local IP (or) ephemeral/elastic Public IP (or) Fully Qualified Domain Names (FQDN)

1. [Download the client installer](https://docs.teradici.com/find/product/software-and-mobile-clients) based on your client OS. You don't need a login credentials to download client software and can have as many copys of various client OS as you need.

1. Install the PCoIP client software per the OSs Administration Guides installation instructions.

1. Locate the **IP address** or **FQDN** of the AWS EC2 Mac instance via the [EC2 Dashboard](https://console.aws.amazon.com/ec2)

1. Identify the Mac instance within the list of **Running Instances** in the EC2 Dashboard, check the **box** near the instance name, if it was named.

1. Under the **Details** tab you will see **Public IPv4 Address** (or) **Private IPv4 Address** (or) **Private IPv4 DNS** (or) **Public IPv4 DNS**

1. From the client system, start your PCoIP client per OS. Typically the PCoIP client will have a icon:

    ![image](https://github.com/ChadSmithTeradici/TeradiciPCoIPonMACinAWS/blob/main/images/PCoIP_icon.jpg)

1. When the PCoIP client starts, it will ask for a **Host Address or Code**. Enter in your **IP address or FQDN** previously identified in previous section. (optionally) enter a name to **Connection Name** field then **SAVE**, if you want to save connection.

    ![image](https://github.com/ChadSmithTeradici/TeradiciPCoIPonMACinAWS/blob/main/images/PCoIP-Client.jpg)
    
1. Next, you will get a Cannot verify your connection to IP warning. This error is becuase a 3rd party trusted certificate has not been install on the host. You can select the **Connect Insecurely** option.
    
    ![image](https://github.com/ChadSmithTeradici/TeradiciPCoIPonMACinAWS/blob/main/images/PCoIP-Trusted.jpg)
    
1. Finally, enter in the macOS login credentials(**ec2-user**, if not changed)that you used previously in your VNC session to log into the instance.

    ![image](https://github.com/ChadSmithTeradici/TeradiciPCoIPonMACinAWS/blob/main/images/PCoIP-Auth.jpg)


## Clean up

To avoid incurring charges to your AWS account for the resources used in this tutorial, you can simply delete the instance:

1.  In the [EC2 Dashboard](https://console.aws.amazon.com/ec2) , go to the Mac **Instance State** scroll to **Terminate**
1.  You can repurpose PCoIP floating seat, allow up to 24hrs for Teradici Cloud Licensing server to flush assoication to EC2 Mac instance.
1.  Note: if you delete instance before 24hr peroid. AWS will still charge you the remaining hours until 24hr peroid expires

## What's next

+   [Configure and optimize](https://www.teradici.com/web-help/pcoip_agent/graphics_agent/macos/21.07/admin-guide/configuring/configuring/) the PCoIP expereince on the EC2 Mac Instance. 
+   Learn more about [Teradici](https://www.teradici.com/) products and offerings.
+   Learn more about [AWS EC2 Mac Instances](https://www.youtube.com/watch?v=d0FulqrjHkk&ab_channel=AmazonWebService)
