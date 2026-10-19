# Sql Server Setup

This playbook creates a postgresql database, adds the postgresql apt repository to ensure the most up to date version, and creates a service account for it to ensure it is working correctly.

There should be no steps outside of running the ansible to create the mgmt database, other than creating a .env file with the following fields:

```sh
#!/bin/bash
DB_USER=bravo
DB_PASSWORD=change_me_to_a_strong_password
DB_NAME=bravo
```

And then running:
```
source .env
```
