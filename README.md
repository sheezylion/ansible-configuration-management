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

Result:

<img width="1642" alt="Screenshot 2024-07-14 at 15 15 28" src="https://github.com/user-attachments/assets/32717e23-a8d4-4b72-895b-9bc8c8d131d6">

- Point it to the ansible-config-mgt repository Copy the repository URL

Result:

<img width="1622" alt="Screenshot 2024-07-14 at 15 16 21" src="https://github.com/user-attachments/assets/2dbcd588-ee04-4801-8bde-d445a8e7d393">

#### In configuration of our ansible freestyle project choose Git, provide there the link to our ansible-config-mgt GitHub repository and credentials (user/password) so Jenkins could access files in the repository.

Result:

<img width="1557" alt="Screenshot 2024-07-14 at 15 18 05" src="https://github.com/user-attachments/assets/03515723-378b-4db1-b4df-9b7cd133d4af">

<img width="1419" alt="Screenshot 2024-07-14 at 15 19 20" src="https://github.com/user-attachments/assets/72ae180c-8073-41c1-bc97-2b38a1fac755">

- Configure a Post-build job to save all (**) files, like you did it in Project 9.

<img width="1665" alt="Screenshot 2024-07-14 at 15 21 15" src="https://github.com/user-attachments/assets/37c3281c-e6ea-4531-83e5-0be13d2abf12">

#### 5. Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder

Result:

<img width="1679" alt="Screenshot 2024-07-14 at 15 24 29" src="https://github.com/user-attachments/assets/0c5c574d-5726-4632-ae8b-01f8cb9da0d1">

#### Check ansible project on jenkins for the build

Result:

<img width="1679" alt="Screenshot 2024-07-14 at 15 27 58" src="https://github.com/user-attachments/assets/de1443ba-e8dc-45dd-a60a-796fe3c9d8d6">

<img width="1680" alt="Screenshot 2024-07-14 at 15 28 12" src="https://github.com/user-attachments/assets/0af764f8-8243-43ed-91b7-06624386d3a9">

```
ls /var/lib/jenkins/jobs/Ansible/builds/<build_number>/archive/
```

Result:

<img width="881" alt="Screenshot 2024-07-14 at 15 32 39" src="https://github.com/user-attachments/assets/c59d7958-cd5a-41f8-a4d6-9950bd48c75d">


### Note: Trigger Jenkins project execution only for /main (master) branch.

#### Now your setup will look like this:

<img width="781" alt="architecture2" src="https://github.com/user-attachments/assets/953e23b4-5c77-4645-a404-0b5462718e58">

**Tip:** Allocate an Elastic IP to your Jenkins-Ansible server to avoid reconfigure of GitHub webhook to a new IP address anytime you stop/start your Jenkins-Ansible server.

Allocate elastic IP:

Result:

<img width="953" alt="Screenshot 2024-07-14 at 15 38 22" src="https://github.com/user-attachments/assets/76337f82-1e1e-41b0-a564-9ef489f22641">

<img width="1678" alt="Screenshot 2024-07-14 at 15 38 48" src="https://github.com/user-attachments/assets/f2d700bc-cd2e-46de-9e65-be784bf492e4">

<img width="1004" alt="Screenshot 2024-07-14 at 15 39 07" src="https://github.com/user-attachments/assets/8213bd7f-14a1-4971-9b4c-13ccf9011f37">

#### Update the webhook

Result:

<img width="1514" alt="Screenshot 2024-07-14 at 15 41 33" src="https://github.com/user-attachments/assets/ccc7fbfd-8ef9-4c1c-9f00-859f498f5b96">

**Note:** Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

### Step 2 – Prepare your development environment using Visual Studio Code

#### 1. First part of DevOps is Dev, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor.

There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC).

#### 2. After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.

Result:

<img width="1680" alt="Screenshot 2024-07-14 at 16 18 08" src="https://github.com/user-attachments/assets/cf7b03c1-0dde-4c68-a40b-07be8c7e248a">

```
https://github.com/sheezylion/Ansible-config-mgt.git
```

#### 3. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance git clone <ansible-config-mgt repo link>

Result:

