Role Name
=========

Ansible Role to Install and Configure Logstash

Requirements
------------

**Java** should be present on the nodes machines in order to run Logstash. On Debian OS family, **python-pycurl** and **python-apt** are required to deal with apt Ansible modules. The role already take care of these dependencies, however you can disable this behavior with `--skip-tags=logstash_req`.

Example Playbooks
----------------

```yaml
- hosts: LogstashNodes
  roles:
    - role: valentinogagliardi.logstash-role
      logstash_version: "1.4"

      logstash_defaults:
        - directive: "LS_USER=root"
        - directive: 'LS_HEAP_SIZE="256m"'

      logstash_inputs:
        syslog: >-
          host => "{{ ansible_eth1.ipv4.address }}"
             port => "514"
             type => "from_ossec_syslog"
        collectd: >-
          host => "{{ ansible_eth1.ipv4.address }}"
             port => "25887"

      logstash_filters:
        geoip: >-
          source => "ip_address"

      logstash_outputs:
        file: >-
          path => "/var/log/logstash/output.log"
        redis: >-
          host => "10.8.8.22"
      tags: logstash
```

Role Variables
--------------

```yaml
logstash_python_utils:
 - { package: "python-pycurl" }
 - { package: "python-apt" }

open_jdk_rh: "java-1.7.0-openjdk"
open_jdk_deb: "openjdk-7-jre"

logstash_version: "none"

logstash_apt_repo: "deb http://packages.elasticsearch.org/logstash/{{ logstash_version }}/debian stable main"
logstash_repo_key: "http://packages.elasticsearch.org/GPG-KEY-elasticsearch"
logstash_yum_repo_dest: "/etc/yum.repos.d/logstash.repo"

logstash_packages:
 - { package: "logstash" }
 - { package: "logstash-contrib" }

logstash_conf_dir: "/etc/logstash/conf.d/"

logstash_defaults: 
 - { directive: "LS_USER=logstash" }

defaults_RedHat: "/etc/sysconfig/logstash"
defaults_Debian: "/etc/default/logstash"
```

License
-------

GNU General Public License Version 2

Author Information
------------------

Valentino Gagliardi - valentino.g@servermanaged.it

