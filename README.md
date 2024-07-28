# Ansible Role: Keepalived Setup

This Ansible role installs and configures Keepalived.

## Installation

```bash
ansible-galaxy role install Panzer1119.keepalived-setup
```

## Variables

- `install_keepalived`: Whether to install Keepalived (default: `true`)
- `configure_keepalived`: Whether to configure Keepalived (default: `true`)

- `keepalived_instance_name`: Keepalived instance name
- `keepalived_interface`: Network interface for Keepalived (default: `"eth0"`)
- `keepalived_virtual_router_id`: Virtual router ID for Keepalived (default: `1`)
- `keepalived_advert_interval`: Advert interval for Keepalived (default: `1`)
- `keepalived_authentication_password`: Authentication password for Keepalived (default: `"password"`)
- `keepalived_virtual_ipaddresses`: List of virtual IP addresses for Keepalived (default: `[]`)

- `keepalived_track_process`: Whether to track a process for Keepalived (default: `false`)
- `keepalived_track_process_name`: Process name to track for Keepalived

## Example Playbook

```yaml
- hosts: all
  become: yes
  vars:
    install_keepalived: true
    configure_keepalived: true
    use_zfs_storage_driver: true
    use_zfs_volume_plugin: true
  roles:
    - ansible-role-keepalived-setup
```

Test it with the following command:

```bash
ansible-playbook -i inventory playbook.yml -CD
```
