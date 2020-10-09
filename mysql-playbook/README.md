# Mysql-Playbook
##### mysql 主从二进制安装



1、修改hosts

```yaml
#修改hosts，指定master与slave

[mysql]
192.168.20.92 mysql_role=master
192.168.20.93 mysql_role=slave
```

2、修改vars变量

```yaml
#mysql/vars/main.yml
#修改version，需与files目录下的mysql二进制解压后的目录一致
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



3、准备mysql二进制包

```yaml
将mysql二进制包解压到mysql/files文件夹下，并修改名称与vars变量中的version一致

如：
mysql/files/mysql5.7

cat mysql/vars/main.yml
version: mysql5.7
.....
```

