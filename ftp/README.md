# Very Secure FTP Daemon

The service I chose to deploy is vsftpd, an ftp server used to transfer files between computers over a network. It allows users to connect to the server using the ftp protocol to upload, download, and manage files. vsftpd is designed to provide a secure and reliable ftp service on Linux systems.

# Requirements 

The target Ubuntu system must have ufw installed before deploying the playbook. Ufw is required because the playbook uses it to manage the firewall rule for tcp port 21, which is the standard port used by ftp. The target system must also have ssh access enabled and provide the necessary sudo privileges for Ansible to install and configure vsftpd and modify the firewall.

# Deploy

To deploy the playbook, first install Ansible on the control machine using sudo apt update and sudo apt install ansible -y. Place the ftp.yml playbook and an inventory.ini file in the same directory. The inventory should contain the IP address and ssh username of the Ubuntu target system. After configuring the inventory, test the connection using ansible -i inventory.ini all ping. Then deploy the service using ansible-playbook -i inventory.ini ftp.yml.
