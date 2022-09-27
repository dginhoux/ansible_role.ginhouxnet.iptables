# ROLE dginhoux.iptables



## DESCRIPTION

This ansible role configure iptables.<br />
It also save rules to ensure rules applied at machine startup.



## REQUIREMENTS

#### SUPPORTED PLATFORMS

This role require a supported platform.<br />
It will skip node with unsupported platform to avoid any compatibility problem.<br />
This behaviour can be bypassed by settings the following variable `asserts_bypass=True`.

| Platform | Versions |
|----------|----------|
| Debian | buster, bullseye |
| Fedora | 33, 34, 35, 36 |
| EL | 7, 8 |

#### ANSIBLE VERSION

Ansible >= 2.12

#### DEPENDENCIES

None.



## INSTALLATION

#### ANSIBLE GALAXY

```shell
ansible-galaxy install dginhoux.iptables
```
#### GIT

```shell
git clone https://github.com/dginhoux/ansible_role.iptables dginhoux.iptables
```


## USAGE

#### EXAMPLE PLAYBOOK

```yaml
- hosts: all
  roles:
    - name: start role dginhoux.iptables
      ansible.builtin.include_role:
        name: dginhoux.iptables
```


## VARIABLES

#### DEFAULT VARIABLES

Defaults variables defined in `defaults/main.yml` : 

```yaml
---
## https://docs.ansible.com/ansible/latest/collections/ansible/builtin/iptables_module.html

iptables_configure_action: generate
# iptables_configure_action: cleanup

iptables_install_before_configure: true
iptables_reset_before_configure: true
iptables_reset_customs_chains_before_configure: true
iptables_save_conf: true
iptables_create_global_loopback_traffic_rules: true
iptables_create_global_logged_dropped_traffic_rules: true
iptables_create_global_related_established_traffic_rules: true
iptables_set_chains_policies: true

iptables_save_conf_remove_pattern: true
iptables_save_conf_remove_pattern_list:
  - ":DOCKER-INGRESS"
  - ":DOCKER-ISOLATION-STAGE-1"
  - ":DOCKER-ISOLATION-STAGE-2"
  - ":DOCKER-USER"
  - ":DOCKER"
  - "-j DOCKER-INGRESS"
  - "-j DOCKER-ISOLATION-STAGE-1"
  - "-j DOCKER-ISOLATION-STAGE-2"
  - "-j DOCKER-USER"
  - "-j DOCKER"
  - "-A DOCKER-INGRESS"
  - "-A DOCKER-ISOLATION-STAGE-1"
  - "-A DOCKER-ISOLATION-STAGE-2"
  - "-A DOCKER-USER"
  - "-A DOCKER"
  - "docker_gwbridge"
  - "docker0"

iptables_logged_dropped_traffic_rules:
  - chain: INPUT
    table: filter
    state: present
    log_level: info
    log_prefix: "iptables_input_drop: "
    ip_version: ipv4
  - chain: INPUT
    table: filter
    state: present
    log_level: info
    log_prefix: "ip6tables_input_drop: "
    ip_version: ipv6
  - chain: OUTPUT
    table: filter
    state: present
    log_level: info
    log_prefix: "iptables_output_drop: "
    ip_version: ipv4
  - chain: OUTPUT
    table: filter
    state: present
    log_level: info
    log_prefix: "ip6tables_output_drop: "
    ip_version: ipv6
  - chain: FORWARD
    table: filter
    state: present
    log_level: info
    log_prefix: "iptables_forward_drop: "
    ip_version: ipv4
  - chain: FORWARD
    table: filter
    state: present
    log_level: info
    log_prefix: "ip6tables_forward_drop: "
    ip_version: ipv6

iptables_customs_chains:
  - chain: CUSTOM_CHAIN_IPV4
    table: filter
    state: present
    ip_version: ipv4
  - chain: CUSTOM_CHAIN_IPV6
    table: filter
    state: present
    ip_version: ipv6

iptables_rules_loopback_traffic:
  - chain: INPUT
    table: filter
    state: present
    jump: ACCEPT
    in_interface: lo
    ip_version: ipv4
  - chain: INPUT
    table: filter
    state: present
    jump: ACCEPT
    in_interface: lo
    ip_version: ipv6
  - chain: OUTPUT
    table: filter
    state: present
    jump: ACCEPT
    out_interface: lo
    ip_version: ipv4
  - chain: OUTPUT
    table: filter
    state: present
    jump: ACCEPT
    out_interface: lo
    ip_version: ipv6
  - chain: FORWARD
    table: filter
    state: present
    jump: ACCEPT
    ip_version: ipv4
  - chain: FORWARD
    table: filter
    state: present
    jump: ACCEPT
    ip_version: ipv6

iptables_rules_related_established_traffic:
  - chain: INPUT
    table: filter
    state: present
    jump: ACCEPT
    ip_version: ipv4
  - chain: INPUT
    table: filter
    state: present
    jump: ACCEPT
    ip_version: ipv6
  - chain: OUTPUT
    table: filter
    state: present
    jump: ACCEPT
    ip_version: ipv4
  - chain: OUTPUT
    table: filter
    state: present
    jump: ACCEPT
    ip_version: ipv6
  - chain: FORWARD
    table: filter
    state: present
    jump: ACCEPT
    ip_version: ipv4
  - chain: FORWARD
    table: filter
    state: present
    jump: ACCEPT
    ip_version: ipv6

iptables_rules_traffic:
  - chain: INPUT
    table: filter
    state: present
    comment: allow_icmp_ping
    ip_version: ipv4
    protocol: icmp
    icmp_type: 8
    jump: ACCEPT

  - chain: INPUT
    table: filter
    state: present
    comment: allow_ssh
    ip_version: ipv4
    protocol: tcp
    destination_port: 22
    ctstate: NEW
    jump: ACCEPT

iptables_chains_policy:
  # table filter
  - chain: INPUT
    table: filter
    state: present
    policy: DROP
    ip_version: ipv4
  - chain: INPUT
    table: filter
    state: present
    policy: DROP
    ip_version: ipv6
  - chain: OUTPUT
    table: filter
    state: present
    policy: DROP
    ip_version: ipv4
  - chain: OUTPUT
    table: filter
    state: present
    policy: DROP
    ip_version: ipv6
  - chain: FORWARD
    table: filter
    state: present
    policy: DROP
    ip_version: ipv4
  - chain: FORWARD
    table: filter
    state: present
    policy: DROP
    ip_version: ipv6
  # table nat
  # - chain: PREROUTING
  #   table: nat
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: PREROUTING
  #   table: nat
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6
  # - chain: INPUT
  #   table: nat
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: INPUT
  #   table: nat
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6
  # - chain: OUTPUT
  #   table: nat
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: OUTPUT
  #   table: nat
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6
  # - chain: POSTROUTING
  #   table: nat
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: POSTROUTING
  #   table: nat
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6
  # # table mangle
  # - chain: PREROUTING
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: PREROUTING
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6
  # - chain: INPUT
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: INPUT
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6
  # - chain: FORWARD
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: FORWARD
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6
  # - chain: OUTPUT
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: OUTPUT
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6
  # - chain: POSTROUTING
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv4
  # - chain: POSTROUTING
  #   table: mangle
  #   state: present
  #   policy: DROP
  #   ip_version: ipv6

iptables_chains_policy_default:
  # table filter
  - chain: INPUT
    table: filter
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: INPUT
    table: filter
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: OUTPUT
    table: filter
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: OUTPUT
    table: filter
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: FORWARD
    table: filter
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: FORWARD
    table: filter
    state: present
    policy: ACCEPT
    ip_version: ipv6
  # table nat
  - chain: PREROUTING
    table: nat
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: PREROUTING
    table: nat
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: INPUT
    table: nat
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: INPUT
    table: nat
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: OUTPUT
    table: nat
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: OUTPUT
    table: nat
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: POSTROUTING
    table: nat
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: POSTROUTING
    table: nat
    state: present
    policy: ACCEPT
    ip_version: ipv6
  # table mangle
  - chain: PREROUTING
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: PREROUTING
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: INPUT
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: INPUT
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: FORWARD
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: FORWARD
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: OUTPUT
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: OUTPUT
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv6
  - chain: POSTROUTING
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv4
  - chain: POSTROUTING
    table: mangle
    state: present
    policy: ACCEPT
    ip_version: ipv6
```