<img width="897" alt="Screenshot 2024-07-14 at 16 20 55" src="https://github.com/user-attachments/assets/e6bfa212-1b9c-417a-a8c7-cb0c1d2559a7">

### Step 3 - Begin Ansible Development

#### 1. In your ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature

**Tip:** Give your branches descriptive and comprehensive names, for example, if you use Jira or Trello as a project management tool - include ticket number (e.g. PRJ-num) in the name of your branch and add a topic and a brief description what this branch is about - a bugfix, hotfix, feature, release (e.g. feature/prj-145-lvm)

```
git checkout -b feature/prj-11-ansible-config
```

Result:

<img width="1667" alt="Screenshot 2024-07-14 at 16 31 39" src="https://github.com/user-attachments/assets/24d5c4d8-9bd3-4f03-9a44-520b04d248eb">

#### 2. Checkout the newly created feature branch to your local machine and start building your code and directory structure

```
git fetch
git checkout feature/prj-11-ansible-config
```

#### 3. Create a directory and name it playbooks - it will be used to store all your playbook files.

```
mkdir playbooks
```

#### 4. Create a directory and name it inventory - it will be used to keep your hosts organised

```
mkdir inventory
```

Result:

 <img width="1047" alt="Screenshot 2024-07-14 at 16 36 18" src="https://github.com/user-attachments/assets/553ed6ba-3f8a-4f1c-92a6-0c33e3c1d647">

 #### 5. Within the playbooks folder, create your first playbook, and name it common.yml

```
touch playbooks/common.yml
```

#### 6. Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging, Testing and Production) dev, staging, uat, and prod respectively.

```
touch inventory/dev.yml inventory/staging.yml inventory/uat.yml inventory/prod.yml
```

These inventory files use .ini languages style to configure Ansible hosts.

Results:

<img width="1389" alt="Screenshot 2024-07-14 at 16 39 31" src="https://github.com/user-attachments/assets/06296f3f-0d77-418c-a27e-e42fb37921ad">

<img width="1072" alt="Screenshot 2024-07-14 at 16 39 42" src="https://github.com/user-attachments/assets/18e62434-78a8-4c14-8ac9-491ea65b7000">

### Step 4 - Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts, and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory

Save the below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host - for this you can implement the concept of < a href="https://smallstep.com/blog/ssh-agent-explained/">ssh-agent</a>. Now you need to import your key into ssh-agent:

#### Start the SSH Agent:

This starts the SSH agent in your current terminal session and sets the necessary environment variables.

```
eval `ssh-agent -s`
```

#### Add Your SSH Key:

Add your SSH private key to the agent. replace the path with the correct path to the private key.

```
ssh-add <path-to-private-key>
```

Result:

<img width="1094" alt="Screenshot 2024-07-14 at 16 48 03" src="https://github.com/user-attachments/assets/cf9efe62-f9b0-4479-898a-811fdb135048">

#### Verify the Key is Loaded:

Check that your key has been successfully added to the SSH agent. you should see the name of your key

```
ssh-add -l
```

Result:
<img width="1080" alt="Screenshot 2024-07-14 at 16 49 11" src="https://github.com/user-attachments/assets/aad17c2d-a531-4190-b624-04caec10403b">

#### Now, ssh into your Jenkins-Ansible server using ssh-agent

```
ssh -A ubuntu@public-ip
```

Result:

<img width="1117" alt="Screenshot 2024-07-14 at 16 51 57" src="https://github.com/user-attachments/assets/0bc84631-bc43-4c06-ba32-87815a7ef06c">

Also notice, that your Load Balancer user is ubuntu and user for RHEL-based servers is ec2-user

Update your inventory/dev.yml file with this snippet of code:

```
all:
  children:
    nfs:
      hosts:
        <NFS-Server-Private-IP-Address>:
          ansible_ssh_user: ec2-user
    webservers:
      hosts:
        <Web-Server1-Private-IP-Address>:
          ansible_ssh_user: ec2-user
        <Web-Server2-Private-IP-Address>:
          ansible_ssh_user: ec2-user
    db:
      hosts:
        <Database-Private-IP-Address>:
          ansible_ssh_user: ubuntu
    lb:
      hosts:
        <Load-Balancer-Private-IP-Address>:
          ansible_ssh_user: ubuntu
```

Result:

