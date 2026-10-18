# Bubblechat Openfire playbook

This playbook installs Openfire 5.1.2 on the fresh Windows Server 2019 host named `bubblechat` and completes Openfire's first-run setup. It uses the embedded database and creates the eight accounts listed in the competition packet. All eight accounts receive the packet's shared initial password, `Patty321!`.

| Admin accounts | Standard accounts |
| --- | --- |
| `kingneptune`, `paintythepirate`, `Mrkrabs`, `Spongebob`, `Squidward` | `patrick`, `sandy`, `Pearl` |

The playbook enables XMPP clients on TCP 5222 and HTTP chat on TCP 7070 (BOSH at `/http-bind/` and WebSocket at `/ws/`). The HTTP admin console is reachable from the network at `http://bubblechat.krusty.krab:9090` or `http://10.111.2.4:9090`. Windows Firewall allows inbound 5222, 7070, and 9090. Self-registration and anonymous XMPP authentication are disabled. The built-in `admin` account gets a generated password and is not in the administrator list.

## Before running

Install the Windows collections on the Ansible controller:

```sh
ansible-galaxy collection install ansible.windows community.windows
```

The inventory must contain a Windows host named `bubblechat` with working Ansible/WinRM access and administrator privileges. The XMPP domain is set to `krusty.krab`, matching the AD DNS domain. The Openfire server hostname is `bubblechat.krusty.krab`. XMPP users will have addresses such as `Spongebob@krusty.krab`. This naming does not make Openfire use AD authentication; the playbook creates local Openfire accounts.

```sh
ansible-playbook -i inventory Openfire/openfire-install.yml
```

In AD DNS, ensure `bubblechat.krusty.krab` has an A record pointing to `10.111.2.4`. For XMPP clients that discover the server from `krusty.krab`, add an SRV record for `_xmpp-client._tcp.krusty.krab` targeting `bubblechat.krusty.krab` on port `5222`. Keep the existing `krusty.krab` records used by AD. HTTP clients should use `http://bubblechat.krusty.krab:7070/http-bind/` or `ws://bubblechat.krusty.krab:7070/ws/` with XMPP domain `krusty.krab`.

The playbook stops the service before writing its first-run configuration, because the Windows installer starts Openfire automatically. On later runs it preserves Openfire's initialized configuration and database. If scoring requires a different XMPP domain, override `openfire_xmpp_domain` **before the first deployment**. The initial account password can be overridden with `openfire_account_password` if the competition changes it; supply an override through Ansible Vault rather than a shell argument. A changed domain or account password after first-run setup needs an explicit migration or password update; rerunning the playbook will not silently reset accounts.

## Check after deployment

The playbook verifies ports 5222, 7070, and 9090 locally, checks the admin login page using Bubblechat's network address, and establishes a BOSH session for the supplied XMPP domain. It does not perform an authenticated login or send a chat message. After deployment, sign in as one admin and one standard account, send a message through the scoring client, and confirm Grey Team can use its two admin accounts.

The shared initial password and HTTP transport are competition requirements. Remote admin sign-ins will send credentials over plain HTTP; use this only on the isolated competition network and change credentials when the event permits.
