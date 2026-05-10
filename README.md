# Ansible Role: Keepalived Setup

This Ansible role installs and configures Keepalived.

## Installation

```bash
ansible-galaxy role install Panzer1119.keepalived-setup
```

## Variables

- `install_keepalived`: Whether to install Keepalived (default: `true`)
- `configure_keepalived`: Whether to configure Keepalived (default: `true`)

- `keepalived_instance_name`: Global instance name prefix (default: `"VRRP"`). Used to auto-generate instance names if not specified (e.g., `"VRRP_eth0"`).

- `keepalived_instances`: **Required.** List of VRRP instances. Each instance supports:
  - `name`: Instance name (optional, defaults to `keepalived_instance_name_interface`)
  - `interface`: Network interface (required)
  - `virtual_ipaddress`: List of virtual IP addresses (required, at least one)
  - `virtual_router_id`: Virtual router ID (default: `1`)
  - `priority`: Priority (uses computed MASTER/BACKUP priority)
  - `state`: VRRP state, `MASTER` or `BACKUP` (computed from inventory groups)
  - `advert_interval`: Advertisement interval (default: `1`)
  - `authentication_password`: Auth password (default: `"password"`)
  - `unicast_peers`: List of peer IPs (computed from master/backup groups)

- `keepalived_unicast_src_ips`: Dictionary mapping instance names to unicast source IPs per host (optional). If not specified, defaults to `ansible_host` for all instances.
  - Example: `{ "AdGuardHome_eth0": "192.168.10.5", "AdGuardHome_eth1": "10.20.30.5" }`
  - Unicast peers are auto-computed from the `master` and `backup` inventory groups

- `keepalived_advert_interval`: Default advertisement interval for all instances (default: `1`)
- `keepalived_authentication_password`: Default authentication password for all instances (default: `"password"`)
- `keepalived_track_process`: Whether to track a process for Keepalived (default: `false`)
- `keepalived_track_process_name`: Process name to track for Keepalived

## Example Playbook

```yaml
- hosts: all
  become: yes
  vars:
    install_keepalived: true
    configure_keepalived: true
    keepalived_instance_name: "AdGuardHome"
    keepalived_instances:
      - interface: "eth0"
        virtual_router_id: 51
        virtual_ipaddress:
          - "192.168.10.10/24"
          - "192.168.10.11/24"
      - interface: "eth1"
        virtual_router_id: 52
        virtual_ipaddress:
          - "10.20.30.10/24"
    keepalived_advert_interval: "1"
    keepalived_authentication_password: "password"
    keepalived_track_process: true
    keepalived_track_process_name: "AdGuardHome"
  roles:
    - ansible-role-keepalived-setup
```

Note: The instances above will be named `AdGuardHome_eth0` and `AdGuardHome_eth1` respectively.

### Specifying unicast IPs per instance per host

In your inventory file (e.g., `host_vars/node1.yml`), define a dict mapping instance names to their unicast source IPs:

```yaml
# inventory/host_vars/node1.yml
keepalived_unicast_src_ips:
  "AdGuardHome_eth0": "192.168.10.5"
  "AdGuardHome_eth1": "10.20.30.5"
```

If not specified, the role defaults to using `ansible_host` for all instances.

### Unicast peers auto-computation

The role automatically computes `unicast_peers` for each instance from the `master` and `backup` inventory groups. The current host is automatically excluded from the peer list.

Test it with the following command:

```bash
ansible-playbook -i inventory playbook.yml -CD
```
