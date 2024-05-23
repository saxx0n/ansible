Ansible playbooks for homelab-use

!! All playbook have been generalized, so updating the defaults/main.yml will be required for proper function</h1>

Playbooks:
- configure_base.yml  
   - This playbook should be run against any server, and it will pull it into compliance/a known good state.  
   - Fully idempotent.
- configure_proxmox.yml
   - This playbook should be run against a proxmox server (was tested against 8.1).  
   - Fully idempotent.
  
Roles:
- configure_base
  - This role provides a base level of configuration.
- configure_firewall
  - This role will setup a firewall and enable SSH
- configure_proxmox
  - This role will setup a proxmox server, and prep it for terraform/cluster use
  - Requires a hashi-vault server
- configure_users
  - This role will read user data from a hashi-vault database and configure the target system with it.  Along with this it removes non-wanted accounts and handles root password/key
  - Requires a hashi-vault server
