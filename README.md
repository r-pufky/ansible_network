# Network
Advanced network configuration management.

## Requirements
[supported platforms](https://github.com/r-pufky/ansible_network/blob/main/meta/main.yml)

## Role Variables
[defaults](https://github.com/r-pufky/ansible_network/blob/main/defaults/main)

## Dependencies
**galaxy-ng** roles cannot be used independently. Part of
[r_pufky.deb](https://github.com/r-pufky/ansible_collection_deb) collection.

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
network_interfaces_source_enable: true
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
    name: 'r_pufky.deb.network'
```

## FRRouting is supported as well.
FRR can be configured with interfaces simultaneously.

``` yaml
- name: 'Add openfabric routing support'
  ansible.builtin.include_role:
    name: 'r_pufky.deb.network'
  vars:
    network_frr_enable: true
    network_frr_interfaces_enable: true
    network_frr_daemon_fabricd_enable: true
    network_frr_config: |
      frr defaults traditional
      hostname test_frr_host
      log syslog warning
      ip forwarding
      no ipv6 forwarding
      service integrated-vtysh-config
      !
      interface lo
       ip address 10.11.11.11/32
       ip router openfabric 1
       openfabric passive
      interface eth0
       ip router openfabric 1
       openfabric csnp-interval 2
       openfabric hello-interval 1
       openfabric hello-multiplier 2
      !
      line vty
      !
      router openfabric 1
       net 49.0011.0010.1111.0011.00
       lsp-gen-interval 1
       max-lsp-lifetime 600
       lsp-refresh-interval 180
```

## Development
Configure [environment](https://github.com/r-pufky/ansible_collection_docs/blob/main/dev/environment/README.md)

Run all unit tests:
``` bash
molecule test --all
```

### Releases
Major release versions track Debian release versions:

* **[13.x.x](https://github.com/r-pufky/ansible_network)**: 13 Trixie.
* **[12.x.x](https://github.com/r-pufky/ansible_network/tree/12.x)**: 12 Bookworm.

### Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_network/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)
