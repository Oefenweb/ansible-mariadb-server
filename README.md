## mariadb-server

[![Build Status](https://travis-ci.org/Oefenweb/ansible-mariadb-server.svg?branch=master)](https://travis-ci.org/Oefenweb/ansible-mariadb-server) [![Ansible Galaxy](http://img.shields.io/badge/ansible--galaxy-mariadb--server-blue.svg)](https://galaxy.ansible.com/Oefenweb/mariadb-server)

Set up a [mariadb-server](https://mariadb.com/products/technology/server) server in Debian-like systems.

#### Requirements

* `python-mysqldb` (will be installed)
* `tee` (will not be installed)

#### Variables

##### General

* `mariadb_server_root_password`: [default: `+eswuw9uthUteFreyAqu`]: Root password **Make sure to change!**

* `mariadb_server_install`: [`['xtrabackup']`]: Additional packages to install

* `mariadb_server_etc_my_cnf`: [default: `[]`]: Global configuration declarations
* `mariadb_server_etc_my_cnf_includedir`: [optional]: Used to include other option files from this directory (e.g. `/etc/mysql/conf.d/`)

* `mariadb_server_user_root_cnf_manage`: [default: `true`]: Whether or not to manage `~root/.my.cnf`
* `mariadb_server_user_root_cnf`: [default: `mariadb_server_user_root_cnf_preset`, see `defaults/main.yml`]: Root user configuration declarations

##### SSL

* `mariadb_server_ssl_map`: [default: `{}`]: SSL declarations
* `mariadb_server_ssl_map.key`: [required]: The identifier of the file (e.g. `ca-cert`)
* `mariadb_server_ssl_map.key.src`: [required]: The local path of the file to copy, can be absolute or relative (e.g. `../../../files/mariadb-server/etc/mysql/ca-cert.pem`)
* `mariadb_server_ssl_map.key.dest`: [required]: The remote path of the file to copy (e.g. `/etc/mysql/ca-cert.pem`)
* `mariadb_server_ssl_map.key.owner`: [optional, default `root`]: The name of the user that should own the file
* `mariadb_server_ssl_map.key.group`: [optional, default `mysql`]:The name of the group that should own the file
* `mariadb_server_ssl_map.key.mode`: [optional, default `0640`]: The mode of the file

##### Plugins

* `mariadb_server_plugins_present`: [default: `[]`]: Plugins to `INSTALL`
* `mariadb_server_plugins_present.{n}.name`: [required]: The name of the plugin (e.g. `QUERY_RESPONSE_TIME_AUDIT`)
* `mariadb_server_plugins_present.{n}.soname`: [required]: The base name of the shared library file that contains the code that implements the plugin (e.g. `query_response_time.so`)

* `mariadb_server_plugins_absent`: [default: `[]`]: Plugins to `UNINSTALL`
* `mariadb_server_plugins_absent.{n}.name`: [required]: The name of the plugin

##### Databases

* `mariadb_server_databases_present`: [default: `[]`]: Databases to `CREATE`
* `mariadb_server_databases_present.{n}.name`: [required]: The name of the database
* `mariadb_server_databases_present.{n}.collation`: [optional, default: `utf8_general_ci`]: The collation of the database
* `mariadb_server_databases_present.{n}.encoding`: [optional, default: `utf8`]: The character set of the database

* `mariadb_server_databases_absent`: [default: `[{name: test}]`]: Databases to `DROP`
* `mariadb_server_databases_absent.{n}.name`: [required]: The name of the database

##### Users

* `mariadb_server_users_present`: [default: `[]`]: Users to `CREATE`
* `mariadb_server_users_present.{n}.name`: [required]: The name of the user
* `mariadb_server_users_present.{n}.password`: [required]: The password of the user
* `mariadb_server_users_present.{n}.privs`: [required]: Privileges (e.g. `'test.*:ALL'`)
* `mariadb_server_users_present.{n}.hosts`: [optional, default: `mariadb_server_users_present_hosts`]: Hosts to `CREATE` privileges for (e.g. `%`)

* `mariadb_server_users_present_hosts`: [default: `[localhost]`]: Hosts to `CREATE` privileges for

* `mariadb_server_users_absent`: [default: `[{name: ''}]`]: Users to `DROP`
* `mariadb_server_users_absent.{n}.name`: [required]: The name of the user
* `mariadb_server_users_absent.{n}.hosts`: [optional, default: `mariadb_server_users_absent_hosts`]: Hosts to `DROP` privileges for (e.g. `%`)

* `mariadb_server_users_absent_hosts`: [default: `[{{ ansible_hostname }}, 127.0.0.1, localhost, ::1, %]`]: Hosts to `DROP` privileges for

##### Queries

* `mariadb_server_queries`: [default: `[]`]: Query declarations
* `mariadb_server_queries.{n}.database`: [required]: Name of the database to execute queries on
* `mariadb_server_queries.{n}.queries`: [default: `[]`]: A list of queries to execute

##### Timezone info

* `mariadb_server_zoneinfo_manage`: [default: `false`]: Whether or not to load time zone tables
* `mariadb_server_zoneinfo_tz_dir`: [default: `/usr/share/zoneinfo`]: The zoneinfo directory path name
* `mariadb_server_zoneinfo_tz_file`: [default: `''`]: The path of a single time zone file (e.g. `/usr/share/zoneinfo/Europe/Amsterdam`)
* `mariadb_server_zoneinfo_tz_name`: [default: `''`]: A time zone name (e.g. `Europe/Amsterdam`)
* `mariadb_server_zoneinfo_command`: [default: `mysql_tzinfo_to_sql {{ mariadb_server_zoneinfo_tz_dir }}`]: The zoneinfo command to generate SQL (e.g. `mysql_tzinfo_to_sql {{ mariadb_server_zoneinfo_tz_file }} {{ mariadb_server_zoneinfo_tz_name }}`, `mysql_tzinfo_to_sql --leap {{ {{ mariadb_server_zoneinfo_tz_file }} }}`)

## Dependencies

None

## Recommended

* `mariadb-client` ([see](https://github.com/Oefenweb/ansible-mariadb-client), when `mariadb_server_manage_root_my_cnf` is `false`)
* `limits` ([see](https://github.com/Oefenweb/ansible-limits))

#### Example(s)

##### Simple

```yaml
---
- hosts: all
  roles:
    - mariadb-server
```

##### Configure databases and users

```yaml
---
- hosts: all
  roles:
    - mariadb-server
  vars:
    mariadb_server_databases_present:
      - name: ipsum
      - name: dolor

    mariadb_server_databases_absent:
      - name: sit
      - name: amet

    mariadb_server_users_present_hosts:
      - 'localhost'
      - '%'

    mariadb_server_users_present:
      - name: consectetur
        password: 'elit'
        privs:
          - 'ipsum.*:ALL'
          - 'dolor.*:ALL'
      - name: adipiscing
        password: 'lacus'
        privs:
          - 'ipsum.*:SELECT'
          - 'dolor.*:INSERT,UPDATE'
        hosts:
          - '%'

    mariadb_server_users_absent:
      - name: urna
      - name: vehicula
        hosts:
          - '%'
```

##### Configure SSL

```yaml
- hosts: all
  roles:
    - mariadb-server
  vars:
    mariadb_server_ssl_map:
      ca-cert:
        src: ../../../files/mariadb-server/etc/mysql/ca-cert.pem
        dest: /etc/mysql/ca-cert.pem
      client-cert:
        src: ../../../files/mariadb-server/etc/mysql/client-cert.pem
        dest: /etc/mysql/client-cert.pem
      client-key:
        src: ../../../files/mariadb-server/etc/mysql/client-key.pem
        dest: /etc/mysql/client-key.pem
      server-cert:
        src: ../../../files/mariadb-server/etc/mysql/server-cert.pem
        dest: /etc/mysql/server-cert.pem
      server-key:
        src: ../../../files/mariadb-server/etc/mysql/server-key.pem
        dest: /etc/mysql/server-key.pem
    mariadb_server_etc_my_cnf:
      - section: client
        options:
          - name: ssl_cert
            value: "{{ mariadb_server_ssl_map['client-cert']['dest'] }}"
          - name: ssl_key
            value: "{{ mariadb_server_ssl_map['client-key']['dest'] }}"
      - section: mysqld
        options:
          - name: ssl_ca
            value: "{{ mariadb_server_ssl_map['ca-cert']['dest'] }}"
          - name: ssl_cert
            value: "{{ mariadb_server_ssl_map['server-cert']['dest'] }}"
          - name: ssl_key
            value: "{{ mariadb_server_ssl_map['server-key']['dest'] }}"
```

##### Configure replication

###### Master-slave

```yaml
- hosts: master
  roles:
    - mariadb-server
  vars:
    mariadb_server_users_present:
      - name: replicator
        password: 'replicator'
        privs:
          - '*.*:REPLICATION SLAVE'
        hosts:
          - '%'

    mariadb_server_etc_my_cnf:
      - section: mysqld
        options:
          - name: server_id
            value: 1
          - name: log_bin
            value: mysql-bin
          - name: log_bin_index
            value: mysql-bin.index
          - name: sync_binlog
            value: 1
          - name: report_host
            value: "{{ inventory_hostname }}"

- hosts: slave
  roles:
    - mariadb-server
  vars:
      - name: replicator
        password: 'replicator'
        privs:
          - '*.*:REPLICATION SLAVE'
        hosts:
          - '%'

    mariadb_server_etc_my_cnf:
      - section: mysqld
        options:
          - name: server_id
            value: 2
          - name: relay_log
            value: mysql-relay
          - name: relay_log_index
            value: mysql-relay.index
          - name: sync_relay_log
            value: 1
          - name: report_host
            value: "{{ inventory_hostname }}"

          - name: read_only
            value: 1
          - name: skip_slave_start
            value: 1
```

###### Master-master

```yaml
- hosts: master1
  roles:
    - mariadb-server
  vars:
    mariadb_server_users_present:
      - name: replicator
        password: 'replicator'
        privs:
          - '*.*:REPLICATION SLAVE'
        hosts:
          - '%'

    mariadb_server_etc_my_cnf:
      - section: mysqld
        options:
          - name: server_id
            value: 1
          - name: log_bin
            value: mysql-bin
          - name: log_bin_index
            value: mysql-bin.index
          - name: sync_binlog
            value: 1
          - name: relay_log
            value: mysql-relay
          - name: relay_log_index
            value: mysql-relay.index
          - name: sync_relay_log
            value: 1
          - name: report_host
            value: "{{ inventory_hostname }}"

          - name: skip_slave_start
            value: 1

- hosts: master2
  roles:
    - mariadb-server
  vars:
    mariadb_server_users_present:
      - name: replicator
        password: 'replicator'
        privs:
          - '*.*:REPLICATION SLAVE'
        hosts:
          - '%'

    mariadb_server_etc_my_cnf:
      - section: mysqld
        options:
          - name: server_id
            value: 2
          - name: log_bin
            value: mysql-bin
          - name: log_bin_index
            value: mysql-bin.index
          - name: sync_binlog
            value: 1
          - name: relay_log
            value: mysql-relay
          - name: relay_log_index
            value: mysql-relay.index
          - name: sync_relay_log
            value: 1
          - name: report_host
            value: "{{ inventory_hostname }}"

          - name: skip_slave_start
            value: 1
```

#### License

MIT

#### Author Information

* Mischa ter Smitten

#### Feedback, bug-reports, requests, ...

Are [welcome](https://github.com/Oefenweb/ansible-mariadb-server/issues)!
