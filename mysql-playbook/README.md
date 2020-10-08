# Ansible
#### 1、修改hosts

```yaml
#修改hosts，指定master与slave

[mysql]
192.168.20.92 mysql_role=master
192.168.20.93 mysql_role=slave
```

#### 2、修改vars变量

```yaml
#mysql/vars/main.yml
#master_ip变量必须修改
---
# vars file for mysql
mysql_group: mysql
mysql_user: mysql
version: mysql5.7
destdir: /web/soft
basedir: '{{ destdir }}/{{ version }}'
datadir: /web/data/mysql_data
logsdir: '{{ basedir }}/logs'
mysqlsock: '{{ basedir }}/mysql.sock'
root_pass: 123abc!
repl_user: repl
repl_pass: repl@00..
master_ip: 192.168.20.92
```

