# SMB AND SAMBA SERVER DEPLOYMENT

## Overview
 This playbook installs and configures SMB, a file sharing program that allows you to set up a Samba server on Linux and quickly transfer files through a shared folder between operating systems on the same network. 

This playbook will:
- Install the Samba package
- Install smbclient and pexpect as dependencies

 ## User Instructions
 The machine being accessed must be a Linux box with Ansible already installed and configured to be used from the deployment machine.
 The variables "username" and "password" at the start should be replaced with the username and desired SMB password of the user that you are running on the target client.
 