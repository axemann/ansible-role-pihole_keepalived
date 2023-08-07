Ansible Role: pihole-keepalived
=================

High availability (HA) pihole, using keepalived for failover.

When providing two (or more) DNS servers to the computers on your network, each
computer choses its primary DNS server at random (not in priority order, as one
would suspect).  This causes several problems:

- Your pihole stats will be spread across all of your (multiple) pihole
servers.
- If one pihole DNS server falls offline, it may take some time before your
computer switches to another DNS server, resulting in slow web browsing, etc.

Keepalived will allow one to pass a single virtual IP (VIP) as the DNS server to
your various computers/devices. This IP will always point to your **primary**
pihole server (unless it has failed), resulting in all pihole stats remaing on
the **primary** server. If the primary pihole server fails, the VIP will
automatically switch over to a secondary pihole server, with no loss of DNS
availability (of course, your pihole stats will be shown on the secondary for
the time the primary is _down_).

Requirements
------------

1. At least two pihole servers must be running.

    NOTE: You may install these manually using the instructions at [Pi-hole.net](https://pi-hole.net/),
    via an ansible-galaxy role (such as [r_pufky.pihole](https://galaxy.ansible.com/r_pufky/pihole)),
    or via your own locally-developed ansible role.

2. Static IP assignments must be configured during initial pihole installation for each pihole server.

Role Variables
--------------

```
##### defaults for pihole-keepalived (set via defaults/main.yml):
pihole_keepalive_auth_pass: ChangeMe                 # Set to your desired password (max 8 characters). Must be identical for all pihole servers.
pihole_keepalive_virtual_ipaddress: 10.10.10.50/24   # Set to your desired VIP. Must be identical for all pihole servers.
```

### To override, you may set the following variables via group_vars/host_vars, playbook, CLI, etc:

```
##### example group_vars/pihole_servers:
pihole_keepalive_auth_pass: ChangeMe                 # Set to your desired password (max 8 characters). Must be identical for all pihole servers.
pihole_keepalive_virtual_ipaddress: 10.10.10.50/24   # Set to your desired VIP. Must be identical for all pihole servers.

##### example host_vars/pihole1:
pihole_keepalive_state: MASTER                       # Set each pihole's initial state to either MASTER or BACKUP.
pihole_keepalive_priority: 255                       # Must be unique for each pihole server (MASTER should be greater than all BACKUPs).

##### example host_vars/pihole2:
pihole_keepalive_state: BACKUP                       # Set each pihole's initial state to either MASTER or BACKUP.
pihole_keepalive_priority: 250                       # Must be unique for each pihole server (MASTER should be greater than all BACKUPs).

##### example host_vars/pihole3:
pihole_keepalive_state: BACKUP                       # Set each pihole's initial state to either MASTER or BACKUP.
pihole_keepalive_priority: 245                       # Must be unique for each pihole server (MASTER should be greater than all BACKUPs).
```

Dependencies
------------

None

Example Playbook
----------------

```
---
- name: pihole_servers.yml
  hosts: pihole_servers
  become: true
  become_user: root
  serial: 1

  roles:
    - tomhoover.pihole-keepalived
```

License
-------

MIT

Author Information
------------------

[Tom Hoover](https://github.com/tomhoover)

<!--- vim: set nofoldenable: -->
