# Apache Deployment 

## Service 

This Ansible deployment configures and manages an Apache HTTP Server, an open-source web server software managed by the Apache Software Foundation, on Linux target machines. It continuously runs on a machine, waits for HTTP requests from clients, locates the resources requested on the server, and returns the requested resources to the client for display in a web browser.  

## Features

- Package Management: installs the Apache service on target machine(s).
- Service Enablement: detects if Apache is disabled at boot and conditionally enables it.
- Service Management: detects if Apache is stopped and conditionally starts it.
- Template Deployment: deploys a custom Gray Team-themed web page with variables defined in apache.yml. 
- Log Collection: retrieves access and error logs from the target machine(s) and stores copies on the Ansible controller in dedicated folders. 

## Redeployment Instructions 

Users will pull the apache-deployment folder from the Ansible-Playbooks repository on GitHub. Provided in the folder are the ansible.cfg, apache.yml, inventory.ini configuration files and the logs and webpage folders to store retrieved Apache logs and the custom webpage. 

Before deployment, ensure that the controller has Ansible installed and configured and that the target Linux machine(s) have SSH enabled and configured with a user account capable of using sudo. 

Next, users should modify the inventory.ini file with the IP address(es) of the target machine(s) and the appropriate SSH user(s). The variables in the apache.yml file can be changed to suit the team name, competition name, and a desired message for the webpage. 

Once the above steps are completed, the playbook can be run, using ansible-playbook apache.yml, to install Apache, ensure the service is running and enabled, deploy a custom webpage, and retrieve access and error logs. 
