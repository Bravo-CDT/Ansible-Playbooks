# Windows Certificate Authority

The ansible in this folder deploys certificate authority on windows server and signes a CA root certificate which later copied to other machines in the domain.

## Instructions to depl
To deploy this setup, first ensure that all target Windows hosts have WinRM enabled for remote management, while target Linux hosts have SSH enabled with user accounts configured for sudo privileges. On the Ansible controller, verify that Ansible is installed along with the required Windows modules collection by running ansible-galaxy collection install ansible.windows. Next, update your Ansible inventory file so that target systems match the specific host groups defined in the script (windows_servers, windows_clients, and linux), or update the playbook's host target lines to match your existing inventory setup. You can also adjust configuration variables located in the playbook, such as modifying the CACommonName variable to change the Certificate Authority's name; all possible changes are noted with inline comments in the script. Finally, execute the playbook from your terminal by running ansible-playbook -i <your_inventory_file> deploy-ca.yml

