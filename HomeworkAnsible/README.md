This branch contains an Ansible script that sets up an SMTP server (specifically, Postfix) on a target machine. It also installs S-Nail as a CLI email client the machine can use to read any emails sent its way (for example, the test email).

Ensure that the hostname of the target machine is in the inventory you choose (as it's used as a variable to send the test email). 
