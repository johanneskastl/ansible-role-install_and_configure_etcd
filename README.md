![Ansible Lint](https://github.com/johanneskastl/ansible-role-install_and_configure_etcd/workflows/Ansible%20Lint/badge.svg)

install_and_configure_etcd
=========

Install and configure etcd

Requirements
------------

None.

Role Variables
--------------

## General settings (in theory OS-dependent, but should be the same on most operating systems)

- `systemd_unit_path`: path used for systemd drop-ins (default: `/etc/systemd/system`)
- `systemd_unit_name`: name of the systemd service (default: `etcd.service`)
- `package_name`: name of the package to install (default: `etcd`)

## really OS-dependent settings

- `sysconfig_file_path`: path to the configuration file containing the environment variables (automatically set depending on the OS used)
- `etcd_data_dir`: etcd data directory (automatically set depending on the OS used)

## etcd v2 AP
- `enable_api_v2`: in case you need to enable the v2 API that was disabled in etcd 3.4, you can set this variable to 'true' and it will be re-enabled

## etcd settings with defaults

These variables have sensible defaults, but should most probably be overwritten by the user.

- `etcd_name`: Human-readable name for this member (default: `default`)
- `etcd_listen_peer_urls`: List of URLs to listen on for peer traffic (default: `http://localhost:2380`)
- `etcd_listen_client_urls`: List of URLs to listen on for client traffic (default: `http://localhost:2379`)
- `etcd_advertise_client_urls`: List of client URLs (of the actual node we are configuring) to advertise to the public (default: `http://localhost:2379`)

## etcd settings without a default

These settings are only used if the user has defined the variables.

- `etcd_initial_advertise_peer_urls`: List of peer URLs (of the actual node we are configuring) to advertise to the rest of the cluster (most probably something like `http://localhost:2380` or `http://192.168.1.2:2380`)
- `etcd_initial_cluster`: initial cluster configuration for bootstrapping, something like `default=http://localhost:2380`
- `etcd_initial_cluster_state`: initial cluster state ('new' or 'existing')
- `etcd_initial_cluster_token`: initial cluster token for the etcd cluster during bootstrap. Specifying this can protect you from unintended cross-cluster interaction when running multiple clusters

## Multi-node cluster

In case you want to setup a cluster of etcd servers, you can omit the variables `etcd_name` and `etcd_initial_cluster` and only the `etcd_servers_group`:

- `etcd_servers_group`: name of a Ansible group of hosts, which will be used as servers for etcd

The role automatically builds the long string, consisting of "hostname" plus "hosts's IP address plus port" for each of the servers, and uses this string as input for `etcd_initial_cluster`. In addition, if you specify `etcd_servers_group`, the variable `etcd_name ` will be set to each server's hostname.

Dependencies
------------

None

Example Playbook for a single server
------------------------------------

    - hosts: server1
      roles:
        - role: 'johanneskastl.install_and_configure_etcd'
        enable_api_v2: 'true'
        etcd_name: 'my_cluster'
        etcd_listen_peer_urls: "http://{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}:2380,http://127.0.0.1:7001"
        etcd_listen_client_urls: "http://{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}:2379,http://localhost:2379"
        etcd_initial_advertise_peer_urls: "http://{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}:2380"
        etcd_initial_cluster: "my_cluster=http://{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}:2380"
        etcd_advertise_client_urls: "http://{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}:2379"
        etcd_initial_cluster_state: 'new'
        etcd_initial_cluster_token: 'some_token'

License
-------

BSD-3-Clause

Author Information
------------------

I am Johannes Kastl, reachable via kastl@b1-systems.de.
