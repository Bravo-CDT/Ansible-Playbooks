# Bubblechat Openfire playbook

This playbook installs Openfire 5.1.2 on a fresh Windows Server 2019 host named `bubblechat`. Openfire authenticates users against Active Directory over LDAPS at `treedome.krusty.krab:636` (`10.111.2.1`). The search base is `DC=krusty,DC=krab`, and usernames use AD's `sAMAccountName` attribute. The embedded database stores Openfire state; it is not used to authenticate users.

The playbook does not create AD accounts or local Openfire accounts. The five administrator usernames (`kingneptune`, `paintythepirate`, `Mrkrabs`, `Spongebob`, and `Squidward`) must exist under the AD search base; the preflight verifies them. All eligible AD users under the search base can sign in for chat without console access. The original local-account playbook listed `patrick`, `sandy`, and `Pearl` as standard chat users. `Pearl` does not exist in the current AD, and an existing AD user can be used instead. There is no local recovery administrator. If AD is unavailable, these users cannot authenticate to Openfire.

Openfire's [embedded database schema](https://raw.githubusercontent.com/igniterealtime/Openfire/v5.1.2/distribution/src/database/openfire_hsqldb.sql) seeds a built-in `admin` record. LDAP-only user and authentication providers make that record inaccessible. Do not enable local or hybrid authentication later without first rotating or removing its default password.

The playbook enables XMPP clients on TCP 5222 and HTTP chat on TCP 7070 (BOSH at `/http-bind/` and WebSocket at `/ws/`). The HTTP admin console is reachable from the network at `http://bubblechat.krusty.krab:9090` or `http://10.111.2.4:9090`. Windows Firewall allows inbound 5222, 7070, and 9090. Self-registration and anonymous XMPP authentication are disabled.

## Before running

On the `scoring` Ansible controller, install the Windows collections and the WinRM Python library in Ansible's pipx environment:

```sh
ansible-galaxy collection install ansible.windows community.windows
pipx inject ansible-core pywinrm
```

The playbook supplies Bubblechat's address (`10.111.2.4`), WinRM HTTP port (`5985`), NTLM transport with message encryption, and the `KRUSTY\mrkrabs` connection account. That account must have administrator access on Bubblechat. It reuses the static LDAP bind password in the playbook, so no credential prompt or separate inventory file is needed. This WinRM connection is separate from Openfire's LDAPS connection to the domain controller on port 636. [Ansible notes](https://docs.ansible.com/projects/ansible/latest/os_guide/windows_winrm.html) that NTLM protection over HTTP is weaker than verified TLS; the HTTP choice is for this lab.

From a checkout containing the `Openfire` folder, verify the connection and syntax without running the install tasks. The comma in `bubblechat,` makes it an inline inventory host:

```sh
ansible-playbook -i 'bubblechat,' Openfire/openfire-install.yml --tags connection_check
ansible-playbook -i 'bubblechat,' Openfire/openfire-install.yml --syntax-check
```

The connection check must return `pong`, and the syntax check must pass. The connection check only contacts Bubblechat; it does not install Openfire. From Bubblechat, `treedome.krusty.krab` must resolve to the DC and TCP 636 must be reachable. Its Windows certificate store must trust the DC's LDAPS certificate chain for the playbook's bind preflight. The DC certificate must identify `treedome.krusty.krab`. The preflight confirms that the bind account works and that the five admin usernames exist below `DC=krusty,DC=krab`.

The playbook uses the selected Blue Team Domain Admin account `mrkrabs` (`CN=mrkrabs,CN=Users,DC=KRUSTY,DC=KRAB`) for its LDAP bind. Openfire needs only directory search/read access; Domain Admin privileges are not required for this function. The password is stored in the tracked playbook and deployed Openfire configuration, so access to either exposes this privileged AD credential. No LDAP credential prompt or extra variables are needed. The playbook stops before installing Openfire if the bind or directory search fails. When ready to deploy, run:

```sh
ansible-playbook -i 'bubblechat,' Openfire/openfire-install.yml
```

The playbook does not create or change `mrkrabs` in AD. If that account's password changes, update the `openfire_ldap_bind_password` variable in the playbook and the deployed Openfire configuration; a normal rerun does not replace Openfire's completed first-run configuration.

Openfire's XMPP domain is `krusty.krab`, so users have addresses such as `Spongebob@krusty.krab`. The server hostname is `bubblechat.krusty.krab`. In AD DNS, ensure `bubblechat.krusty.krab` has an A record pointing to `10.111.2.4`. For XMPP clients that discover the server from `krusty.krab`, add an SRV record for `_xmpp-client._tcp.krusty.krab` targeting `bubblechat.krusty.krab` on port `5222`. Keep the existing `krusty.krab` records used by AD. HTTP clients should use `http://bubblechat.krusty.krab:7070/http-bind/` or `ws://bubblechat.krusty.krab:7070/ws/` with XMPP domain `krusty.krab`.

The playbook stops the service before writing its first-run configuration because the Windows installer starts Openfire automatically. On later runs it preserves Openfire's initialized configuration and database. Changes to the domain, LDAP settings, bind credentials, or administrator list after first-run setup require an explicit update of the deployed Openfire configuration; rerunning this playbook will not apply a new template.

## Check after deployment

The playbook checks that the DC's LDAPS port is reachable, binds to AD, finds the five administrator accounts, verifies ports 5222, 7070, and 9090 locally, checks the admin login page through Bubblechat's network address, and establishes a BOSH session for the configured XMPP domain. These checks do not authenticate through Openfire. After deployment, sign in with one named AD admin and one standard AD user, send a message through the scoring client, and verify that an AD account outside the admin list cannot access the console. Check Openfire's LDAP logs if any login fails.

Openfire 5.1.2's [LDAP socket factory](https://raw.githubusercontent.com/igniterealtime/Openfire/v5.1.2/xmppserver/src/main/java/org/jivesoftware/util/SimpleSSLSocketFactory.java) encrypts the LDAPS connection but does not validate the DC certificate's trust chain. Importing the CA certificate into Java's truststore does not fix that behavior. If the deployment requires authenticated server identity for LDAP, use a vetted Openfire fix or a TLS proxy that validates the DC certificate before routing credentials. The remote admin console and HTTP chat endpoints above also use plain HTTP; restrict access to the intended competition network.