#### DEFAULT OS SPECIFIC VARIABLES

Those variables files are located in `vars/*.yml` are used to handle OS differences.<br />
One of theses is loaded dynamically during role runtime using the `include_vars` module and set OS specifics variable's.

* Debian Family

```yaml
iptables_packages:
  - iptables-persistent
  - libnetfilter-conntrack3

iptables_services:
  - name: netfilter-persistent
    state: started
    enabled: true

iptables_conf_v4: /etc/iptables/rules.v4
iptables_conf_v6: /etc/iptables/rules.v6

iptables_binary_ipv4: /usr/sbin/iptables
iptables_binary_ipv6: /usr/sbin/ip6tables
iptables_save_binary_ipv4: /usr/sbin/iptables-save
iptables_save_binary_ipv6: /usr/sbin/ip6tables-save
```

* RedHat Family

```yaml
iptables_packages:
  - iptables-services
  - libnetfilter_conntrack

iptables_services:
  - name: iptables
    state: started
    enabled: true
  - name: ip6tables
    state: started
    enabled: true
  - name: firewalld
    state: stopped
    enabled: false

iptables_conf_v4: /etc/sysconfig/iptables
iptables_conf_v6: /etc/sysconfig/ip6tables

iptables_binary_ipv4: /usr/sbin/iptables
iptables_binary_ipv6: /usr/sbin/ip6tables
iptables_save_binary_ipv4: /usr/sbin/iptables-save
iptables_save_binary_ipv6: /usr/sbin/ip6tables-save
```


## AUTHOR

Dany GINHOUX - https://github.com/dginhoux



## LICENSE

MIT
