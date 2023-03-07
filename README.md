# Ansible Role for Patroni

[![CI](https://github.com/NETWAYS/ansible-role-patroni/actions/workflows/ci.yml/badge.svg?event=push)](https://github.com/NETWAYS/ansible-role-patroni/actions/workflows/ci.yml)

An Ansible role which installs and configures [Patroni](https://github.com/zalando/patroni/) - HA solution for PostgreSQL.

**This branch is managed by NETWAYS.**

## Requirements

This role requires root privileges, so tell ansible to use `become: true` in any [convenient way](http://docs.ansible.com/ansible/latest/become.html) for you.

## Role Variables

### General

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
- `patroni_pip_packages`: Packages that will be installed with pip. Defaults:
  - `{ name: "setuptools", state: "latest",  umask: "0022", executable: "pip3" }`
  - `{ name: "patroni[{{ patroni_dcs }}]", state: "present", umask: "0022", executable: "pip3" }`
- `patroni_replication_username`: PostgreSQL DB username for replication (default: `replicator`)
- `patroni_replication_password`: PostgreSQL DB password for replication (default: `repuserpasswd`)
- `patroni_superuser_username`: PostgreSQL DB username for master (default: `postgres`)
- `patroni_superuser_password`: PostgreSQL DB password for master (default: `supersecretpostgrespasswd`)

### Global/Universal

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#global-universal

- `patroni_scope`: Scope of the Patroni cluster (default: `main`)
- `patroni_namespace`: Namespace of the Patroni cluster (default: `/service/`)
- `patroni_name`: Name of the Patroni node (default: `"{{ inventory_hostname }}"`)

### Log

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#log

- `patroni_log_destination`: The  (default: `stderr`) #TODO
- `patroni_log_level`: Sets the general logging level. Default value is INFO (see https://docs.python.org/3.6/library/logging.html#levels) (default: `INFO`)
- `patroni_log_format`: Sets the log formatting string. Default value is %(asctime)s %(levelname)s: %(message)s (see https://docs.python.org/3.6/library/logging.html#logrecord-attributes) (default: `"%(asctime)s %(levelname)s: %(message)s"`)
- `patroni_log_dateformat`: Sets the datetime formatting string. (see https://docs.python.org/3.6/library/logging.html#logging.Formatter.formatTime) (default: `""`)
- `patroni_log_max_queue_size`: Patroni is using two-step logging. Log records are written into the in-memory queue and there is a separate thread which pulls them from the queue and writes to stderr or file. The maximum size of the internal queue is limited by default by 1000 records, which is enough to keep logs for the past 1h20m. (default: `1000`)
- `patroni_log_dir`: Directory to write application logs to. The directory must exist and be writable by the user executing Patroni. If you set this value, the application will retain 4 25MB logs by default. You can tune those retention values with `patroni_log_file_num` and `patroni_log_file_size` (default: `/var/log/patroni`)
- `patroni_log_file_num`: The number of application logs to retain (default: `4`)
- `patroni_log_file_size`: Size of patroni.log file (in bytes) that triggers a log rolling (default: `25000000`)
- `patroni_log_loggers`: This section allows redefining logging level per python module. Defaults:
  - `{ module: "patroni.postmaster", level: "WARNING" }`
  - `{ module: "urllib3", level: "DEBUG" }`

### REST API

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#rest-api
`patroni_restapi_port`: The Port for the `patroni_restapi_port`. Default: `8008`
`patroni_restapi_connect_address`: IP address (or hostname) and port, to access the Patroni’s REST API. Default: `"{{ ansible_host }}:{{ patroni_restapi_port }}"`
`patroni_restapi_listen`: IP address (or hostname) and port that Patroni will listen to for the REST API. Default `"0.0.0.0:{{ patroni_restapi_port }}"`
`patroni_restapi_username`: (optional): Username for authentication. Default `undefined`
`patroni_restapi_password`: (optional): Password for authentication. Default `undefined`
`patroni_restapi_certfile`: (optional): Specifies the file with the certificate in the PEM format. If the certfile is not specified or is left empty, the API server will work without SSL. Default: `undefined`
`patroni_restapi_keyfile`: (optional): Specifies the file with the secret key in the PEM format. Default: `undefined`
`patroni_restapi_keyfile_password`: (optional): Specifies a password for decrypting the keyfile. Default: `undefined`
`patroni_restapi_cafile`: (optional): Specifies the file with the CA_BUNDLE with certificates of trusted CAs to use while verifying client certs. Default: `undefined`
`patroni_restapi_ciphers`: (optional): Specifies the permitted cipher suites (e.g. “ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:!SSLv1:!SSLv2:!SSLv3:!TLSv1:!TLSv1.1”). Default: `undefined`
`patroni_restapi_verify_client`: (optional): `none` (default in Patroni), `optional` or `required`. When none REST API will not check client certificates. When required client certificates are required for all REST API calls. When optional client certificates are required for all unsafe REST API endpoints. Default: `undefined` 
`patroni_restapi_http_extra_headers`: (optional): HTTP headers let the REST API server pass additional information with an HTTP response. Default: `undefined`
`patroni_restapi_https_extra_headers`:  (optional): HTTPS headers let the REST API server pass additional information with an HTTP response when TLS is enabled. This will also pass additional information set in `patroni_restapi_http_extra_headers`. Default: `undefined`


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
