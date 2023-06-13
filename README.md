# ROLE dginhoux.iptables



## DESCRIPTION

This ansible role configure iptables for both iv4 and ipv6 stacks.<br />
It also save rules to ensure rules are applied at machine startup.
<br /><br />
A special feature is added on save function. It used for remove line with content not necessary to be persistent, like docker rules (variable iptables_conf_remove_pattern), is removed with a lineinfile module.




## REQUIREMENTS

#### SUPPORTED PLATFORMS

This role require a supported platform.<br />
It will skip node with unsupported platform to avoid any compatibility problem.<br />
This behaviour can be bypassed by settings the following variable `skip_check_platform_compatibility=true`.

| Platform | Versions |
|----------|----------|
| Debian | buster, bullseye |
| Fedora | 33, 34, 35, 36, 37, 38 |
| EL | 7, 8 |

#### ANSIBLE VERSION

Ansible >= 2.13

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
---

#### step 1 install necessary packages
iptables_packages: true


#### step 2 reset everything
iptables_reset: true
iptables_reset_default_policy_list:
  # table filter ipv4
  - {chain: INPUT, table: filter, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: OUTPUT, table: filter, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: FORWARD, table: filter, state: present, policy: ACCEPT, ip_version: ipv4}
  # table filter ipv6
  - {chain: INPUT, table: filter, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: OUTPUT, table: filter, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: FORWARD, table: filter, state: present, policy: ACCEPT, ip_version: ipv6}
  # table nat ipv4
  - {chain: PREROUTING, table: nat, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: INPUT, table: nat, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: OUTPUT, table: nat, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: POSTROUTING, table: nat, state: present, policy: ACCEPT, ip_version: ipv4}
  # table nat ipv6
  - {chain: PREROUTING, table: nat, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: INPUT, table: nat, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: OUTPUT, table: nat, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: POSTROUTING, table: nat, state: present, policy: ACCEPT, ip_version: ipv6}
  # table mangle ipv4
  - {chain: PREROUTING, table: mangle, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: INPUT, table: mangle, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: FORWARD, table: mangle, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: OUTPUT, table: mangle, state: present, policy: ACCEPT, ip_version: ipv4}
  - {chain: POSTROUTING, table: mangle, state: present, policy: ACCEPT, ip_version: ipv4}
  # table mangle ipv6
  - {chain: PREROUTING, table: mangle, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: INPUT, table: mangle, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: FORWARD, table: mangle, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: OUTPUT, table: mangle, state: present, policy: ACCEPT, ip_version: ipv6}
  - {chain: POSTROUTING, table: mangle, state: present, policy: ACCEPT, ip_version: ipv6}


#### step 3 create customs rules
## https://docs.ansible.com/ansible/latest/collections/ansible/builtin/iptables_module.html
iptables_rules:
  - { chain: INPUT, table: filter, state: present, ip_version: ipv4, jump: DROP, 
    ctstate: INVALID, comment: "drop invalid connections" }
  - { chain: INPUT, table: filter, state: present, ip_version: ipv4, jump: ACCEPT,
    ctstate: ESTABLISHED,RELATED, comment: "allow established/related connections" }
  - { chain: INPUT, table: filter, state: present, ip_version: ipv4, jump: ACCEPT,
    in_interface: lo }
  - {chain: INPUT, table: filter, state: present, comment: allow_icmp_ping, ip_version: ipv4,
    protocol: icmp, icmp_type: 8, jump: ACCEPT}
  - {chain: INPUT, table: filter, state: present, comment: allow_ssh, ip_version: ipv4,
    protocol: tcp, destination_port: 22, jump: ACCEPT, ptables_save_conf: true}
  - { chain: OUTPUT, table: filter, state: present, ip_version: ipv4, jump: DROP, 
    ctstate: INVALID, comment: "drop invalid connections" }
  - { chain: OUTPUT, table: filter, state: present, ip_version: ipv4, jump: ACCEPT,
    ctstate: ESTABLISHED,RELATED, comment: "allow established/related connections" }
  - { chain: OUTPUT, table: filter, state: present, ip_version: ipv4, jump: ACCEPT,
    out_interface: lo }
  - {chain: OUTPUT, table: filter, state: present, comment: allow_dns, ip_version: ipv4,
    protocol: udp, destination_port: 53, jump: ACCEPT}
  - {chain: OUTPUT, table: filter, state: present, comment: allow_ntp, ip_version: ipv4,
    protocol: udp, destination_port: 123, jump: ACCEPT}
  - {chain: OUTPUT, table: filter, state: present, comment: allow_http_hhtps, ip_version: ipv4,
    protocol: tcp, destination_ports: [80,443], jump: ACCEPT}


