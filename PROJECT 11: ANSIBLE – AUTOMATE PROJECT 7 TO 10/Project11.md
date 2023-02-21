# ANSIBLE CONFIGURATION MANAGEMENT – AUTOMATE PROJECT 7 TO 10


In Projects 7 to 10 we had to perform a lot of manual operations to seet up virtual servers, install and configure required software, deploy your web application.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

<img width="398" alt="image" src="https://user-images.githubusercontent.com/111741533/220304514-12b03f1f-b547-463a-ac3f-5c8b2461d600.png">

### Tasks

1. Install and configure Ansible client to act as a Jump Server/Bastion Host
2. Create a simple Ansible playbook to automate servers configuration


## Step 1: INSTALL AND CONFIGURE ANSIBLE ON EC2 INSTANCE

- Update Name tag on your Jenkins EC2 Instance to Jenkins-Ansible. We will use this server to run playbooks.
- In your GitHub account create a new repository and name it [ansible-config](https://github.com/iamRichardCN/ansible-config).
- Instal Jenkins and Ansible
- To set up the jenkins we followed the steps in [projet 9](https://github.com/iamRichardCN/DEVOPS-PROJECTS/blob/main/PROJECT%209:%20CONTINOUS%20INTEGRATION%20PIPELINE%20FOR%20TOOLING%20WEBSITE/project9.md)

<img width="736" alt="install JENKINS" src="https://user-images.githubusercontent.com/111741533/220312052-840b0c13-ff1d-4912-8c10-b474fadb2c77.png">


- Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config’ repository.
- Configure Webhook in GitHub and set webhook to trigger ansible build.
- Configure a Post-build job to save all (**) files, like you did it in Project 9

- Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder



```ls /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/```


<img width="703" alt="confirm jenkins build" src="https://user-images.githubusercontent.com/111741533/220312624-2942d22e-d102-4237-bc75-8227aa8854ea.png">



Install **Ansible**
```
sudo apt update
sudo apt install ansible
```

<img width="824" alt="ansible version" src="https://user-images.githubusercontent.com/111741533/220307890-abbcb535-deda-4957-a6fe-2996593d48f5.png">

**Note:** Trigger Jenkins project execution only for /main (master) branch.
Now the setup will look like this:


<img width="474" alt="image" src="https://user-images.githubusercontent.com/111741533/220314737-a82eb1a0-ea46-4d7e-93e5-1655d416af2c.png">

## Step 2 – Prepare the development environment using Visual Studio Code

Install Visual Studio Code software on your local machine, then connect to your new Github repo

In the ansible-configGitHub repository, create a new branch that will be used for development of a new feature (the branch name was (PRJ-11).

**start the development**
- Checkout the newly created feature branch to your local machine and start building your code and directory structure
- Create a directory and name it **playbooks** – it will be used to store all the playbook files.
- Create a directory and name it **inventory** – it will be used to keep the hosts organised.
- Within the playbooks folder, create your first playbook, and name it common.yml
- Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively

### set up an Ansible Inventory

Save below inventory structure in the inventory/dev file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

**Note**: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host – for this you can implement the concept of ssh-agent. Now you need to import your key into ssh-agent:

To learn how to setup SSH agent and connect VS Code to your Jenkins-Ansible instance, please see this video:

- For Windows users – ssh-agent on windows
- For Linux users – ssh-agent on linux

```
eval `ssh-agent -s`
ssh-add <path-to-private-key
```

<img width="495" alt="ssh and perm key settings" src="https://user-images.githubusercontent.com/111741533/220317057-dadaacc4-6fff-4b06-b6fe-0dea943b6ac5.png">

Test the communication among servers

<img width="359" alt="testing communication" src="https://user-images.githubusercontent.com/111741533/220318114-7b428e7c-71c0-433e-b257-ff3f76278d1a.png">


Update the ```inventory/dev.yml``` file with this snippet of code:

```
[nfs]
<NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
[webservers]
<Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
<Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'
[db]
<Database-Private-IP-Address> ansible_ssh_user='ec2-user' 
[lb]
<Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
```

### **CREATE A COMMON PLAYBOOK**

In common.yml playbook we write configuration for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update your ```playbooks/common.yml``` file with following code:

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

- name: update LB server
  hosts: lb
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

### Step 6 – Update GIT with the latest code

Now all of the directories and files live on the local machine and you need to push changes made locally to GitHub.

Now we have a separate branch, we will need to know how to raise a Pull Request (PR), get the branch peer reviewed and merged to the master branch.

Commit your code into GitHub:

use git commands to add, commit and push your branch to GitHub.


```
git status

git add <selected files>

git commit -m "<commit message>"
```
Create a Pull request (PR)

Wear a hat of another developer for a second, and act as a reviewer.

If the reviewer is happy with your new feature development, merge the code to the master branch.

Head back on your terminal, checkout from the feature branch into the master, and pull down the latest changes.

Once your code changes appear in master branch – Jenkins will do its job and save all the files (build artifacts) to

```/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/```

Directory on Jenkins-Ansible server.

However, you may have to change the ownership of your /mnt directory in the NFS server too nobody:nobody if you have permission denied in the output of your Jenkins build.

```sudo chown -R nobody:nobody /mnt```

### RUN FIRST ANSIBLE TEST

Run first Ansible test Now, it is time to execute ansible-playbook command and verify if your playbook actually works:
```
ansible-playbook -i /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/inventory/dev.yml /var/lib/jenkins/jobs/ansible/builds/<build-number>/archive/playbooks/common.yml
```

<img width="947" alt="updated ansible output" src="https://user-images.githubusercontent.com/111741533/220322207-6e771909-be5f-493e-bafc-3c368435cafd.png">


You can go to each of the servers and check if wireshark has been installed by running which wireshark or wireshark --version

<img width="758" alt="final confirmation which wireshark" src="https://user-images.githubusercontent.com/111741533/220322956-059565a2-6308-4f4e-8096-759d4bf84a63.png">










