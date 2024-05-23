# ansible
Ansible playbooks for homelab-use

!! All playbook have been generalized, so updating the defaults/main.yml will be required for proper function

Playbooks:
  - configure_base.yml  --- This playbook should be run against any server, and it will pull it into compliance/a known good state.  Fully idempotent.

Roles:
  - configure_base       --- This role provides a base level of configuration.
  - configure_firewall   --- This role will setup a firewall and enable SSH
  - configure_users      --- This role will read user data from a hashi-vault database and configure the target system with it.  Along with this it removes non-wanted accounts and handles root password/key