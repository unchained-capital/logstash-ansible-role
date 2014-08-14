Role Name
=========

Ansible Role to Install and Configure Logstash

Requirements
------------

**Java** should be present on the nodes machines in order to run Logstash. On Debian OS family, **python-pycurl** and **python-apt** are required to deal with apt Ansible modules. The role already take care of these dependencies, however you can disable this behavior with `--skip-tags=logstash_req`.

Example Playbooks
----------------

```yaml
---
- hosts: LogstashNodesWithAFilter
  roles:
   - { role: logstash-role,
             logstash_version: "1.4",

             logstash_defaults:
             [{ directive: "LS_USER=root" }],

             logstash_inputs:
             { file: { path: '[ "/home/dummy/logs/access.log" ]', type: '"nginx_logs"', tags: '"nginx_logs"' }},
 
             logstash_filters:
             { geoip: { source: '"remote_addr"' },
               metrics: { meter: '[ "http.%{response}" ]', add_tag: '"nginx_metrics"' }},

             logstash_outputs:
             {  file: { path: '"/var/log/logstash/logstash_processed.log"' }},

             tags: ["logstash"] }

- hosts: DummyLogstashNodes
  roles:
   - { role: valentinogagliardi.logstash-role,
             logstash_version: "1.4",

             logstash_defaults:
             [{ directive: "LS_USER=root" }],

             logstash_inputs:
             { file: { path: '[ "/var/log/auth.log" ]', type: '"auth_logs"' }},

             logstash_outputs:
             {  file: { path: '"/var/log/logstash/%{type}.log"' }},

             tags: ["logstash"] }

- hosts: ComplicatedLogstashNodes
  roles:
   - { role: valentinogagliardi.logstash-role,
             logstash_version: "1.4",

             logstash_defaults:
             [{ directive: "LS_USER=root" }],

             logstash_inputs:
             { file: { path: '[ "/var/log/syslog", "/var/log/messages" ]', exclude: '"*.gz"', type: '"linux_logs"' },
               syslog: { port: '"514"', host: '"1.2.3.4"', type: '"syslog"' },
               redis: { host: '127.0.0.1', port: '"6379"', type: '"redis"' }},

             logstash_outputs:
             { elasticsearch: { host: '"1.2.3.5"' },
               email: { body: 'Error Log!', from: 'logger@mydomain.com', subject: 'Alert!', to: 'pager@myadmin.com' },
               file: { path: '"/var/log/logstash/logs_%{type}.log"', flush_interval: '"50"' }},
             tags: ["logstash"] }

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

