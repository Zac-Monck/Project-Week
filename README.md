# Project-Week
project week repository

The files in this repository were used to configure the network depicted below.

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

Pentest.yml  
---
- name: Config Web VM with Docker
  hosts: webservers
  become: true
  tasks:
  - name: unistall apache2
    apt:
       name: apache2
       state: absent
  - name: docker.io
    apt:
      update_cache: yes
      name: docker.io
      state: present

  - name: Install pip3
    apt:
      name: python3-pip
      state: present

  - name: Install Docker python module
    pip:
      name: docker
      state: present

  - name: download and launch a docker web container
    docker_container:
      name: dvwa
      image: cyberxsecurity/dvwa
      state: started
      published_ports: 80:80

  - name: Enable docker service
    systemd:
      name: docker
      enabled: yes

elk-playbook.yml  

# Project-Week
project week repository
 name: Config ELK VM with Docker
  hosts: 10.2.0.5
  remote_user: azdmin
  become: true
  tasks:
    - name: Install docker.io
      apt:     
        name: docker.io
        state: present
        update_cache: yes
    - name: Install pip3
      apt:
        name: python3-pip
        state: present
        force_apt_get: yes
    - name: Install docker via pip
      pip:
        name: docker
        state: present
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: '262144'
        state: present
        reload: yes
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044

filebeat-playbook.yml

---
- name: Installing and Launch Filebeat
  hosts: webservers
  become: yes
  tasks:
    # Use command module
  - name: Download filebeat .deb file
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb

    # Use command module
  - name: Install filebeat .deb
    command: sudo dpkg -i filebeat-7.6.1-amd64.deb

    # Use copy module
  - name: Drop in filebeat.yml
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

    # Use command module
  - name: Enable and Configure System Module
    command: filebeat modules enable system

    # Use command module
  - name: Setup filebeat
    command: filebeat setup

    # Use command module
  - name: Start filebeat service
    command: service filebeat start

    # Use systemd module
  - name: Enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes

This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, (the Damn Vulnerable Web Application).

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
-the load balancers will protect the networks availability
-the jumpbox will reduce the chance of an attack because all conections will come via this VM

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the configuration and system file.
- _TODO: What does Filebeat watch for? - filebeat will watch for log files
- _TODO: What does Metricbeat record?- metricbeat will record the operating system

The configuration details of each machine may be found below.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box |Gateway   | 10.0.0.4   | Linux            |
| web1     |dvwa      | 10.1.0.5   | Linux            |
| web2     |dvwa      | 10.1.0.6   | Linux            |
| elk      |elk       | 10.2.0.5   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
: 20.37.253.162

Machines within the network can only be accessed by ssh.
- _TODO: Which machine did you allow to access your ELK VM? What was its IP address? the jump box, its public IP is 20.37.253.162 orits private is10.0.0.4

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 10.0.0.4             |
| Jump Box | Yes                 | 20.37.253.162        |


### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _TODO: What is the main advantage of automating configuration with Ansible? it is quick to execute

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc.
pentest.yml
- the docker is installed
- python is installed next
- docker python 3rd
- it then downloads and executes the DVWA container
- lastly it enables the docker services

elk.yml
- the docker is installed
- python is installed next
- docker python 3rd
- it then increases the virtual memory for elk
- it downloads and executes the elk docker container

filebeat_playbook.yml
-filebeat downloaded and installed
-it then enables the module
-Configures and executes filebeat

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_
-web1 10.1.0.5
-web2 10.1.0.6

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_
-filebeat & metricbeat

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._
filebeat collects the logs from the vms
metricbeat collects the metrics from the OS for the vm's

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook file to the docker container.
- Update the ansible hosts file to include the ip addresses
- Run the playbook, and navigate to ansible.cfg to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it? the playbook is a ".yml" file
- _Which file do you update to make Ansible run the playbook on a specific machine? it gets updated to the "hosts" file
- _Which URL do you navigate to in order to check that the ELK server is running? kibana
