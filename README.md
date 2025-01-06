# Network
Network configuration.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_network/blob/main/meta/main.yml)

[collections/roles](https://github.com/r-pufky/ansible_network/blob/main/meta/requirements.yml)

## Role Variables
[defaults](https://github.com/r-pufky/ansible_network/blob/main/defaults/main)

## Dependencies
Part of the [r_pufky.srv](https://github.com/r-pufky/ansible_collection_srv)
collection.

## Example Playbook
Apply network configuration to hosts.

group_vars/all/vars/all.yml
``` yaml
# enable for all hosts
network_ipv6_enable: true
network_tcp_bbr_enable: true
```

host_vars/example.com/network.yml
``` yaml
network_resolv: |
  search example.com
  nameserver 192.168.1.1
  # custom /etc/resolv.conf for host
network_resolv_immutable: true
network_interfaces: 'source /etc/network/interfaces.d/*'
network_local:
  - service: 'lo'
    state: 'present'
    config: |
      auto lo
      iface lo inet loopback
      # The loopback network interface
  - service: 'eth0'
    state: 'present'
    config: |
      auto eth0
      iface eth0 inet dhcp
  - service: 'eth10'
    state: 'absent'
```

Apply the base role
``` yaml
- name: 'Apply base configuration'
  ansible.builtin.include_role:
    name: 'r_pufky.srv.network'
```

## Development
Configure [environment](https://github.com/r-pufky/ansible_collection_srv/blob/main/docs/dev/environment/README.md)

Run all unit tests:
``` bash
molecule test --all
```

### Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_network/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
