# Java Spring Deployment with AWS

## EC2 Setup

### Set up your server instance on AWS

> 1. [ ] [Login to AWS](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fconsole.aws.amazon.com%2Fec2%2Fv2%2Fhome%3Fstate%3DhashArgs%2523Instances%253Asort%253DinstanceId%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fec2&forceMobileApp=0&code_challenge=2NOh6S4ks7AdP04QdehoAt3ehxpWm4gvRmfaOb2dCSg&code_challenge_method=SHA-256) and serach EC2 to get to your instances.Go to your running instances and select __Launch Instance__.  

![](/AWS_Deployment_Java/assets/1655222903__step1.png)

> 2. [ ] Name your instance after your project.  

![](/AWS_Deployment_Java/assets/1655222904__step3.png)

> 3. [ ] Select the image we want on our instance. This is the operating system that will be installed on our virtual machine. We want __Ubuntu__ at the __highest version__ that's still __Free tier eligible__. Just make sure you always pick __Free tier eligible__ if you don't want to end up paying Amazon fees!  

![](/AWS_Deployment_Java/assets/1655222904__step4.png)

> 4. [ ] Select our instance type. __t2.micro__ is the largest instance that is still __Free tier eligible__. Again, we want to pick the smallest server possible, so we don't get charged.  

![](/AWS_Deployment_Java/assets/1655222905__step5.png)

> 5. [ ] Configure your key pair. This will allow you to connect to your instance as an admin. This key cannot be shared. Never upload your PEM key to a git repo or put it on a public place on the internet. This is a literal key to access our instance. Misplacing it, can potentially allow bad actors to install malicious software. __Always store your PEM keys in a safe local folder!__  
> 
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Name your key pair, ideally after your project, set the types to __RSA__ and __.pem__.  

![](/AWS_Deployment_Java/assets/1655222905__step6.png)

> 6. [ ] Configure your security group. We want to limit the number of computers that can access our server through SSH, so it is recommended you set your SSH traffic to __My IP__. _If you are on public wifi or you change locations regularly, your IP address will change. If you are having trouble using SSH, confirm this IP address is yours!_  
>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;We also want to enable all HTTPS and HTTP traffic. This is a server after all!  

![](/AWS_Deployment_Java/assets/1655223575__step7.png)

> 7. [ ] Configure your storage. We want to make sure we stay in __Free tier eligible__.  

![](/AWS_Deployment_Java/assets/1655222905__step8.png)

> 8. [ ] You should be able to launch your instance and return to your dashboard now.
> 
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Select your instance and hit the __"Connect"__ button. The commands in the window that pops up should allow you to SSH (secure shell) 
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;your way onto the server.  
>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;In your terminal, cd into the directory that contains you pem key. Once there, run the commands from the __"SSH client"__ tab on the 
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;EC2 dashboard _(The first command will properly set the permissions for a key name keyname.pem. The second will SSH into your AWS 
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;server instance. The following commands are an example. Be sure to copy the ones provided in your browser for proper functionality)_:  
```
chmod 400 keyname.pem
ssh -i "keyname.pem" ubuntu@ec2-XXX-XXX-XXX-XXX.compute-1.amazonaws.com
```
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;__Note__: In Windows CMD you do not need to run the chmod command.  
>
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;You should now be connected to your server instance.  

> 9. [ ] Finally, run these two commands to make sure that our system is up to date:  
```
sudo apt-get update
sudo apt-get -y upgradecopy
```
>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;If you see a pink screen to update __menu.lst__, choose the default option to __keep the local version currently installed__.  
***

## MySQL and Data Export

***

## Apache Setup

***

## Spring Boot Setup

***

## JDK and systemd
