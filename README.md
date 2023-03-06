# Ansible Role for Patroni

[![CI](https://github.com/NETWAYS/ansible-role-patroni/actions/workflows/ci.yml/badge.svg?event=push)](https://github.com/NETWAYS/ansible-role-patroni/actions/workflows/ci.yml)

An Ansible role which installs and configures [Patroni](https://github.com/zalando/patroni/) - HA solution for PostgreSQL.

**This branch is managed by NETWAYS.**

## Requirements

This role requires root privileges, so tell ansible to use `become: true` in any [convenient way](http://docs.ansible.com/ansible/latest/become.html) for you.

## Role Variables

- `patroni_postgresql_version`: The PostgreSQL version that will be installed (default: `11`)
- `patroni_postgresql_version_latest`: Install latest major version of PostgreSQL (default: `false`)
- `patroni_postgresql_exists`: Does PostgreSQL already exist? (default: `false`)
- `patroni_install_from_pip`: Install Patroni with pip (default: `true`)
- `patroni_install_haproxy`: Install HAProxy (default: `false`)
- `patroni_install_postgresql_repo`: Configure PostgreSQL repository? (default: `true`)
- `patroni_config_dir`: Path of Patroni configuration directory (default: `/etc/patroni`)
- `patroni_config_file`: Filename of the Patroni configuration (default: `"{{ inventory_hostname }}.yml"`)
- `patroni_system_user`: The Patroni system user (default: `postgres`)
- `patroni_system_group`: The Patroni system group (default: `postgres`)
- `patroni_backup_on_copy`: Create a backup before copying files on host (default: `true`)
- `patroni_exec_start_pre`: Command executed before Patroni will be started (default: `"/bin/mkdir -m 2750 -p /var/run/postgresql/{{ patroni_postgresql_version }}-main.pg_stat_tmp"`)
- `patroni_pip_packages`: Packages that will be installed with pip
  - { name: "setuptools",                 state: "latest",  umask: "0022", executable: "pip3" }
  - { name: "patroni[{{ patroni_dcs }}]", state: "present", umask: "0022", executable: "pip3" }
- `patroni_replication_username`: PostgreSQL DB username for replication (default: `replicator`)
- `patroni_replication_password`: PostgreSQL DB password for replication (default: `repuserpasswd`)
- `patroni_superuser_username`: PostgreSQL DB username for master (default: `postgres`)
- `patroni_superuser_password`: PostgreSQL DB password for master (default: `supersecretpostgrespasswd`)

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
