# Krusty Krab Ansible

One project for both the Linux and Windows environments. Run everything from the repo root.

```
ansible.cfg       inventory and roles_path
inventory.ini     [linux:children] and [windows:children] groups
group_vars/       all.yml (shared domain, users, passwords), linux.yml, windows.yml
roles/            Linux and Windows roles
Theme-assets/     Windows wallpapers, index page, Chrome installer
site.yml          Windows, then Linux
windows.yml       Windows only
linux.yml         Linux only
HomeworkAnsible/  Homework 2 playbooks
```

## Deploy

```
ansible-galaxy collection install -r requirements.yml

ansible-playbook site.yml                 # everything
ansible-playbook windows.yml              # Windows only
ansible-playbook linux.yml                # Linux only
ansible-playbook windows.yml --tags dc    # one service (tags: dc, ca, iis, openfire, telnet, smb, smtp, sql, apache, nginx, theme)
ansible-playbook site.yml --limit thesafe # one host group
```

Linux needs the root CA from the Windows CA play, so run Windows (or at least the `ca` tag) first.
