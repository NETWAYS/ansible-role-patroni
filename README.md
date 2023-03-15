# Ansible Role for Patroni

[![CI](https://github.com/NETWAYS/ansible-role-patroni/actions/workflows/ci.yml/badge.svg?event=push)](https://github.com/NETWAYS/ansible-role-patroni/actions/workflows/ci.yml)

An Ansible role which installs and configures [Patroni](https://github.com/zalando/patroni/) - HA solution for PostgreSQL.

**This branch is managed by NETWAYS.**

## Requirements

This role requires root privileges, so tell ansible to use `become: true` in any [convenient way](http://docs.ansible.com/ansible/latest/become.html) for you.

## Role Variables

### General

- `patroni_postgresql_version`: The PostgreSQL version that will be installed (**Default**: `11`)
- `patroni_postgresql_version_latest`: Install latest major version of PostgreSQL (**Default:** `false`)
- `patroni_postgresql_exists`: Does PostgreSQL already exist? (**Default:** `false`)
- `patroni_install_from_pip`: Install Patroni with pip (**Default:** `true`)
- `patroni_install_haproxy`: Install HAProxy (**Default:** `false`)
- `patroni_install_postgresql_repo`: Configure PostgreSQL repository? (**Default:** `true`)
- `patroni_config_dir`: Path of Patroni configuration directory (**Default:** `/etc/patroni`)
- `patroni_config_file`: Filename of the Patroni configuration (**Default:** `"{{ inventory_hostname }}.yml"`)
- `patroni_system_user`: The Patroni system user (**Default:** `postgres`)
- `patroni_system_group`: The Patroni system group (**Default:** `postgres`)
- `patroni_backup_on_copy`: Create a backup before copying files on host (**Default:** `true`)
- `patroni_exec_start_pre`: Command executed before Patroni will be started (**Default:** `"/bin/mkdir -m 2750 -p /var/run/postgresql/{{ patroni_postgresql_version }}-main.pg_stat_tmp"`)
- `patroni_pip_packages`: Packages that will be installed with pip. Defaults:
  - `- { name: "setuptools", state: "latest",  umask: "0022", executable: "pip3" }`
  - `- { name: "patroni[{{ patroni_dcs }}]", state: "present", umask: "0022", executable: "pip3" }`
- `patroni_replication_username`: PostgreSQL DB username for replication (**Default:** `replicator`)
- `patroni_replication_password`: PostgreSQL DB password for replication (**Default:** `repuserpasswd`)
- `patroni_superuser_username`: PostgreSQL DB username for master (**Default:** `postgres`)
- `patroni_superuser_password`: PostgreSQL DB password for master (**Default:** `supersecretpostgrespasswd`)
- `patroni_dcs`: Which DCS is configured (**Default:** `etcd`)
- `patroni_dcs_exists`: Does a DCS exist (**Default:** `true`)

### Global/Universal

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#global-universal

- `patroni_scope`: Scope of the Patroni cluster (**Default:** `main`)
- `patroni_namespace`: Namespace of the Patroni cluster (**Default:** `/service/`)
- `patroni_name`: Name of the Patroni node (**Default:** `"{{ inventory_hostname }}"`)

### Log

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#log

- `patroni_log_destination`: The  (**Default:** `stderr`) #TODO
- `patroni_log_level`: Sets the general logging level. Default value is INFO (see https://docs.python.org/3.6/library/logging.html#levels) (**Default:** `INFO`)
- `patroni_log_format`: Sets the log formatting string. Default value is %(asctime)s %(levelname)s: %(message)s (see https://docs.python.org/3.6/library/logging.html#logrecord-attributes) (**Default:** `"%(asctime)s %(levelname)s: %(message)s"`)
- `patroni_log_dateformat`: Sets the datetime formatting string. (see https://docs.python.org/3.6/library/logging.html#logging.Formatter.formatTime) (**Default:** `""`)
- `patroni_log_max_queue_size`: Patroni is using two-step logging. Log records are written into the in-memory queue and there is a separate thread which pulls them from the queue and writes to stderr or file. The maximum size of the internal queue is limited by default by 1000 records, which is enough to keep logs for the past 1h20m. (**Default:** `1000`)
- `patroni_log_dir`: Directory to write application logs to. The directory must exist and be writable by the user executing Patroni. If you set this value, the application will retain 4 25MB logs by default. You can tune those retention values with `patroni_log_file_num` and `patroni_log_file_size` (**Default:** `/var/log/patroni`)
- `patroni_log_file_num`: The number of application logs to retain (**Default:** `4`)
- `patroni_log_file_size`: Size of patroni.log file (in bytes) that triggers a log rolling (**Default:** `25000000`)
- `patroni_log_loggers`: This section allows redefining logging level per python module. **Defaults:**
  - `- { module: "patroni.postmaster", level: "WARNING" }`
  - `- { module: "urllib3", level: "DEBUG" }`

### REST API

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#rest-api
- `patroni_restapi_port`: The Port for the `patroni_restapi_port`. (**Default:** `8008`)
- `patroni_restapi_connect_address`: IP address (or hostname) and port, to access the Patroni’s REST API. (**Default:** `"{{ ansible_host }}:{{ patroni_restapi_port }}"`)
- `patroni_restapi_listen`: IP address (or hostname) and port that Patroni will listen to for the REST API. (*Default* `"0.0.0.0:{{ patroni_restapi_port }}"`)
- `patroni_restapi_username`: (optional): Username for authentication. (**Default** `undefined`)
- `patroni_restapi_password`: (optional): Password for authentication. (**Default** `undefined`)
- `patroni_restapi_certfile`: (optional): Specifies the file with the certificate in the PEM format. If the certfile is not specified or is left empty, the API server will work without SSL. (**Default:** `undefined`)
- `patroni_restapi_keyfile`: (optional): Specifies the file with the secret key in the PEM format. (**Default:** `undefined`)
- `patroni_restapi_keyfile_password`: (optional): Specifies a password for decrypting the keyfile. (**Default:** `undefined`)
- `patroni_restapi_cafile`: (optional): Specifies the file with the CA_BUNDLE with certificates of trusted CAs to use while verifying client certs. (**Default:** `undefined`)
- `patroni_restapi_ciphers`: (optional): Specifies the permitted cipher suites (e.g. “ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES256-GCM-SHA384:ECDHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES128-GCM-SHA256:!SSLv1:!SSLv2:!SSLv3:!TLSv1:!TLSv1.1”). (**Default:** `undefined`)
- `patroni_restapi_verify_client`: (optional): `none` (default in Patroni), `optional` or `required`. When none REST API will not check client certificates. When required client certificates are required for all REST API calls. When optional client certificates are required for all unsafe REST API endpoints. (**Default:** `undefined`)
- `patroni_restapi_http_extra_headers`: (optional): HTTP headers let the REST API server pass additional information with an HTTP response. (**Default:** `undefined`)
- `patroni_restapi_https_extra_headers`:  (optional): HTTPS headers let the REST API server pass additional information with an HTTP response when TLS is enabled. This will also pass additional information set in `patroni_restapi_http_extra_headers`. (**Default:** `undefined`)

## etcd

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#etcd

- `patroni_etcd_host`: The host:port for the etcd endpoint. (**Default:** "")
- `patroni_etcd_hosts`: List of etcd endpoint in format host1:port1,host2:port2,etc… Could be a comma separated string or an actual yaml list. (**Default:** 127.0.0.1:2379)
- `patroni_etcd_use_proxies`: If this parameter is set to true, Patroni will consider hosts as a list of proxies and will not perform a topology discovery of etcd cluster. (**Default:** false)
- `patroni_etcd_url`: URL for the etcd. (**Default:** "")
- `patroni_etcd_proxy`: Proxy url for the etcd. If you are connecting to the etcd using proxy, use this parameter instead of url. (**Default:** "")
- `patroni_etcd_srv`: Domain to search the SRV record(s) for cluster autodiscovery. Patroni will try to query these SRV service names for specified domain (in that order until first success): _etcd-client-ssl, _etcd-client, _etcd-ssl, _etcd, _etcd-server-ssl, _etcd-server. (**Default:** "")
- `patroni_etcd_protocol`:  (optional) http or https, if not specified http is used. If the `patroni_etcd_url` or `patroni_etcd_proxy` is specified - will take protocol from them. (**Default:** http)
- `patroni_etcd_username`: (optional) username for etcd authentication. (**Default:** "")
- `patroni_etcd_password`: (optional) password for etcd authentication. (**Default:** "")
- `patroni_etcd_cacert`: (optional) The ca certificate. If present it will enable validation. (**Default:** "")
- `patroni_etcd_cert`: (optional) file with the client certificate. (**Default:** "")
- `patroni_etcd_key`: (optional) file with the client key. Can be empty if the key is part of cert. (**Default:** "")

## etcdv3

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#etcdv3

- `patroni_etcd_config_section`: If you want that Patroni works with Etcd cluster via protocol version 3, you need to use the `etcd3` section in the Patroni configuration file. All configuration parameters are the same as for `etcd`. (**Default:** `etcd`)

## Consul

Read more here: https://patroni.readthedocs.io/en/latest/ENVIRONMENT.html#consul

- `patroni_consul_host`: The host:port for the Consul local agent. (**Default:** `"127.0.0.1:{{ patroni_consul_port | default(8500) }}"`)
- `patroni_consul_port`: (optional) Consul port. (**Default:**  8500)
- `patroni_consul_url`: URL for the Consul local agent, in format: http(s)://host:port. (**Default:** "")
- `patroni_consul_scheme`: (optional) http or https, defaults to http. (**Default:** http)
- `patroni_consul_token`: (optional) ACL token. (**Default:** "")
- `patroni_consul_verify`: (optional) whether to verify the SSL certificate for HTTPS requests. (**Default:** "")
- `patroni_consul_cacert`: (optional) The ca certificate. If present it will enable validation. (**Default:** "")
- `patroni_consul_cert`: (optional) file with the client certificate. (**Default:** "")
- `patroni_consul_key`: (optional) file with the client key. Can be empty if the key is part of cert. (**Default:** "")
- `patroni_consul_dc`: (optional) Datacenter to communicate with. By default the datacenter of the host is used. (**Default:** "")
- `patroni_consul_checks`: (optional) list of Consul health checks used for the session. By default an empty list is used. (**Default:** "")
- `patroni_consul_register_service`: (optional) whether or not to register a service with the name defined by the scope parameter and the tag master, primary, replica, or standby-leader depending on the node’s role. Defaults to false. (**Default:** false)
- `patroni_consul_service_check_interval`: (optional) how often to perform health check against registered url. (**Default:** 5s)

# Zookeeper

Read more here: https://patroni.readthedocs.io/en/latest/ENVIRONMENT.html#zookeeper
- `patroni_zookeeper_hosts`: Comma separated list of ZooKeeper cluster members: “‘host1:port1’,’host2:port2’,’etc…’”. It is important to quote every single entity!. 127.0.0.1:2181

# Bootstrap configuration

Read more here: https://patroni.readthedocs.io/en/latest/SETTINGS.html#bootstrap-configuration

- `patroni_bootstrap_dcs_ttl`: TTL for DCS (**Default:** `30`)
- `patroni_bootstrap_dcs_loop_wait`: Loop wait for DCS (**Default:** `10`)
- `patroni_bootstrap_dcs_retry_timeout`: Retry timeout for DCS (**Default:** `10`)
- `patroni_bootstrap_dcs_maximum_lag_on_failover`: Maximum lag on failover (**Default:** `1048576`)
- `patroni_bootstrap_dcs_master_start_timeout`: Start timeout for master (**Default:** `300`)
- `patroni_bootstrap_dcs_synchronous_mode`: Synchronous mode for DCS (**Default:** `false`)
- `patroni_bootstrap_dcs_synchronous_mode_strict`: Strict synchronous mode (**Default:** `false`)
- `patroni_bootstrap_dcs_check_timeline`: Check timeline (**Default:** `false`)
- `patroni_bootstrap_dcs_standby_cluster`: Configuration of a standby cluster. **Defaults:**
  - `- { option: "host",                     value: "" }`
  - `- { option: "port",                     value: "" }`
  - `- { option: "primary_slot_name",        value: "" }`
  - `- { option: "create_replica_methods",   value: "" }`
  - `- { option: "restore_command",          value: "" }`
  - `- { option: "archive_cleanup_command",  value: "" }`
  - `- { option: "recovery_min_apply_delay", value: "" }`

- `patroni_bootstrap_dcs_postgresql_use_pg_rewind`: Use PostgreSQL pg_rewind (**Default:** `false`)
- `patroni_bootstrap_dcs_postgresql_use_slots`: Use PostgreSQL slots (**Default:** `true`)
- `patroni_bootstrap_dcs_postgresql_parameters`: PostgreSQL parameters. **Defaults:**
  - `- { option: "max_connections",           value: "100" }`
  - `- { option: "max_locks_per_transaction", value: "64" }`
  - `- { option: "max_worker_processes",      value: "8" }`
  - `- { option: "max_prepared_transactions", value: "0" }`
  - `- { option: "wal_level",                 value: "replica" }`
  - `- { option: "wal_log_hints",             value: "on" }`
  - `- { option: "track_commit_timestamp",    value: "off" }`
  - `- { option: "max_wal_senders",           value: "10" }`
  - `- { option: "max_replication_slots",     value: "10" }`
  - `- { option: "wal_keep_segments",         value: "8" }`

- `patroni_bootstrap_dcs_postgresql_recovery_conf`: PostgreSQL recovery.conf parameters (**Default:** `[]`)
  - `#- { option: "standby_mode",    value: "on" }`
  - `#- { option: "restore_command", value: "cp ../wal_archive/%f %p" }`

- `patroni_bootstrap_dcs_postgresql_pg_hba`: PostgreSQL pg_hba.conf parameters (**Default:** `[]`)
  - `#- { type: "host", database: "all",         user: "all",                                address: "0.0.0.0/0", method: "ident", options: "map=omicron" }`
  - `#- { type: "host", database: "replication", user: "{{ patroni_replication_username }}", address: "0.0.0.0/0", method: "md5" }`

`patroni_bootstrap_dcs_postgresql_pg_ident`: PostgreSQL pg_ident.conf (**Default:** `[]`)
  - `#- { mapname: "omicron", sysuser: "robert", pguser: "bob" }`

`patroni_bootstrap_dcs_slots`: DCS slots parameters (**Default:** [])
  - `#-{ name: "permanent_physical_1", type: "physical" }`
  - `#-{ name: "permanent_logical_1",  type: "logical", database: "foo", plugin: "pgoutput" }`

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
