# Portfoli Deployment Via CICD-Jenkins

## Introduction


## Step 1 : Create Portfoliop Server.
Lunch Instnace
- Name and tags : Portfolio-Server
- Os Images - Ubuntu
- Instance type - t2.micro
- Security group - Existing One
- Lunch Instnace

![project screenshot](/Images/server.PNG)

Also enable 80 port in security group and For jenkins server you have to enable 8080 port

![project screenshot](/Images/SG.PNG)

## Step 2 : Set-up Portfolio Server 

Firstly you have take access of virtual machine via GitBash
```
ssh -i <private-key> username:<public-ip>

example
ssh -i Jenkins-Key-Pair.pem ubuntu@54.80.153.1
```
Then you have update and install and start the apache2 webserver. Because our portfolio website run on apache2 webserver
For instalation use below command.
```
sudo apt update -y
sudo apt install apache2 -y
sudo systemctl start apache2
sudo systemctl enable apache2
```
Our main user is now ubuntu we have to give default path of apache2 webserver permission to ubuntu user so they can performe every operation on that path and inside files and folder.
For give ownership to ubuntu use below cmd
```
sudo chown ubuntu:ubuntu /var/www/html/
```
Now ```/var/www/html``` have ubuntu owner. 
 
![project screenshot](/Images/cmd.PNG)

## Step 3 : Copy .pem file to Jenkins server
Now go to the where your .pem key is located and copy that file to your jenkin server by using below cmd this cmd run on gitbash
```
scp -i <key-file-name> <key-file-name> username@public-ip:defaultpath

example :
scp -i jenkins-file.pem jenkins-file.pem ubuntu@54.81.24.101:/home/ubuntu
```
If you have alredy done this then skip this step.

## Step 4 : Install required plugins
In the jenkins you have to install some plugins for build CICD pipeline Fr that you have to follow below steps if you alredy installed then skip this step.
- Log in into jenkins server
- Click on Setting
- Open plugins --> Available plugins
- Search
    - ssh agen --> install
    - pipeline --> install
    - ssh build agent --> install
    - github --> install
- Now restart the jenkins.

If your already installed this plugins then skip this stage.

## Step 5 : Create Job For Portfolio deployment.
- Click on "New Item"
- Enter an item name : Portfolio-Deployment
- Select an item type : pipeline
- Click on Ok

![project screenshot](/Images/job.png)

For Python-App application code you take from this repository https://github.com/Vishu-Nargunde99/Portfoli-deployment-CICD

You can clone this repo on your local machine and upload to your GitHub account. And whenever you made change in your portfolio automatically update in portfolio.

## Step 6 : Create Credential
- Click on Setting
- In security section --> Credential --> Click on global --> Add Credential
    - Kind - SSH Username with private key
    - Scope - Global(jenkins, nodes, items)
    - ID - ssh-key-credential
    - Description - ssh-key-credential
    - Username - ubuntu
    - Private Key - Check in - Entire directly
    - Click on Add
    - Copy your private-key from your local machine
    - Create Here our credential is created.

![project screenshot](/Images/credential.PNG)

## Step 7 : Configure Job
- Go to created job "Portfolio-Deployment"
- click on Configure

![project screenshot](/Images/configure.png)

- Scoll down
- In trigger section --> check in - GitHub hook trigger GITScm polling
- Pipeline
    - Definition - Pipeline script from SCM
        - SCM - Git
            - Repository - Copy your repository HTTPS where your jenkinfile stored
            - Branch - main/master (which is your branch name)
        - Script Path - Change Jenkinsfile To jenkinfile
- Click on "Save".
- Click on "Build Now"

Here build now is successfull.

## Step 8 : Generate Automate code push
- Log in into your GitHub Account
- Go to your code repository of Nodejs
- Click on setting

- Click on webhook
- Add webhook
- payload URl
```
http://<jenkins-server-public-ip>:8080/github-webhook/

Example
http://54.81.24.101:8080/github-webhook/
```
- Add webhook
- Now you can just chnage your code then add, commit and push code to your reposotory.


Congratualion!.....You can successfully deployed your Portfolio  using CICD.