Ansible playbooks for homelab-use

!! All playbook have been generalized, so updating the defaults/main.yml will be required for proper function</h1>

Playbooks:
- communication_test.yml
  - Exactly what it says on the tin.  Tests that ansible can communicate with a server
- configure_aap.yml
   - This playbook runs against localhost and configures an Ansible Automation Platform (AAP) server.
   - Fully idempotent.
- configure_base.yml  
   - This playbook should be run against any server, and it will pull it into compliance/a known good state.  
   - Fully idempotent.
- configure_proxmox.yml
   - This playbook should be run against a proxmox server (was tested against 8.1).  
   - Fully idempotent.
- patch_os.yml
  - This playbook will patch a Debian/RHEL system and reboot as needed
  - Fully idempotent
  
Roles:
- configure_aap
  - This role will configure pretty much all needed settings for an AAP server, but I had to do a LOT of damage to it to generalize it so a full review before it runs will be necessary.  It ::should:: work, but I cant really test it...
  - Requires a hashi-vault server
  - If all "ansible.controller" tasks are replaced with "awx.awx" (same syntax) tasks, it should work against an AWX (upstream AAP, IE Free) server without incident
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
- patch_os
  - This role will patch a server to the latest OS and reboot as needed.  On rPI will cleanup old firmware files
