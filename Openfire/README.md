#Openfire Installation
This playbook will install Openfire on Windows

## Services and Features
Service: Openfire, a open-source chat server based on Java
Features: 
- Opens TCP port `5222` for clients to connect
- Sets the maximum memory to 1 GB to prevent overusage of RAM
- Checks that port `9090` is available to confirm interface has initiated

## Requirements
Install the two collections on the Ansible control machine 
ansible-galaxy collection install ansible.windows community.windows