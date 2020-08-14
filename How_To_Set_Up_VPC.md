# Setting up a VPC (Virtual Private Cloud on AWS)

## Creating the VPC

First thing is first we want to Create out **VPC** so to do that navigate to the **VPC** page on Amazon and click **Create VPC**

* Note : Use a meaningful name so it can easily be identified, use a IPv4 that is not taken by anyone else in your network to avoid problems later on.
* Note : Check the `No Ipv6 CIDR BLock` and create it!

![Step1](Images/SnapShot_1.PNG)

## Creating the Internet Gateway

Next we are going to create our **Internet Gateway** to provide internet for our **Subnets**, well only the public one for the time being.

Navigate to the **Internet Gateways** and Click **Create Internet Gateway**


![Step2](Images/SnapShot_2.PNG)

It should now saw **Detached**, when you click create as it is not current attached to any networking entity. We are going to attach it to the VPC we just created.

Click **Actions** in the top left and click **Attach VPC** then when the search box appears look for the **VPC** we just created and click **Attach**

![Step3](Images/SnapShot_3.PNG)

## Creating the Subnets _Public_ and _Private_

Now we are going to create the **Subnets**, a **private** and **public** one so that we can create a level of security in our design. We plan to place the App inside the **public** one which has access to the internet, and the DB inside the **private** one which does not have access to the internet at the moment.

Navigate to **Subnets** and click **Create Subnet**

![Step4](Images/SnapShot_4.PNG)

Here we are creating the **public subnet** and we are going to give it a slightly different IP so it is still associated with the **VPC**, here I have replaced the third **octet** with a **3** but you could use **1** or **2**. Any number betweeen the **0 - 255** is valid here.

The same rules apply here for the **private subnet** just make sure it is **NOT** the same IP as either the **public subnet** or **VPC** exactly.

![Step5](Images/SnapShot_5.PNG)

Create this **subnet** too, we now have both the public and private subnet.

In my case:

* **Public Subnet** : 109.10.1.0/24

* **Private Subnet** : 109.10.2.0/24

We also need to now Attach the **IGW** to a **Route Table** otherwise it will not be able to know which of the subnets is its target by default. Usually this is done by having one as the **main route**. By default **subnets** come with less internet, this is good because it allows you to control the amount of internet access it has.

## Create Route Table

We now need to create this **Route table** so we can tell the **IGW** exactly where to go to when it comes into the **VPC**.

Before we create the **Route table** we first need to go to the **Route table** tab in AWS and search the name of your **VPC** in the search bar. It should come up with a blank entry. This is your default **Route** and is technically your **private subnet** association as it doesn't have internet by default which is what we want. 

Rename this with a good naming convention making sure to label it as your **Private Route** and the **Main Route** too.

![Step6](Images/SnapShot_6.PNG)

Okay, now click **Create Route Table**. 

![Step7](Images/SnapShot_7.PNG)

Following the same naming convention create the **public route table**. Then go back to the **Route Tables** page and search your VPC and you should see your **private** and **public** route tables both there.

![Step8](Images/SnapShot_8.PNG)

Now Click that **public Route** and then go down to the **Routes** tab and click **Edit Routes**.

![Step9](Images/SnapShot_9.PNG)

When inside you need to click **Add Route** and add the connection for your **IGW** so to give it internet.

* The destination of the new route is **0.0.0.0/0** and the Target is your **IGW**. The 0 is kind of a representation of the world, E.G. any traffic from the internet. **Save** that and go back.

![Step10](Images/SnapShot_10.PNG)

Now we want to create a **subnet** association with our **public Route**, to do that select the **public Route** and go to **subnet associations** and click **Edit subnet associations**.

![Step11](Images/SnapShot_11.PNG)

Select your **public Subnet** and click **Save**.

![Step12](Images/SnapShot_12.PNG)

As for the **private subnet**, if you click that in the **Route tables** and go to **subnet associations** you can see that it is the **main route** therefore the private **subnet** will automatically be associated with it. 

![Step13](Images/SnapShot_13.PNG)

# Building the Security Groups for our VPC / Subnets
