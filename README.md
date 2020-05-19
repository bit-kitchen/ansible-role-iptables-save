ansible-role-iptables-save
==========================

An ansible role that installs the package required for iptables rule persistence and listens to `iptables-save` and `ip6tables-save` for rule persistence.

Requirements
------------

None.

Role Variables
--------------

None.

Dependencies
------------

None.

Example Playbook
----------------

```yml
- name: Forward traffic to port 443 to another host
  hosts: localhost
  gather_facts: yes
  roles:
  - bit_kitchen.iptables_save
  vars:
    self_addr: "{{ ansible_facts.eth0.ipv4.address }}"
    self_port: 443
    dest_addr: 10.20.30.40
    dest_port: 443
    protocol: tcp
  tasks:
  - iptables:
      action: insert
      table: nat
      chain: PREROUTING
      protocol: "{{ protocol }}"
      destination_port: "{{ self_port }}"
      jump: DNAT
      to_destination: "{{ dest_addr }}:{{ dest_port }}"
    notify: iptables-save
  - iptables:
      action: insert
      table: nat
      chain: POSTROUTING
      protocol: "{{ protocol }}"
      destination: "{{ dest_addr }}"
      destination_port: "{{ dest_port }}"
      jump: SNAT
      to_source: "{{ self_addr }}"
    notify: iptables-save
```

License
-------

[MIT](LICENSE)

Author Information
------------------

[bit.kitchen](https://github.com/bit-kitchen)