#### step 4 set policies default action 
iptables_policies:
  # table filter ipv4
  - {chain: INPUT, table: filter, state: present, policy: DROP, ip_version: ipv4}
  - {chain: OUTPUT, table: filter, state: present, policy: DROP, ip_version: ipv4}
  - {chain: FORWARD, table: filter, state: present, policy: DROP, ip_version: ipv4}
  # table filter ipv6
  - {chain: INPUT, table: filter, state: present, policy: DROP, ip_version: ipv6}
  - {chain: OUTPUT, table: filter, state: present, policy: DROP, ip_version: ipv6}
  - {chain: FORWARD, table: filter, state: present, policy: DROP, ip_version: ipv6}
  # table nat ipv4
  # - {chain: PREROUTING, table: nat, state: present, policy: DROP, ip_version: ipv4}
  # - {chain: INPUT, table: nat, state: present, policy: DROP, ip_version: ipv4}
  # - {chain: OUTPUT, table: nat, state: present, policy: DROP, ip_version: ipv4}
  # - {chain: POSTROUTING, table: nat, state: present, policy: DROP, ip_version: ipv4}
  # table nat ipv6
  # - {chain: PREROUTING, table: nat, state: present, policy: DROP, ip_version: ipv6}
  # - {chain: INPUT, table: nat, state: present, policy: DROP, ip_version: ipv6}
  # - {chain: OUTPUT, table: nat, state: present, policy: DROP, ip_version: ipv6}
  # - {chain: POSTROUTING, table: nat, state: present, policy: DROP, ip_version: ipv6} 
  # table mangle ipv4
  # - {chain: PREROUTING, table: mangle, state: present, policy: DROP, ip_version: ipv4}
  # - {chain: INPUT, table: mangle, state: present, policy: DROP, ip_version: ipv4}
  # - {chain: FORWARD, table: mangle, state: present, policy: DROP, ip_version: ipv4}
  # - {chain: OUTPUT, table: mangle, state: present, policy: DROP, ip_version: ipv4}
  # - {chain: POSTROUTING, table: mangle, state: present, policy: DROP, ip_version: ipv4}
  # table mangle ipv6
  # - {chain: PREROUTING, table: mangle, state: present, policy: DROP, ip_version: ipv6}
  # - {chain: INPUT, table: mangle, state: present, policy: DROP, ip_version: ipv6}
  # - {chain: FORWARD, table: mangle, state: present, policy: DROP, ip_version: ipv6}
  # - {chain: OUTPUT, table: mangle, state: present, policy: DROP, ip_version: ipv6}
  # - {chain: POSTROUTING, table: mangle, state: present, policy: DROP, ip_version: ipv6}


#### step 5 save iptables persistent config
iptables_save: true
iptables_conf_remove_pattern:
  - ":DOCKER-INGRESS"
  - ":DOCKER-ISOLATION-STAGE-1"
  - ":DOCKER-ISOLATION-STAGE-2"
  - ":DOCKER-USER"
  - :DOCKER
  - -j DOCKER-INGRESS
  - -j DOCKER-ISOLATION-STAGE-1
  - -j DOCKER-ISOLATION-STAGE-2
  - -j DOCKER-USER
  - -j DOCKER
  - -A DOCKER-INGRESS
  - -A DOCKER-ISOLATION-STAGE-1
  - -A DOCKER-ISOLATION-STAGE-2
  - -A DOCKER-USER
  - -A DOCKER
  - docker_gwbridge
  - docker0


```

#### DEFAULT OS SPECIFIC VARIABLES

Those variables files are located in `vars/*.yml` are used to handle OS differences.<br />
One of theses is loaded dynamically during role runtime using the `include_vars` module and set OS specifics variable's.

* Debian Family

```yaml
iptables_pkgs:
  - name: iptables-persistent
    state: present
  - name: libnetfilter-conntrack3
    state: present

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
iptables_pkgs:
  - name: iptables-services
    state: present
  - name: libnetfilter_conntrack
    state: present

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
