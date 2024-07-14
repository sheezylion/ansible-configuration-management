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


