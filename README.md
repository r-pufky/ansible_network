# Network
Advanced network configuration management.

## [Requirements][i]
Requires [r_pufky.deb][g] galaxy-ng collection.

## Role Variables
Detailed variable use documented in defaults. See usage for role operation.

* [defaults][j] - User configurable options.

* [FRR][k] - FRR options.

## Usage
Proxmox/LXC containers network configuration must be set in LXC container
configuration.

### Feature Flags
Tasks are gated by feature flags and executed in the following order.

  Step | Flag                  | Notes
 ------|-----------------------|-------
  1    | network_flg_container | Deploy container specific network settings.
  2    | network_flg_frr       | Install FRR.

### Example Playbooks

#### Apply network configuration to a group of hosts.

group_vars/all/vars/all.yml
``` yaml
# enable for all hosts
network_srv_ipv6: true
network_srv_tcp_bbr: true
```

host_vars/node1.example.com/network.yml
``` yaml
network_cfg_resolv: |
  search example.com
  nameserver 192.168.1.1
  # custom /etc/resolv.conf for host
network_cfg_resolv_immutable: true
network_cfg_interfaces_source_enable: true
network_cfg_local:
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

``` yaml
- name: 'Configure all nodes with IPv4 & TCP BBR; set interfaces for node1.'
  ansible.builtin.include_role:
    name: 'r_pufky.deb.network'
```

#### FRR can be configured with interfaces simultaneously.

``` yaml
- name: 'Add openfabric routing support'
  ansible.builtin.include_role:
    name: 'r_pufky.deb.network'
  vars:
    network_flg_frr: true
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
Configure [environment][a].

``` bash
# Run all tests.
molecule test --all
```

### [Releases][b]

  Release | Debian | Ansible | Notes
 ---------|--------|---------|-------
  3.x.x   | 13     | 2.20    | Ansible 2.20, semantic versioning.
  2.x.x   | 13     | 2.18    | Migrate to Debian Trixie.
  1.x.x   | 12     | 2.18    | Use standardized libraries.
  0.x.x   | 12     | 2.18    | Migration from private repository.

## Issues
Create a bug and provide as much information as possible.

Associate pull requests with a submitted bug.

## License
[AGPL-3.0 License](https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0)
 [(direct link)](https://github.com/r-pufky/ansible_network/blob/main/LICENSE)

## Author Information
PGP Fingerprint: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9](https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9)
| [github gist](https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22)

## License
[AGPL-3.0 License][c] | [direct link][f]

## Author Information
PGP: [466EEC2B67516C7117C85CE3A0BC35D16698BAB9][d] | [github gist][e]


[a]: https://r-pufky.github.io/ansible_docs
[b]: https://semver.org/spec/v2.0.0
[c]: https://www.tldrlegal.com/license/gnu-affero-general-public-license-v3-agpl-3-0
[d]: https://keys.openpgp.org/vks/v1/by-fingerprint/466EEC2B67516C7117C85CE3A0BC35D16698BAB9
[e]: https://gist.github.com/r-pufky/a8df36977c55b5bb20829267c4c49d22

[f]: https://github.com/r-pufky/ansible_network/blob/main/LICENSE
[g]: https://github.com/r-pufky/ansible_collection_deb
[i]: https://github.com/r-pufky/ansible_network/blob/main/meta/main.yml
[j]: https://github.com/r-pufky/ansible_network/tree/main/defaults/main/main.yml
[k]: https://github.com/r-pufky/ansible_network/blob/main/defaults/main/frr.yml