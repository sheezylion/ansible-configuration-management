# ANSIBLE CONFIGURATION MANAGEMENT (AUTOMATE PROJECT 7 TO 10)
In Projects 7 to 10 we perform a lot of manual operations to set up virtual servers, install and configure required software and deploy our web application.

This Project will make us appreciate **DevOps tools** even more by making most of the routine tasks automated with 
<a href="https://www.redhat.com/en/topics/automation/what-is-configuration-management#:~:text=Configuration%20management%20is%20a%20process,in%20a%20desired%2C%20consistent%20state.&text=Managing%20IT%20system%20configurations%20involves,building%20and%20maintaining%20those%20systems.">Ansible Configuration Management</a>,
at the same time we will become confident with writing code using declarative languages such as YAML.

## Ansible Client as a Jump Server (Bastion Host)
A **Jump Server** (sometimes also referred as **Bastion Host**) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server - it provides better security and reduces attack surface.

## Task
- Install and configure Ansible client to act as a Jump Server/Bastion Host
- Create a simple Ansible playbook to automate servers configuration

On the diagram below the Virtual Private Network (VPC) is divided into two subnets - Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

<img width="743" alt="ansible architecture" src="https://github.com/sheezylion/ansible-configuration-management/assets/142250556/325c48c3-83dc-4656-b174-f725609cad7e">

### Step 1 - Install and Configure ANSIBLE ON EC2 Instance

#### 1. Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.

<img width="1617" alt="Screenshot 2024-07-14 at 14 56 30" src="https://github.com/user-attachments/assets/58870b8a-24f6-4461-b05d-2bb4d1cb860a">

#### 2. In your GitHub account create a new repository and name it ansible-config-mgt

<img width="1633" alt="Screenshot 2024-07-14 at 15 03 40" src="https://github.com/user-attachments/assets/701d4320-fdd7-4f64-944d-7d3ff0d6f800">

#### 3. Instal Ansible

```
sudo apt update
```

Result:

<img width="770" alt="Screenshot 2024-07-14 at 15 06 42" src="https://github.com/user-attachments/assets/05ed8d4e-0f59-4914-b625-99e81d38f846">

```
sudo apt install ansible
```

Result:

<img width="1207" alt="Screenshot 2024-07-14 at 15 07 43" src="https://github.com/user-attachments/assets/4d4a937f-0113-44b8-ade7-c2f5429f059a">

#### Check your ansible version

```
ansible --version
```

Result:

<img width="1045" alt="Screenshot 2024-07-14 at 15 08 59" src="https://github.com/user-attachments/assets/8173af19-9ae4-424f-bbe3-4793f8bb42d9">

#### 4. Configure Jenkins build job to save your repository content every time you change it – this will solidify your Jenkins configuration skills acquired in Project 9

- Configure a Webhook in GitHub and set the webhook to trigger ansible build. On ansible-config-mgt repository, select Settings > Webhooks > Add webhook

Result:

<img width="1447" alt="Screenshot 2024-07-14 at 15 12 45" src="https://github.com/user-attachments/assets/723a5203-2f81-421b-ab30-95760fbea8a1">

<img width="924" alt="Screenshot 2024-07-14 at 15 12 56" src="https://github.com/user-attachments/assets/2aaaaeb6-6737-4067-8689-1935d4f534bc">

- Create a new Freestyle project ansible in Jenkins