<img width="1599" alt="Screenshot 2024-07-14 at 18 55 02" src="https://github.com/user-attachments/assets/8c2ad181-f355-4907-a3a0-712ec33ad6ef">

### Step 5 - Create a Common Playbook

It is time to start giving Ansible the instructions on what you need to be performed on all servers listed in inventory/dev

In common.yml playbook you will write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your playbooks/common.yml file with following code

```
---
- name: Update web and NFS servers
  hosts: webservers, nfs
  remote_user: ec2-user
  become: true
  become_user: root
  tasks:
    - name: Ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: Update LB and DB servers
  hosts: lb, db
  remote_user: ubuntu
  become: true
  become_user: root
  tasks:
    - name: Update apt repo
      apt:
        update_cache: yes

    - name: Ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest
```

Result:

<img width="1562" alt="Screenshot 2024-07-14 at 18 57 11" src="https://github.com/user-attachments/assets/41b8c190-feb4-483f-96f3-650450d94244">

Examine the code above and try to make sense out of it. This playbook is divided into two parts, each of them is intended to perform the same task :

install wireshark utility (or make sure it is updated to the latest version) on your RHEL 9 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 9 and apt for Ubuntu.

Feel free to update this playbook with following tasks:

- Create a directory and a file inside it

- Change timezone on all servers

- Run some shell script

### Step 6 - Update GIT with the latest code

Now all of your directories and files live on your machine and you need to push changes made locally to GitHub.

In the real world, you will be working within a team of other DevOps engineers and developers. It is important to learn how to collaborate with help of GIT. In many organisations there is a development rule that do not allow to deploy any code before it has been reviewed by an extra pair of eyes - it is also called Four eyes principle. Now you have a separate branch, you will need to know how to raise a Pull Request (PR), get your branch peer reviewed and merged to the main branch.

### Commit your code into GitHub:

#### 1. Use git commands to add, commit and push your branch to GitHub.

```
git status

git add <selected files>

git commit -m "commit message"

git push origin <the feature branch>
```

#### 2. Create a Pull Request (PR)

Result:

<img width="1233" alt="Screenshot 2024-07-14 at 19 04 29" src="https://github.com/user-attachments/assets/679269e9-bd7b-4295-a4cc-2e96040b2c3b">

<img width="1291" alt="Screenshot 2024-07-14 at 19 05 17" src="https://github.com/user-attachments/assets/69a5b925-8945-4d64-a056-a569d4f1b052">

<img width="1261" alt="Screenshot 2024-07-14 at 19 06 02" src="https://github.com/user-attachments/assets/f3698dac-9b6b-46af-8409-8027b48420e0">

#### 3. Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes

Result:

<img width="1385" alt="Screenshot 2024-07-14 at 19 09 06" src="https://github.com/user-attachments/assets/07f548a0-e33a-4001-8113-160fe40ddfd5">

<img width="1645" alt="Screenshot 2024-07-14 at 19 09 33" src="https://github.com/user-attachments/assets/5437831e-d68a-427d-9763-1607419212b4">

#### Once your code changes appear in main branch - Jenkins will do its job and save all the files (build artifacts) to

Result:

<img width="1027" alt="Screenshot 2024-07-14 at 19 17 08" src="https://github.com/user-attachments/assets/b42d3bf6-86d9-417e-a485-064b67fc9451">

Output console

<img width="1671" alt="Screenshot 2024-07-14 at 19 19 16" src="https://github.com/user-attachments/assets/2401fcb7-56bd-480d-a55a-0cc5bb993dd1">

Check the artifact directory

```
/var/lib/jenkins/jobs/Ansible/builds/<build_number>/archive/
```

Result:

<img width="932" alt="Screenshot 2024-07-14 at 19 22 21" src="https://github.com/user-attachments/assets/06aa9838-5326-4927-a8fa-f4f46339833d">


### Step 7 - Run first Ansible test

Now, it is time to execute ansible-playbook command and verify if your playbook actually works: first setup our vs code to connect our instance for remote development, follow these steps:

Result:
<img width="1339" alt="Screenshot 2024-07-14 at 19 59 51" src="https://github.com/user-attachments/assets/9ee7b991-6205-4c1a-bd74-ee7e2aeccec1">


