# Project-Base-Learning-11
Ansible Configuration Management – Automating Project 7 to 10

## PROJECT TASK

- Install and configure Ansible client to act as a Jump Server/Bastion Host
- Create a simple Ansible playbook to automate servers configuration 

## BACKGROUND KNOWLEDGE

- We can Automate routine tasks done on project 7 - 10 using [Ansible configuration management tool](https://www.redhat.com/en/topics/automation/what-is-configuration-management#:~:text=Configuration%20management%20is%20a%20process,in%20a%20desired%2C%20consistent%20state.&text=Managing%20IT%20system%20configurations%20involves,building%20and%20maintaining%20those%20systems). Projects 7 to 10 you had to perform a lot of manual operations to set up virtual servers, install and configure required software, deploy your web application. This project seeks to automate all these task. At the same time you will become confident at writing code using declarative language such as [YAML](https://en.wikipedia.org/wiki/YAML)


- Ansible Client as a Jump Server (Bastion Host). A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface. On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.  When you reach Project 15, you will see a Bastion host in proper action. But for now, we will develop Ansible scripts to simulate the use of a Jump box/Bastion host to access our Web Servers.

![11_1](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/f391c26a-11d7-4252-a240-13a8430dc81f)


## Setup and technologies used in Project 11
- 

1. - Two RHEL8 Web Servers

2. - One MySQL DB Server (based on Ubuntu 20.04 LTS)

3. - One RHEL8 NFS server

3. - One Nginx LB server(based on Ubuntu 20.04 LTS)

4. - One **Jenkins/Ansible Server** server(based on Ubuntu 20.04 LTS)

- Your target architecture will look like this:
  
![11_2](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/d60dc43a-ce48-4360-a63f-c074f49747f7)

## STEP 1      ***INSTALL AND CONFIGURE ANSIBLE ON OUR ACTIVE JENKIN SERVER***

Already Jenkins is running on our Ubuntu 20.04 server, now we will be Installing Ansible on Jenkins Server, and then on the EC2 instance, we rename it to Jenkins-Ansible. We will also use this server to run our playbooks.

#### 11_3 pix of your running instances

### 1A.In your GitHub account create a new repository and name it `ansible-config-mgt`

#### 11_4 pix of the new repo

### 1B. We install ansible on our jenkins server and rename it to Jenkins-Ansible

```
sudo apt update
sudo apt install ansible
ansible --version
```
#### 11_5 short pix showing the ansible version

### 1C. Configure Jenkins

Make reference to your project 9,  follow the step to  configure Jenkins

- Configure Jenkins build job to save your repository content every time there is an edit.

- Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.

- Configure the "ansible-config-mgt" to connect to the jenkins through webhook to trigger build automatically.

- Configure a Post-build job to save and archive all artifacts i.e (**).

- Test our setup by making some change in README.MD file

Note: Trigger Jenkins project execution only for /main (master) branch.

#### 11_6 pix showing your Jenkins config  

Now your setup will look like this:

![11_7](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/5dcf7dbd-9c66-4ec5-99fc-36866d62b6b9)

N/B: Every time the jenkins-ansible server stop/start, we have to reconfigure GitHub webhook to the new IP address. In order to avoid this, we allocate an Elastic IP to the Jenkins-Ansible server. Elastic IP is free only when it is being allocated to an EC2 Instance so we release Elastic IP once the instance is terminated.


## STEP 2      ***PREPARE THE DEVELOPMENT ENVIRONMENT USING VS CODE***

### 2A. INSTALL VS CODE

First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. VS Code is the preference tool to use here, for this project, ensure below highlighted are installed on your VS code.
 
![11_8](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/56d7499e-394c-4c2c-a204-71f5144234bf)

- After you have successfully installed VSC, [configure it to connect to your newly created GitHub repository](https://www.youtube.com/watch?v=mR9jhYD3bnI)

- Clone the ansible-config-mgt repo to the Jenkins-Ansible instance

`git clone <ansible-config-mgt repo link>`

#### 11_9 pix of this cmd executed

- cd into ansible-config-mgt

#### 11_10 pix showing your Jenkins config  


## STEP 3      ***BEGIN ANSIBLE DEVELOPMENT***

### 3A New Branch Creation in the ansible-config-mgt GitHub repository

In the ansible-config-mgt GitHub repository, We create a new branch that will be used for development of a new feature and name it feature/PJ-11

To create this branch, we run the commands

`git status`

To show the present branch. Then

`git checkout -b feature/PJ-11`

and then

`git status`

To show the branch we just changed into

#### 11_11 pix showing successful cmd execution

### 3B On your new Branch, Create a Playbooks Directory & Inventory Directory
In the new branch we will be creating some directories that we will be working with. Create a directory and name it playbooks – it will be used to store all your playbook files. Also create another directory and name it inventory – it will be used to keep your hosts organised.

#### 11_12 pix showing successful cmd execution


### 3C create your 1st Playbook  on Playbook / & an inventory file (.yml) for each environment on your Inventory /

- Within the playbooks folder, create your first playbook, and name it common.yml

- Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.

#### 11_13 pix showing successful cmd execution


## STEP 4      ***SET UP AN ANSIBLE INVENTORY***

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the `inventory/dev` file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

To setup SSH agent and connect VS Code to the Jenkins-Ansible instance. [Click here](https://www.youtube.com/watch?v=lKXMyln_5q4)

Open the link [openSSH-documentation](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell) and follow the setup procedure.

To add the key we run the command eval

```
eval `ssh-agent -s` 

ssh-add <path-to-private-key>
```

Confirm the key has been added with the command below, you should see the name of your key

`ssh-add -l`

Now, ssh into your Jenkins-Ansible server using ssh-agent

`ssh -A ubuntu@<jenkins-ansible-public-IP-address>`

#### 11_14 pix showing successful cmd execution

Also note, that your Load Balancer user is `ubuntu` and user for RHEL-based servers is `ec2-user`

To Connect the vscode to the server through ssh, click on the highlighted section in the bottom left corner





Update your inventory/dev.yml file with this snippet of code:
