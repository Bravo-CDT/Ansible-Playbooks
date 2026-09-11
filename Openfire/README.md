#Openfire Installation
This playbook will install Openfire on Windows

## Services and Features
Service: Openfire, a open-source chat server based on Java
Features: 
- Opens TCP port `5222`
- Sets the maximum memory to 1 GB
- Checks that port `9090` is available

## Requirements
Install the two collections on the Ansible control machine 
ansible-galaxy collection install ansible.windows community.windows