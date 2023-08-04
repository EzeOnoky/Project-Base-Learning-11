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


  
