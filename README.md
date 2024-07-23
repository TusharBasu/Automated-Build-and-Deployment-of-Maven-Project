# Automated Build and Deployment of Maven Web-app  
------------------------------------------------------------------------------------------
# **Project Architecture**
![Screenshot 2024-07-23 153635](https://github.com/user-attachments/assets/a041e797-10ac-48c5-b12a-ba9d75bf13cb)



~ By default the Tomcat will run on Port No. 8080


~ By default the Jenkins will run on Port No. 8080


~ Here we have to change the Port No. of Tomcat to 9090.


# Tasks:
---------------
========
Part 1
========
1. Create an EC2 Instance - Connect to the instance using MobaXTerm Tool.

![image](https://github.com/user-attachments/assets/cc79b451-22c7-4e3a-8d6a-798cc3c4c930)



========
Part 2
========
JENKINS INSTALLATION ON LINUX EC2 INSTANCE
~ Add Jenkins repo. to our YUM repo:
        sudo wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo


 ~ Import a key-file from Jenkins-CI to enable the installation from package:
        sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key


        sudo yum upgrade


~ Installing Java
        sudo amazon-linux-extras install java-openjdk11 -y



        java -version
        
![image](https://github.com/user-attachments/assets/a7f44c65-c1e2-4455-af80-7cf4c0a0b6d0)



~ Install Jenkins:
        sudo yum install jenkins -y
![image](https://github.com/user-attachments/assets/66826893-fd1a-4f6e-b9b3-77ba2cc3f968)



~ Lets start, enable, and check the status of Jenkins 
        sudo systemctl enable jenkins        
        sudo systemctl start jenkins
        sudo systemctl status jenkins
                You should see Active & Running in Green Colour, which means the Jenkins Tool is successfully installed

![image](https://github.com/user-attachments/assets/7f385f7e-6b5e-4d13-bd5f-2cb792f20870)



~ Check the jenkins accessibility. By default the Jenkins will run on Port No. 8080.
So, open Port No. 8080 for EC2 instance to access Jenkins
        "Check" the instance ----> Security ----> Click on the link under SGs ----> Add Rules ----> Custom TCP, 8080, Anywhere ----> Save Rules.


Open new tab: <Paste the Public IP of Instance>:8080 ----> You will see the Jenkins Home Page ----> Now we have to Unlock the Jenkins ----> You will see a code in the red colour ----> Copy the code in red colour ----> Go to MobaXTerm ----> sudo cat <paste the red color code> ----> You will see a password. Copy that password and paste in jenkins home page ----> Click on suggested plugins ----> You will see "Create First Admin User" ----> Enter Username, Password, Name, Email Id ----> Save and Continue ----> Jenkins URL:http://52.14.0.208:8080/  ----> Save and Finish ----> You will see "Jenkins is ready!" ----> Click on "Start using jenkins" ----> Now you can see the jenkins home page and here you can create Jenkins Jobs.

![image](https://github.com/user-attachments/assets/46906bf3-d2e1-4929-a547-2501911013a1)

# Welcome to jenkins Dashboard!!!

![image](https://github.com/user-attachments/assets/be222714-863a-498a-9cf6-ef3b4e53cf27)



========
Part 3
========
TOMCAT WEBSERVER INSTALLATION ON LINUX EC2 INSTANCE


https://tomcat.apache.org/download-90.cgi ----> Downloads ----> Tomcat 9 ----> Copy the link of tar.gz file ----> https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.73/bin/apache-tomcat-9.0.73.tar.gz ----> sudo wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.73/bin/apache-tomcat-9.0.73.tar.gz ----> ls -l ----> You will the tomcat file in red colour. This is a tar.gz file and we have to extract the tar.gz file ----> tar -xvf <enter the name of tomcat file in red colour> ----> All the files will get extracted ----> ls -l  ----> You will see the list of extracted files 


Lets start the tomcat server:


Inorder to start the tomcat server, we need a file called "starup.sh"
This "startup.sh" file will be there in "bin" folder ----> cd bin ----> ls -l ----> you will see startup.sh file ----> ./startup.sh ----> You will see "Tomcat Started"


Lets access the tomcat server:
Since, the tomcat also runs on Port No. 8080, we cannot access tomcat as on the same port no. jenkins is also running.
Now, we have to change the Port No. of Tomcat. (Tomcat Port No. 9090)


How to change the port no. of Tomcat?
------------------------------------------------------
In order to change the port no. of Tomcat, we will configure a file known as "server.xml" This server.xml file will be available in "conf" folder. 


cd ..  ----> pwd ----> /home/ec2-user/apache-tomcat-9.0.73 ----> ls -l ----> cd conf ----> ls -l ----> you will see server.xml file ----> vi server.xml ---->  scroll down till you see "connector port=8080" ----> press "i" ----> Change the port no to 9090 instead of 8080 ----> escape ----> :wq


Check the Tomcat accessibility. By default the Tomcat will also run on Port No. 8080. But on the same port no. jenkins is also running.
So we have to add the port no. to 9090 in ec2 instance to access Tomcat:
        "Check" the instance ----> Security ----> Click on the link under SGs ----> Add Rules ----> Custom TCP, 9090, Anwhere ----> Save Rules.
Shutdown the tomcat server ----> cd bin ----> ./shutdown.sh  ----> ./startup.sh


Open new tab: <Paste the Public IP of Instance>:9090 ----> You will see the Tomcat Home Page
Go to browser and check for tomcat accessibility. You are now able to access Tomcat and Jenkins in the same instance.

# Welcome to Tomcat server!!! 
![image](https://github.com/user-attachments/assets/d7c5c990-9a94-41d6-a835-2f23d138b58f)




Here we can access only the Home page of Tomcat. 
Inorder to access complete tomcat server, we need to do the configuration in "context.xml" file.
apache-tomcat-9.0.73/webapps/manager/META-INF/context.xml


cd apache-tomcat-9.0.73/webapps/manager/META-INF/context.xml ----> vi context.xml ----> You will see a "valve" tag. You have to edit "Allow" tag ----> ".*" />


Configuring the Users in Tomcat
---------------------------------------------
we need to edit the tomcat-users.xml to configure the users. 
The "tomcat-users.xml" file is available in "conf" folder


<role rolename="manager-gui" />
<user username="tomcat" password="tomcat" roles="manager-gui" />
<role rolename="admin-gui" />  
<user username="admin" password="admin" roles="manager-gui,admin-gui"/>


cd bin
./shutdown.sh
./startup.sh


Access Tomcat on Browser ----> Click on Server Status ----> Enter the username (admin) and password (admin)

# Now we can access manager and all others page of tomcat serever.!!!

![image](https://github.com/user-attachments/assets/4b9a2868-1a50-4fac-8505-6ee01e42d332)


Install GIT in EC2 Instance where Jenkins is running
        sudo yum install git -y


Jenkins Port No.: 8080
Tomcat Port No.: 9090


++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Steps to create the Jenkins job with GITHUB Repo. + Maven + Tomcat Server:
++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
Connect to the Tomcat Server 
Connect to the Jenkins Software


Inorder to do the automation, Jenkins is going to execute some script to deploy the war file into the Tomcat Webserver.


___________________________________________________________
Step 1: Adding the manager-script role in tomcat-users.xml file
___________________________________________________________


In order to do the automation, Tomcat user should have a "manager-script" permission. There should be one user in the Tomcat with the "manager-script" permission. Then with that user credentials we can automate the deployment process.


Goto MobaXTerm and Connect to the Instance ----> ls -l ----> cd apache-tomcat-9.0.73 ----> ls -l ----> cd conf ----> ls -l ----> You can see tomcat-users.xml ----> vi tomcat-users.xml




___________________________________________________________
Step 2: Installing "deploy to container" plugin in Jenkins software
___________________________________________________________


Jenkins Dashboard ----> Manage Jenkins ----> Mange Plugins ----> Available Plugins ----> In the search bar, type deploy to container and check the same ----> Click on "Install without restart" ----> Click on "Go back to the top page.


___________________________________________________________________________
Step 3: Creating a Jenkins job and for that Jenkins job we will give permission to deploy

#Configure the Jenkins and it's all settings.

![image](https://github.com/user-attachments/assets/7fbe4ec3-0623-4153-929f-d1b88fc2d05f)

#Successfully Build

![image](https://github.com/user-attachments/assets/b4729194-7e31-4733-974f-c9f22e309aaa)

#Successfully Deployed Maven project

![image](https://github.com/user-attachments/assets/60c4aa85-2c5f-4301-b8aa-7ecaf3bb85eb)

# After Changes!!!

![image](https://github.com/user-attachments/assets/020b947c-fbe9-4e39-89c1-28f1267bd292)

___________________________________________________________________________
Add JDK, GIT, MAVEN in Jenkins Browser Page by going into Global Tools Configuration
