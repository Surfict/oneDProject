**OS choice**

The OS choice has been made taking in account the following parameters :



*   Good support and community (if a vulnerability is found, the patch is quickly deployed. You can easily find help if you have a question on this particular distribution from a good community with documentation/stackoverflow/etc )
*   Minimaliste distribution. Having a minimalist distribution will reduce the number of potential vulnerabilities. It will also consume less resources. 
*   Stable distribution (which doesn’t need an update every day)

I have chosen **Alpine Linux **inside Docker and **RancherOS **for the base OS on the EC2 instances regarding this parameters.

**Securing the docker image :**



*   Using a minimalist OS to reduce the number of potential vulnerabilities
*   Creating a special group/user, giving the good permissions to this user to handle the application, but nothing more  is a good protection to minimize exposure.

**Problems encountered :**

One of the main problem i had during this projet is that Alpin is not using glibC and we need to recompile the node’ binaries with musl, which takes a lot of time (about one hour with a powerful machine, i think with a micro ec2 instance it would take an entire day) when we are creating the docker image. 

To fix this problem and set-up a working testing environment, i decided to import the docker images into the project, and to load them in the cloud-init script. 

This is why you will find two files : 

CloudFormation.yml and CloudFormationImages.yml 

If you want to use the first one, who is going to build the image and recompile nodejs’binaries, i strongly recommand you to use an EC2 instance with solid compute capabilities. Otherwise, you can use the CloudFormationImages.yml which is exactly the same script, except that he will load the image from the git project and not create it from scratch.

**How to test the project :**

To check if the two server behind the load-balancer are used, i added a digit after the “Hello world” : I’am the container number “ONE” or “TWO”. Server one will return ONE. By refreshing the page, you can see the alternance between the two servers. 



1. With docker-compose
    1. Clone the repository
    2. Docker-compose up
2. With Cloudformation 
    1. Open the AWS Console and go to the Ireland region. The AMI used on the CloudFormation file is only available in this region, so the script won’t work in any other.
    2. Create a new stack using the Cloudformation File. You will need to precise a valid Keypair Name. 
    3. This stack will create :
        1. A new VPC, one public subnet, and two security groups (and much more stuff, like Nacles, IGW, etc)
        2. Three EC2 instance : two nodejs server and one nginx server.
3. You can go to the public ip of the nginx instance to check if everything is working well. 


