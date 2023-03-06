# Ansible Role for Patroni

[![CI](https://github.com/NETWAYS/ansible-role-patroni/actions/workflows/ci.yml/badge.svg?event=push)](https://github.com/NETWAYS/ansible-role-patroni/actions/workflows/ci.yml)

An Ansible role which installs and configures [Patroni](https://github.com/zalando/patroni/) - HA solution for PostgreSQL.

**This branch is managed by NETWAYS.**

## Requirements

This role requires root privileges, so tell ansible to use `become: true` in any [convenient way](http://docs.ansible.com/ansible/latest/become.html) for you.

## Role Variables

- patroni_postgresql_version: 11
- patroni_postgresql_version_latest: false
- patroni_postgresql_exists: false
- patroni_install_from_pip: true
- patroni_install_haproxy: false
- patroni_install_postgresql_repo: true
- patroni_config_dir: /etc/patroni
- patroni_config_file: "{{ inventory_hostname }}.yml"
- patroni_system_user: postgres
- patroni_system_group: postgres

## Dependencies

There are no dependencies for the role, but Patroni itself needs a DCS (Etcd, Consul, ZooKeeper or Exhibitor) to be installed and configured properly and it's your responsibility to make it up and running before using this role.
Currently, it is supposed that a DCS is prepared. Otherwise, you can try one of the following roles (just uncomment respective section [here](https://github.com/kostiantyn-nemchenko/ansible-role-patroni/blob/master/meta/main.yml#L28) and set `patroni_dcs_exists` variable to false):

* [**andrewrothstein.etcd-cluster**](https://github.com/andrewrothstein/ansible-etcd-cluster)
* [**brianshumate.consul**](https://github.com/brianshumate/ansible-consul)
* [**AnsibleShipyard.ansible-zookeeper**](https://github.com/AnsibleShipyard/ansible-zookeeper)

## Example Playbook

    - hosts: postgresql-servers
      become: yes
      roles:
        - kostiantyn-nemchenko.patroni

## License

MIT

## Author Information
Kostiantyn Nemchenko <kostiantyn.nemchenko@gmail.com>
