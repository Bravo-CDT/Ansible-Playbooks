# SMB AND SAMBA SERVER DEPLOYMENT

## Overview
 This playbook installs and configures SMB, a file sharing program that allows you to set up a Samba server on Linux and quickly transfer files through a shared folder between operating systems on the same network. 

This playbook will:
- Install the Samba package
- Install smbclient and pexpect as dependencies
- Configure a new shared folder on the target machine.
- Create an account through the user on the target machine to access the folder.
- Create an example text file to move into the folder.

 ## User Instructions
 The machine being accessed must be a Linux box with Ansible already installed and configured to be used from the deployment machine. You should also already have user access to the target machine and a user folder available.

 The variables "username" and "password" at the start should be replaced with the username and desired SMB password of the user that you are running on the target client.
 The test file created at the end can also be edited to whatever name and content you'd like.

 To access this testfile once it is put in the shared folder, simply run this command from a computer on its network that has Samba configured on it. This will start up Samba and let you remotely access the folder.
 
 smbclient //[target_ip]/sambashare -U [username]
 