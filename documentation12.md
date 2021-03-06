Project 12
----------------------------

### Jenkins job enhancement

### I SSH'd into the Jenkins-Ansible-Server to create a directory named ansible-config-artifact

```
sudo mkdir /home/ubuntu/ansible-config-artifact

sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact

```
I ran ll to check permissions and owner

```
ll
```


![create aca](./images/ansible-create-aca.JPG)  

### Next step was to download the copy artifactc plugin in Jenkins

![copy artifacts](./images/copy-artifacts-installed.JPG) 

### I created a new Freestyle project and named it save_artifacts.

### This project will be triggered by completion of the existing ansible project. 


### I created a new item and configured it as such

- I checked the Discard old builds checkbox
- Set the Max # of builds to 2
- Left source code management as None
- Under Build Triggers, I selected Build after other projects are built
- For Projects to watch, I selected the previous project called ansible
- I created a build step and selected copy artifacts from another project
- For project name, I selected ansible
- Artifacts to copy was set as ** 
- For artifacts not to copy, I entered README.md
- Target directory was set as /home/ubuntu/ansible-config-artifact

### I pushed the changes made to git to check if the setup was working correctly

### The save artifacts build faled with the error  below

![copy artifacts](./images/artifacts-error.JPG)

### I did a chmod -R 777 against /home

![copy artifacts](./images/artifacts-1.JPG) 

![copy artifacts](./images/artifacts-2.JPG) 

### Next I used git pull origin main to update the main local branch
```
git pull origin main
```
### I then created a new branch refactor and switched to it

```
git checkout -b refactor
```
![refactor 1](./images/refactor-1.JPG) 

- I created a new file site.yml in the playbook folder
- I created a folder named static-assignments in the root folder
- I moved playbooks/common.yml to the static-assignments folder
- Next, I imported common.yml playbook into the site.yml file

### The next step was to remove wireshark that I installed previously on all other servers

- I created another playbook under static-assignments called common-del.yml to delete the wireshark utility
```
touch static-assignments/common-del.yml
```
### I posted the code below into common-del.yml

```
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    yum:
      name: wireshark
      state: removed

- name: update LB server
  hosts: lb
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: delete wireshark
    apt:
      name: wireshark-qt
      state: absent
      autoremove: yes
      purge: yes
      autoclean: yes
```


