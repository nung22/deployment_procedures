# Java Spring Deployment with AWS
### Table of Contents

1. [EC2 Setup](#section1)
2. [MySQL & Data Export](#section2)
3. [Apache Setup](#section3)
4. [Spring Boot Setup](#section4)
5. [JDK & systemd](#section5)
***

## EC2 Setup <a name="section1"></a>

### Set up your server instance on AWS

> 1. [ ] [Login to AWS](https://signin.aws.amazon.com/signin?redirect_uri=https%3A%2F%2Fconsole.aws.amazon.com%2Fec2%2Fv2%2Fhome%3Fstate%3DhashArgs%2523Instances%253Asort%253DinstanceId%26isauthcode%3Dtrue&client_id=arn%3Aaws%3Aiam%3A%3A015428540659%3Auser%2Fec2&forceMobileApp=0&code_challenge=2NOh6S4ks7AdP04QdehoAt3ehxpWm4gvRmfaOb2dCSg&code_challenge_method=SHA-256) and search EC2 to get to your instances.Go to your running instances and select __Launch Instance__.  
>
>![](/AWS_Deployment_Java/assets/sect1_step1.png)

> 2. [ ] Name your instance after your project.  
>
>![](/AWS_Deployment_Java/assets/sect1_step2.png)

> 3. [ ] Select the image we want on our instance. This is the operating system that will be installed on our virtual machine. We want __Ubuntu__ at the __highest version__ that's still __Free tier eligible__. Just make sure you always pick __Free tier eligible__ if you don't want to end up paying Amazon fees!  
>
>![](/AWS_Deployment_Java/assets/sect1_step3.png)

> 4. [ ] Select our instance type. __t2.micro__ is the largest instance that is still __Free tier eligible__. Again, we want to pick the smallest server possible, so we don't get charged.  
>
>![](/AWS_Deployment_Java/assets/sect1_step4.png)

> 5. [ ] Configure your key pair. This will allow you to connect to your instance as an admin. This key cannot be shared. Never upload your PEM key to a git repo or put it on a public place on the internet. This is a literal key to access our instance. Misplacing it, can potentially allow bad actors to install malicious software. __Always store your PEM keys in a safe local folder!__  
> 
>     Name your key pair, ideally after your project, set the types to __RSA__ and __.pem__.  
>
>![](/AWS_Deployment_Java/assets/sect1_step5.png)

> 6. [ ] Configure your security group. We want to limit the number of computers that can access our server through SSH, so it is recommended you set your SSH traffic to __My IP__. _If you are on public wifi or you change locations regularly, your IP address will change. If you are having trouble using SSH, confirm this IP address is yours!_  
>
>     We also want to enable all HTTPS and HTTP traffic. This is a server after all!  
>
>![](/AWS_Deployment_Java/assets/sect1_step6.png)

> 7. [ ] Configure your storage. We want to make sure we stay in __Free tier eligible__.  
>
>![](/AWS_Deployment_Java/assets/sect1_step7.png)

> 8. [ ] You should be able to launch your instance and return to your dashboard now.
> 
>     Select your instance and hit the __"Connect"__ button. The commands in the window that pops up should allow you to SSH (secure shell) your way onto the server.  
>
>     In your terminal, cd into the directory that contains you pem key. Once there, run the commands from the __"SSH client"__ tab on the EC2 dashboard _(The first command will properly set the permissions for a key name keyname.pem. The second will SSH into your AWS server instance. The following commands are an example. Be sure to copy the ones provided in your browser for proper functionality)_:  
> ```
> chmod 400 keyname.pem
> ssh -i "keyname.pem" ubuntu@ec2-XXX-XXX-XXX-XXX.compute-1.amazonaws.com
> ```
>
> - __Note__ : In Windows CMD you do not need to run the chmod command.  
>
>   You should now be connected to your server instance.  

> 9. [ ] Finally, run these two commands to make sure that our system is up to date:
> ```
> sudo apt-get update
> sudo apt-get -y upgradecopy  
> ```
>
> - If you see a pink screen to update __menu.lst__, choose the default option to __keep the local version currently installed__.  
***

## MySQL & Data Export <a name="section2"></a>

>Now we are going to set up our remote server for deployment. Our server is nothing more than a small, allocated space on someone else’s larger computer (in this case, the big computer belongs to Amazon!). That space has an installed operating system, just like your computer. In this case, we are using a distribution of Linux called Ubuntu, version 22.04.

### Server configuration:

>Although we have Linux, our new computer is otherwise empty. Let’s change that so we can start building a server capable of providing content that the rest of the world can access.

### MySQL

> 1. [ ] Next, we will need to install our MySQL server:
> ```
> sudo apt-get install mysql-server                      // answer Yes when prompted!
> ```
>
> ```
> sudo apt-get update
> ```
>
> - The next steps will allow our Spring Boot project to create a connection to the MySQL database we just installed. 

> 2. [ ] First log into the MySQL server:
> ```
> sudo mysql -uroot -p
> # enter the same password as MySQL on your computer when prompted
> ```
>
> - This step should open up the MySQL shell on your remote server. The next step will allow us to log into the database without using sudo in our ubuntu shell. 
>
>     Type the following commands, outlined in red, into the MySQL shell:
>
>     ***IMPORTANT:*** *change the `your_mysql_password` between the single quotes to the password you used in the above step*
>
>![](/AWS_Deployment_Java/assets/sect2.1_step2.png)
>
> ```
> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'root';
> ```
>
> ```
> FLUSH PRIVILEGES;
> ```
>
> ```
> quit
> ```

> 3. [ ] The next installation will allow us to configure security settings. After exiting the mysql shell, back in your ubuntu remote server, enter the following command:
> ```
> sudo mysql_secure_installation
> ```
>
> - There will be a series of prompts to answer. Follow the screen shot below.
>
>![](/AWS_Deployment_Java/assets/sect2.1_step3.png)
>

> 4. [ ] If the above commands worked correctly, you should be able to log into the MySQL shell without sudo:
> ```
> mysql -uroot -p
> # enter password when prompted
> ```
>
> - Next, we will need to set up the database that our project will need. This can easily be done by going to MySQL Workbench and exporting the data we already have.
>

### Data Export

> 1. Open MySQLWorkbench on the connection that contains the schema of the Spring Boot project you want to deploy.

> 2. Click the "Administration" tab next to the "Schemas" tab.
>

> 3. Click on the Data Export option, choose your schema, and select "Include Create Schema" checkbox. In the example the name of the schema is "auth," but your schema name will be something different depending on the project you want to deploy.
>
>![](/AWS_Deployment_Java/assets/sect2.2_step3.png)
>    Click "Start Export" and it will dump your data into a SQL file. Once the exporting is completed, MySQLWorkbench will give you the directory where the SQL file is located. In this example, the full path is ```/Users/eduardobaik/dumps/Dump20170720.sql```.

> 4. Copy the contents of the dumped SQL file and paste it in the MySQL shell in your server.
>
>![](/AWS_Deployment_Java/assets/sect2.2_step4.gif)

> 5. In MySQL, run ```show databases;``` to see if your schema has been imported correctly. To see the tables of your schema, you can run ```use <<yourSchema>>;``` and ```show tables;```.
>![](/AWS_Deployment_Java/assets/sect2.2_step5.png)

> 6. To exit MySQL, you can run ```quit```.
***

## Apache Setup <a name="section3"></a>

> Apache is one of the most popular web servers in the world. We are going to use Apache to reverse proxy incoming requests to our Spring Boot application.

> 1. Install Apache with the following command:
> ```
> sudo apt-get install apache2
> ```

> 2. In your browser, navigate to your public IP to see the Apache2 Ubuntu Default Page.
>![](/AWS_Deployment_Java/assets/sect3_step2.png)
***

## Spring Boot Setup <a name="section4"></a>

> When a HTTP request comes into our EC2 server, Apache will receive the request and use reverse proxy to forward it to our Spring Boot application running on port 9090. In this tab, we will secure copy our application into our server, set up the reverse proxy, and use systemd to run our application.

> 1. For our reverse proxy to work, we are going to use the Apache JServ Protocol. In your Spring Boot Application File, add the following code:
> 
> ### com.codingdojo.auth.AuthApplication.java
> ```
>package com.codingdojo.auth;
>import org.apache.catalina.connector.Connector;
>import org.apache.coyote.ajp.AbstractAjpProtocol;
>import org.springframework.boot.SpringApplication;
>import org.springframework.boot.autoconfigure.>SpringBootApplication;
>import org.springframework.boot.web.embedded.tomcat.TomcatServletWebServerFactory;
>import org.springframework.context.annotation.Bean;
>@SpringBootApplication
>public class AuthApplication {
>    public static void main(String[] args) {
>        SpringApplication.run(AuthApplication.class, args);
>    }
>    @Bean
>    public TomcatServletWebServerFactory servletContainer() {
>        TomcatServletWebServerFactory tomcat = new TomcatServletWebServerFactory();
>        Connector ajpConnector = new Connector("AJP/1.3");
>        ajpConnector.setPort(9090);
>        ajpConnector.setSecure(false);
>        ajpConnector.setAllowTrace(false);
>        ajpConnector.setScheme("http");
>        ((AbstractAjpProtocol)ajpConnector.getProtocolHandler()).setSecretRequired(false);
>        tomcat.addAdditionalTomcatConnectors(ajpConnector);
>        return tomcat;
>    }
>}
> ```

> 2. Next, we need to package our project into a **war** file.
>
>       - Go to your 'pom.xml' file and Overview tab. If your packaging is jar, change it to war.<br/><br/>
>       ![](/AWS_Deployment_Java/assets/sect4_step2.1.png)<br/><br/>
>       - Run ```Maven``` -> ```Update Project```<br/><br/>
>       ![](/AWS_Deployment_Java/assets/sect4_step2.2.png)<br/><br/>
>       - Run ```Run As``` -> ```Maven Install``` (this will create the war file)<br/><br/>
>       ![](/AWS_Deployment_Java/assets/sect4_step2.3.png)<br/><br/>

> 3. STS will build a war file and save it inside the target directory. In this example, the full directory path is: ```/Users/eduardobaik/Desktop/springProjects/auth/target/auth-0.0.1-SNAPSHOT.war```.
>
>       - Navigate to said directory in your terminal and secure copy the war file into the home directory of your EC2 server. You will need your pem file path and your public ip address. For example:<br/><br/>
>       ```
>       scp -i ~/Desktop/springProject.pem auth-0.0.1-SNAPSHOT.war ubuntu@34.228.244.112:~/
>       ```
>       ![](/AWS_Deployment_Java/assets/sect4_step3.png)<br/><br/>

> 4. Let's create a folder for our application inside of the '/var' directory.
> 
>       ```
>       sudo mkdir /var/springApp
>       sudo mv ~/auth-0.0.1-SNAPSHOT.war /var/springApp/
>       ```

> 5. Now, we need to tell Apache to proxy requests to our application. Note: It may prompt you to restart after each command using ```sudo service apache2 restart```.
>
>       - Set up proxy<br/><br/>
>       ```
>       sudo a2enmod proxy
>       sudo a2enmod proxy_ajp
>       ```
>       - Open our virtual host conf file.<br/><br/>
>       ```
>       cd /etc/apache2/sites-available
>       sudo vim 000-default.conf
>       ```
>       - Add the proxy configuration at the bottom. Make sure to have it run on port 9090.<br/><br/>
>       ![](/AWS_Deployment_Java/assets/sect4_step5.png)
>       ```
>       ProxyPass / ajp://localhost:9090/
>       ProxyPassReverse / ajp://localhost:9090/
>       ```

> 6. Restart Apache.
> ```
> sudo service apache2 restart
> ```
***

## JDK & systemd <a name="section5"></a>

> Lastly, we need to install the JDK to run our war file and create a systemd script to have our application always running.

### JDK

> 1. Install the JDK.
> 
>       ```
>       sudo apt install openjdk-17-jdk
>       ```

> 2. Navigate to '/var/springApp' and run your application with the 'java -jar [ warFile ]'. For example:
> 
>       ```
>       cd /var/springApp
>       java -jar auth-0.0.1-SNAPSHOT.war
>       ```
>  - This will start your Spring Boot application. In your favorite browser, navigate to your public IP address and you should see your application.<br/><br/>
>![](/AWS_Deployment_Java/assets/sect5_step2.png)

### systemd

> Currently our app is working fine; however, if we ever close our ssh session, the Spring Boot application will stop running. To solve this issue, Spring Boot recommends that we create a systemd script to run our application.

> 1. Create a script named [ yourApp ].service in '/etc/systemd/system' directory.
> 
>       ```
>       cd /etc/systemd/system
>       sudo touch auth.service
>       sudo vim auth.service
>       ```
>  - In the editor, follow this example:<br/><br/>
>       ```
>       [Unit]
>       Description=Auth application using Spring Boot
>       After=syslog.target
>       [Service]
>       User=ubuntu
>       ExecStart=/usr/bin/java -jar /var/springApp/auth-0.0.1-SNAPSHOT.war
>       SuccessExitStatus=143
>       [Install]
>       WantedBy=multi-user.target
>       ```
>  - **Note** : Change the Description field and the .war filename to match your application.<br/><br/>

> 2. Let systemd know that we have created a new service:
> 
>       ```
>       sudo systemctl daemon-reload
>       ```
  
> 3. Now that we have created the script, we need to make sure that our applications starts on every system boot:
> 
>       ```
>       sudo systemctl enable auth.service
>       ```
  
> 4. Start our service:
> 
>       ```
>       sudo systemctl start auth
>       ```
  
> 5. We can also stop, restart and check the status of our service:
> 
>       ```
>       sudo systemctl stop auth
>       sudo systemctl restart auth
>       systemctl status auth
>       ```
>  - We don't have to run sudo for the status because we are just checking if the service is running.<br/><br/>
>![](/AWS_Deployment_Java/assets/sect5_step5.png)
> Now, your app will be running via systemd. You can close your ssh session and everyone will still be able to see your application. <br/><br/>
