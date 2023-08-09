# Project-Base-Learning-11
Ansible Configuration Management – Automating Project 7 to 10

## PROJECT TASK

- Install and configure Ansible client to act as a Jump Server/Bastion Host
- Create a simple Ansible playbook to automate servers configuration 

## BACKGROUND KNOWLEDGE

- We can Automate routine tasks done on project 7 - 10 using [Ansible configuration management tool](https://www.redhat.com/en/topics/automation/what-is-configuration-management#:~:text=Configuration%20management%20is%20a%20process,in%20a%20desired%2C%20consistent%20state.&text=Managing%20IT%20system%20configurations%20involves,building%20and%20maintaining%20those%20systems). Projects 7 to 10 you had to perform a lot of manual operations to set up virtual servers, install and configure required software, deploy your web application. This project seeks to automate all these task. At the same time you will become confident at writing code using declarative language such as [YAML](https://en.wikipedia.org/wiki/YAML) - YAML is the main language we use in Ansible to write codes.


- Ansible Client as a Jump Server (Bastion Host). A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface(the attacker can only enter via the JENKIN/ANSIBLE Server - jump server).

- On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.  When you reach Project 15, you will see a Bastion host in proper action. But for now, we will develop Ansible scripts to simulate the use of a Jump box/Bastion host to access our Web Servers.

![11_1](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/f391c26a-11d7-4252-a240-13a8430dc81f)


## Setup and technologies used in Project 11
- 

1. - Two RHEL8 Web Servers

2. - One RHEL8 DB Server

3. - One RHEL8 NFS server

3. - One LB server(based on Ubuntu 20.04 LTS)

4. - One **Jenkins/Ansible Server** server(based on Ubuntu 20.04 LTS)

- Your target architecture will look like this:
  
![11_2](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/d60dc43a-ce48-4360-a63f-c074f49747f7)

## STEP 1      **INSTALL AND CONFIGURE ANSIBLE ON OUR ACTIVE JENKIN SERVER**

Note, prior to above step 1, your servers running on RHEL8, 4 of them - (the 2 web server, DB, NFS), your Ubuntu 20.04 LB & Ubuntu 20.04 Jenkins would have been configured - refer to project 7, 8, 9 respectively for some guidance on this setup. 

Already Jenkins is running on our Ubuntu 20.04 server, now we will be Installing Ansible on Jenkins Server, and then on the EC2 instance, we rename it to Jenkins-Ansible. We will also use this server to run our Ansible Playbooks.

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

- Create a new Freestyle project **ANSIBLE** in Jenkins and point it to your ‘ansible-config-mgt’ repository.

- Configure the "ansible-config-mgt" to connect to the jenkins through webhook to trigger build automatically. webhook triggers the build automatically

- Configure a Post-build job to save and archive all artifacts i.e (**).

- Test our setup by making some change in README.MD file

Note: Trigger Jenkins project execution only for /main (master) branch.

#### 11_6 pix showing your Jenkins config  

Now your setup will look like this:

![11_7](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/5dcf7dbd-9c66-4ec5-99fc-36866d62b6b9)
***Diagram Explained*** - Your Webhook auto triggers changes on your Jenkins/Ansible server, the change comes to the NFS server, the NFS now supplies Web1 & Web2.

N/B: Every time the jenkins-ansible server stop/start, we have to reconfigure GitHub webhook to the new IP address. In order to avoid this, we allocate an Elastic IP to the Jenkins-Ansible server. Elastic IP is free only when it is being allocated to an EC2 Instance so we release Elastic IP once the instance is terminated.


## STEP 2      **PREPARE THE DEVELOPMENT ENVIRONMENT USING VISUAL STUDIO CODE**

VISUAL STUDIO CODE is a text Editor which limits your back and forth between your SSH Client window, GIT Hub repository Window etc. It bring all to one interface, so with CMD line you can interface with ALL.

### 2A. INSTALL VS CODE

First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. VS Code is the preference tool to use here, for this project, ensure below highlighted are installed on your VS code.

VS Code is a free open source coding editor, GIT Hub is a version control platform for hosting development projects. So configuring VS Code to connect to your newly created GitHub repository, makes it easier for you to manage projects from inside a code editor - VS Code.

Note : Remote Development on the VS Code - Helps open SSH on remote servers
 
![11_8](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/56d7499e-394c-4c2c-a204-71f5144234bf)


- After you have successfully installed VSC, [configure it to connect to your newly created GitHub repository](https://www.youtube.com/watch?v=mR9jhYD3bnI)

- Clone the ansible-config-mgt repo to the Jenkins-Ansible instance

Below is to be executed from your VS Code Editor - Click on Terminal to begin execution

`git clone <ansible-config-mgt repo link>`

#### 11_9 pix of this cmd executed

- cd into ansible-config-mgt

#### 11_10 pix showing your Jenkins config  


## STEP 3      **BEGIN ANSIBLE DEVELOPMENT**

### 3A New Branch Creation in the ansible-config-mgt GitHub repository

In the ansible-config-mgt GitHub repository, We create a new branch that will be used for development of a new feature and name it feature/PJ-11

To create this branch, we run the commands

1st confirm the branch you are currently on ....
`git status`

Then proceed to create a Branch...

`git checkout -b PJ-11`

and then

`git status`

Notice the prompt will change to the new branch we just created i.e **prj-11**. If you also check the bottom left corner of your VS Code, you will see the newly created branch. When you switch to the **Main** branch, the prompt will change.

![11_10A](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/d5f2c159-d105-4bf0-b7ff-2efbf36ecd53)


#### 11_11 pix showing successful cmd execution

### 3B On your new Branch, Create a Playbooks Directory & Inventory Directory
In the new branch we will be creating some directories that we will be working with. Create a directory and name it playbooks – it will be used to store all your playbook files. Also create another directory and name it inventory – it will be used to keep your hosts organised.

#### 11_12 pix showing successful cmd execution


### 3C On Playbook Directory-Create your 1st Playbook & On Inventory Directory-Create An inventory file (.yml) for each environment

- Within the playbooks folder, create your first playbook, and name it common.yml

```
cd inventory
touch common.yml
```

- Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.
- 
```
cd inventory
touch dev.yml staging.yml uat.yml prod.yml
```

**NOTE** Aside command line used above, there is option to use GUI(click click...) to create these files inside the folders in the new branche.

#### 11_13 pix showing successful cmd execution


## STEP 4      **SET UP AN ANSIBLE INVENTORY**

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the `inventory/dev` file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

### 4A To setup SSH agent and connect VS Code to the Jenkins-Ansible instance.

For a guide on how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance
[Click here](https://www.youtube.com/watch?v=lKXMyln_5q4) 
[Click here](https://(www.youtube.com/watch?v=OplGrY74qog)

[How to connect to Bastion Host on Visual Studio code via SSH Agent](https://www.youtube.com/watch?v=RRRQLgAfcJw&list=PLtPuNR8I4TvlBxy8IUXUDnmtlKawRsWH_&t=1445s)
See at the bottom - **EXTRA** for documentation on this...

Open the link [openSSH-documentation](https://learn.microsoft.com/en-us/windows-server/administration/openssh/openssh_install_firstuse?tabs=powershell) and follow the setup procedure.

To add our pem key to the SSH agent we run the command eval

**NOTE** Execute below on VS Code editor...

```
eval `ssh-agent -s` 

ssh-add <path-to-private-key>
```
example - `ssh-add Desktop/DevOps/pj7.pem`

Confirm the pem key has been added with the command below, you should see the name of your key

`ssh-add -l`

Now, ssh into your Jenkins-Ansible server using ssh-agent

`ssh -A ubuntu@<jenkins-ansible-public-IP-address>`

#### 11_14 pix showing successful cmd execution

So with this done, our Ansible server will be able to access the other instances(Web server, NFS, DB, LB) using this pem key. on our SSH agent

Also note, that your Load Balancer user is `ubuntu` and user for RHEL-based servers is `ec2-user` So to access the Ubuntu or the RHEL server...just grap the private IP of the specific server you want to access and run below...

`ssh ubuntu@<LB-Private-IP-address>`

`ssh ec2-user@<NFS-Private-IP-address>`

`ssh ec2-user@<WEB Server-Private-IP-address>`

# 1 check if needed

To Connect the vscode to the server through ssh, click on the highlighted section in the bottom left corner

![11_15](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/921fd916-6574-4374-987e-0731d8e5f217)

Search for "ssh", then select "open ssh configuration file" from the options. Select `c:\Users\user\.ssh\config`

Insert the following

```
host: Jenkins-ansible
    hostname: <public-dns-of-jenkins-ansible-server> 
    user: ubuntu
    IdentityFile C:\Users\USER\Downloads\dybran-ec2.pem
```
![11_15](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/921fd916-6574-4374-987e-0731d8e5f217)
Click on the highlighted section in the bottom left corner again, and then select "connect to host". From the options, select the host you created in the ssh configuration file. This connects the jenkins-ansible server to vscode remotely through ssh

# 1 end

#### 11_16 pix showing successful connection to ssh on VSCODE

Now we try to connect remotely through ssh to other servers, e.g Connect to web02 remotely

`ssh ec2-user@<web02-IP-address>`

#### 11_17 pix showing successful connection to web server on VSCODE

Update the /etc/hosts/ of the jenkins-ansible server with the webservers, database, nfs server and load balancer private IP address.

`sudo vi /etc/hosts`

#### 11_18 pix showing successful adding of the private IPs

### 4B Update the inventory/dev.yml file

Update your inventory/dev.yml file with this snippet of code:

#### update below with your own record
```
NFS ansible_host=<NFS-Private-IP> ansible_ssh_user=ec2-user ansible_ssh_private_key_file=dybran-ec2.pem

WEB1 ansible_host=<WEB1-Private-IP> ansible_ssh_user=ec2-user ansible_ssh_private_key_file=dybran-ec2.pem

WEB2 ansible_host=<WEB2-Private-IP> ansible_ssh_user=ec2-user ansible_ssh_private_key_file=dybran-ec2.pem

DBS ansible_host=<DB-Private-IP> ansible_ssh_user=ec2-user ansible_ssh_private_key_file=dybran-ec2.pem

LB ansible_host=<LB-Private-IP> ansible_ssh_user=ubuntu ansible_ssh_private_key_file=dybran-ec2.pem

[NFS]
<NFS-Private-IP> ansible_ssh_user='ec2-user'

[WEB]
<WEB1-Private-IP> ansible_ssh_user='ec2-user'
<WEB2-Private-IP> ansible_ssh_user='ec2-user'

[DB]
<DB-Private-IP> ansible_ssh_user='ec2-user'

[LB]
<LB-Private-IP> ansible_ssh_user='ubuntu'
```
The above a simply telling ansible what users(Ubuntu or ec2-user) is matched to each IP

#### 11_19 pix showing what was updated on inventory/dev.yml file

## STEP 5      **CREATE A COMMON PLAYBOOK**

It is time to start giving Ansible the instructions on what you needs to be performed on all servers listed in `inventory/dev`

In `common.yml` playbook, we will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure

Update your `playbooks/common.yml` file with following code:

On your VS Code, click on the common.yml file and paste below...

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs-server, db01
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: load-balancer
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```

Above playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on your RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.The above playbook will install the latest wireshark utility to the various servers.

Feel free to update this playbook with following tasks:

- Create a directory and a file inside it
- Change timezone on all servers
- Run some shell script

Further explaination of each line in our playbook task, see below bold faced...

**name: update web, nfs and db servers**  -   *description of what you want to do*

**hosts: webservers, nfs-server, db01**  - *servers are grouped based on their server types - tis is RHEL group*

**remote_user: ec2-user**  - *this is about how you want to access the servers, ec2-user is used here*

**become: yes**  -  *means you are telling this user to become the sudo user*

**become_user: root** -  *run the installation as a root user*

**tasks:**

**name: ensure wireshark is at the latest version**

**yum:**  - *package manager for RedHart is yum, for Ubuntu u would see apt*

**name: wireshark** - *name of the application to be installed*

**state: latest**  - *you are telling wireshark to install the latest version*

**---**  -  *this is seen at the begining, You satrt all YMAL file with this ---*


For a better understanding of Ansible playbooks – [watch this video from RedHat](https://www.youtube.com/watch?v=ZAdJ7CdN7DY) and read this [article](https://www.redhat.com/en/topics/automation/what-is-an-ansible-playbook). 

#### 11_20 pix showing your ansible playbook config


## STEP 6      **UPDATE GIT WITH THE LATEST CODE**

Now all of the directories and files are in the local machine(our computer) and we need to push changes made locally to GitHub.

Ideally, We will be working within a team of other DevOps engineers and developers where collaboration is a key component. GIT helps us collabrate in a DevOps team . In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes – it is also called "Four eyes principle".

Now we need to raise a **pull request** for the feature/pj-11 branch we created and the branch peer reviewed and merged to the **main** branch.

### 6A Commit your code into GitHub
video 1:00 - 

Firstly, list all the files you are yet to committed/sent over to GIT using below...
`git status`

![11_20A](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/41996fb2-c78c-45a2-9ca1-4a8fc0307f67)

Now proceed to commit the changes...

```
git add <selected files>
git commit -m "commit message"
```
![11_20B](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/7c764119-6ed9-4cee-8ffe-d57af5c66dec)

So after committing changes for ALL file (./), now we proceed to commit changes made on the playbook directory( ../playbooks/

![11_20c](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/73971db1-c115-4911-b1b3-2f9b992a10a8)

Now We push the new brance to the remote branch

![11_20d](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/7a382bbf-2c1d-4786-a0d2-0c51003a56e5)


### 6B Create a Pull request (PR)

Now return to GIT Hub, follow below steps to pull the request

#### 11_22 pix showing how to create a pull request

The pull request will be reviewed and if it is approved, it will be merged to the main branch. You act as the reviewer, approve and merge the process...

### 6B Merging the code to the main branch

#### 11_23 pix showing how to merge the code to the main branch

### 6C Exit from the Feature Branch to the main branch

After the merge is done, the inventory and playbooks directories are updated in the main branch.

Switch to the main branch

`git checkout main`

then download the updated copy

`git pull`

As seen below, the pull commmand updates what we have in the project branch(Feauture branch prj-11) into our master(main) branch 

![11_20e](https://github.com/EzeOnoky/Project-Base-Learning-11/assets/122687798/fd5f97f0-ae65-4f53-8b27-29dddc745e90)


When this is done, our jenkins builds the artifacts automatically...see below

#### 11_23 pix showing your current jenkins build

...then saves the files in the /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ directory on the jenkins-ansible server. This can also be confirmed on the Jenkins login

## STEP 7      **RUN FIRST ANSIBLE TEST**

Now, it is time to execute ansible-playbook command and verify if your playbook actually works

We cd into **ansible-config-mgt**, To test if we can access the other servers, we run the 2nd & 3rd command below

```
cd ansible-config-mgt
ansible-playbook -i inventory/dev.yml playbooks/common.yml
ansible all -m ping -i /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/inventory/dev.yml
```
#### 11_26 pix showing successful execution of 2nd & 3rd CMD
Note: if we do not want "host key checking", We can uncomment `#host_key_checking=False` in the `/etc/ansible/ansible.cfg file`.

#### 11_26 pix showing the uncommenting of  `#host_key_checking=False` 

To run our playbook, We check if the syntax is **OK** by running the command

`ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/playbooks/common.yml --syntax-check`

If the syntax is OK, the playbook will be displayed. i.e `playbook: <playbook-file-path>`

For a Dry Run to see what actually happens when you run the playbook

`ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/inventory/dev.yml var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/playbooks/common.yml -C`

To run the playbook

`ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/playbooks/common.yml`

#### 11_27 pix showing successful run of the playbook


If there are no errors, check if wireshark is installed in each of the servers by logging into the servers and running the command

`which wireshark` 
or 
`wireshark --version`

#### 11_28 pix showing successful wireshark installation

**Note** : Ansible is **Idempotent**. Idempotency means that if an ansible playbook is run severally, it only makes the change to the server once unless there is a change in the playbook.

# CONGRATULATIONS EZE ! You have just automated routine tasks using Ansible Configuration Management Tool


# EXTRAS

### How to connect to Bastion Host on Visual Studio code via SSH Agent

SSH Agent - a concept where you have the private key of an Ansible server, running in another active instance, from the SSH agent, you can connect directly to the active instance.

`eval 'ssh-agent'`  .....this starts the SSH agent

`ssh-add -k <pem key name>`  ......this adds the pem key, ensure this is executed from the folder where the pem key is located

`ssh-add -l` this will show you the active keys in your Key chain

When you connect via SSH agent, you dont have to copy the connection url and launch it on your terminal. you run below CMD.

`ssh -A ec2-user@3.68.101.75`   .... 3.68.101.75 is the public IP of your Master  Ansible server

`ssh-add -l`   .... Once connected on Master Ansible server, run to this command to add the pem key chain to your Ansible Master

`ssh ec2-user@172.31.166.45`  ...While on the Master Ansible server, your can now SSH into the slave ansible server using its private IP, or anyother server u want to

SO BEFORE YOU RUN ANSIBLE PLAYBOOK, ALWAYS ENSURE YOUR ANSIBLE MASTER CAN CONNECT TO THE OTHER SERVERs(Ansible slave) VIA SSH AGENT

exit   ....this exits u from the slave Ansible into the master ansible

Ansible is installed on MASTER ANSIBLE, after Ansible is installed here, you have to update the INVENTORY

INVENTORY - This is where Ansible goes to excute commands based on the expected task to be executed. you have to update private IP of your Ansible slaves here

So After the inventory has been updated, CD into Ansible directory, & attempt to run below Ansible command, if u get a sucess message, this means your Bastion host(Ansible Master) can successful execute cmd on the slave server

`ansible -i inventory -m ping test`

**SO WHY VS CODE...**
Notice when u connect to an instance and need to use a test Editor, u have to exit the terminal, make changes on your text editor, save the changes and return to your terminal prompt.
With VS Code, you dont need to be doing this switch. From VS Code , u can access both terminal and your text editor all at once

B4 u connect VS Code to your Master Ansible, u must make sure SSH agent is running on the Master Ansible server

1st ensure the SSH agent pem key has been added... `run ssh-add -l`
If you dont see the pem key on the pem key chain....use below to add it.

`eval 'ssh-agent'`  .....this starts the SSH agent

`ssh-add -k <pem key name>` ......this adds the pem key, ensure this is executed from the folder where the pem key is located

`ssh-add -l` .... this will show you the active keys in your Key chain


