# katieavventogithub
This repository contains all my UPenn Bootcamp projects and work. 
## Automated ELK Stack Deployment
Kathleen Avvento

The files in this repository were used to configure the network depicted below.

! https://drive.google.com/file/d/1hrlPIOM1ZgHDYPzvTu98XRsFrmfXLeHM/view?usp=sharing

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the filebeat-playbook.yml file may be used to install only certain pieces of it, such as ELK.
InstallELk.yml
---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azdmin
  become: true
  tasks:

    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

    - name: Install Docker module
      pip:
        name: docker
        state: present

    - name: Increase Virtual Memory
      command: sysctl -w vm.max_map_count=262144

    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: '262144'
        state: present
        reload: yes

    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044

    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes


filebeat-playbook.yml
---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

  - name: install filebeat deb
    command: dpkg -i filebeat-7.6.1-amd64.deb

  - name: drop in filebeat deb
    copy:
      src: /etc/ansible/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebeat service
    command: service filebeat start

  - name: enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes




 

This document contains the following details:
- Description of the Topologu
  - designates the elk machine as the host and gives a user and lays out the tasks
- Access Policies- gives the elk server access to all the webservers 
- ELK Configuration
  - Beats in Use - filebeat and metricbeat
  - Machines Being Monitored- webservers 
- How to Use the Ansible Build- run the command ' ansible-playbook <playbook name>


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly secure, in addition to restricting traffic to the network.
- What aspect of security do load balancers protect? 
  They are defensive security so they mostly protect against DDoS attacks. 
- What is the advantage of a jump box?
  A jump box allows for easy access between two machines and can easily be secured.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the network and system preferences.
- What does Filebeat watch for?
  Filebeat monitors log files and log events.
- What does Metricbeat record?
  Metricbeat collects the metrics and statistics from a network.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name        | Function     | IP Address | Operating System |
|-------------|--------------|------------|------------------|
| Jump Box    | Gateway      | 10.0.0.1   |Linux             |
| Web_1       |DVWA Container|10.0.0.6    |Linux             |
| Web_2       |DVWA Conatiner|10.0.0.7    |Linux             |
| Web_3       |DVWA Container|10.0.0.8    |Linux             |
| Elk_Stack_VM|filebeat      | 10.1.0.4   |Linux             |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the host machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 10.0.0.6
- 10.0.0.7
- 10.0.0.8
- 10.1.0.4 
- 71.230.49.183

Machines within the network can only be accessed by the Jump Box vm .
- Which machine did you allow to access your ELK VM? What was its IP address?
  Jump Box Vm ansible container 
  IP: 65.52.118.28


A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | No                  | 10.0.0.6 10.0.0.7 10.0.0.8 10.1.0.4  |
| Web-1    | No                  | none                 |
| Web-2    | No                  | none                 |
| Web-3    | No                  | none                 |
| Elk_VM   | No                  | none                 |
### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
-  What is the main advantage of automating configuration with Ansible?
   Ansible lets you configure highly complicated workflows. It allows you to orchestrate and control an entire application into where it flows and where it will deploy.
The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- Download Filebeat deb
- install filebeat deb
- drop in filebeat deb- copy filebeat into webservers 
- enable and configure system module
- setup filebeat
- start filebeat service
- enable filebeat service on boot
  
- ...

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.
https://drive.google.com/file/d/1tocOAkWUkbibOl7iEvcbDynniPZ4cJg8/view?usp=sharing


### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- 10.0.0.6
- 10.0.0.7
- 10.0.0.8
- 10.1.0.4

We have installed the following Beats on these machines:
- Filebeat and Metricbeat

These Beats allow us to collect the following information from each machine:
-  In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc.
Filebeat collects log events and forwards them to elasticsearch, you would expect to see any important log events such as: server logs, audit logs, etc
Metricbeat collects metrics from a system and services running on the system. You would expect to see statistics running from systems such as Apache, MySQL, Nginx, etc.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the filebeat.connfig file to the /etc/ansible folder in your ansible container.
- Update the filebeat.config file to include the host IP as 10.1.0.4 on line 1106 & change the IP address to 10.1.0.4 on line 1806
- Run the playbook, and navigate to ELK-stack vm to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it? we copied the filebeatconfig.yml file into the /etc/filebeat/filebeat.yml directories within the webservers
   
- _Which file do you update to make Ansible run the playbook on a specific machine?
We needed to add the elk machine into the anisbleconfig file in order for it to run the playbook.
-  How do I specify which machine to install the ELK server on versus which to install Filebeat on?
Using the hosts option within your playbook file will specify where you will be running and downloading filebeat into. For example the hosts in our filebeat-playbook.yml would be webservers since filebeat needs to be installed onto those machines. 
- _Which URL do you navigate to in order to check that the ELK server is running?
you go to <elkvmIP>:5601/app/kibana to see if you can access your filebeat logs. 
