### Yum



### Copy



### Get_url

+ 下载互联网的软件到本地

  ```shell
  #url支持http https ftp
  ansible oldboy -m get_url -a "url=http://fj.xuliangwei.com/public/ip.txt dest=/var/www/html/"  -i hosts
  ```

+ 下载文件并校验md5值

  ```shell
  ansible oldboy -m get_url -a "url=http://fj.xuliangwei.com/public/ip.txt dest=/var/www/html/ checksum=md5:xxxx"  -i hosts
  ```

  

### File

```shell
#参数
path
state
	touch
	directory
recurse
owner group mode
```



+  创建文件，并设置属主、属组、权限。

  ```shell
  ansible oldboy -m file -a "path=/var/www/html/tt.html state=touch owner=apache group=apache mode=644"  -i hosts
  ```

+ 创建目录，并设置属主、属组、权限。

  ```shell
  ansible oldboy -m file -a "path=/var/www/html/dd state=directory  owner=apache group=apache mode=644"  -i hosts
  ```

+ 创建目录，并设置属主、属组、权限。

  ```shell
  ansible oldboy -m file -a "path=/var/www/html/ state=directory  owner=apache group=apache mode=644  recurse=yes"  -i hosts
  ```



### Service

```shell
#参数
name
state
enable
```



### Group

```txt
#参数
name
gid
state
```

+ 创建news基本组，指定gid为9999

  ```shell
  ansible oldboy -m group -a "name=news gid=9999 state=present"  -i hosts
  ```

+ 创建http系统组，指定gid为8888

  ```shell
  ansible oldboy -m group -a "name=news gid=8888 system=yes state=present"  -i hosts
  ```

+ 删除news基本组

  ```shell
  ansible oldboy -m group -a "name=news  state=absent"  -i hosts
  ```

  

### User

```
#参数
name
state
shell
create_home
remove
```

+ 创建joh用户，uid1040，主要的组为adm

  ```
  ansible oldboy  -m user -a 'name=joh uid=1040 group=adm' -i hosts
  ```

+ 创建joh用户，登录shell为/sbin/nologin，追加组为bin,sys两个组

  ```
  ansible oldboy  -m user -a 'name=joh shell=/sbin/nologin groups=bin,sys' -i hosts 
  ```

+ 创建jsm用户，设置密码为123，并创建家目录

  ```
  #生成密码
  ansible oldboy -m debug -a "msg={{ '123' | password_hash('sha512','salt')}}" -i hosts
  #$6$salt$jkHSO0tOjmLW0S1NFlw5veSIDRAVsiQQMTrkOKy4xdCCLPNIsHhZkIRlzfzIvKyXeGdOfCBoW1wJZPLyQ9Qx/1
  
  #创建
  ansible oldboy -m user -a 'name=jsm password=$6$salt$jkHSO0tOjmLW0S1NFlw5veSIDRAVsiQQMTrkOKy4xdCCLPNIsHhZkIRlzfzIvKyXeGdOfCBoW1wJZPLyQ9Qx/1  create_home=yes'   -i hosts
  ```

+ 移除joh用户

  ```
  ansible oldboy  -m user -a 'name=joh state=absent remove=yes'  -i hosts 
  ```



### Cron

+ 添加定时任务，每分钟执行一次 ls > /dev/null

  ```
  ansible oldboy -m cron -a 'name=job1 job="ls > /dev/null"' -i hosts
  ```

+ 添加定时任务，每天凌晨2点5点执行一次 ls > /dev/null

  ```
  ansible oldboy -m cron -a 'name=job1 minute=0 hour=5,2 job="ls > /dev/null"' -i hosts
  ```

+ 关闭定时任务

  ```
  ansible oldboy -m cron -a 'name=job1 minute=0 hour=5,2 job="ls > /dev/null" disable=yes' -i hosts
  ```



### Firewalld

+ 永久放行https流量，只有重启才生效

  ```
  ansible oldboy -m firewalld -a 'zone=public service=https permanent=yes state=enabled' -i hosts
  ```

+ 永久放行8080-9090，临时和永久都生效

  ```
  ansible oldboy -m firewalld -a 'zone=public port=8080-8090/tcp permanent=yes immediate=yes state=enabled ' -i hosts
  ```

  

### when



### With_items

```

```



### Handlers

```yaml
#示例nginx.yaml
- hosts: mysql
  tasks:
    - name: copy Nginx conf
      copy: 
        src: ./nginx.conf
        dest: /etc/nginx/conf/nginx.conf
      notify: 
        - service nginx restart
  handlers:
    - name: service nginx restart
      service: name=nginx state=restarted
```



### Tags

```yaml
#示例test.yaml
- hosts: mysql
  tasks:
    - name: Install Nginx
      yum: name=nignx state=present
      tags: test_install
     
#指定执行某个tags标签
ansible-playbook -i hosts  test.yaml -t "test_install"

#跳过指定执行某个tags标签
ansible-playbook -i hosts  test.yaml --skip-tags "test_install"
```



### Ignore_errors(忽略错误)

```yaml
#示例test.yaml
- hosts: mysql
  tasks:
    - name: Install Nginx
      yum: name=nignx state=present
      ignore_errors: yes
    - name: Create File
      file: path=/tmp/ttt state=touch
```





### Change_when

```

```



### Httpd.yaml

```yaml
- hosts: oldboy
  tasks:
    - name: Installed Httpd Server
      yum:
        name=httpd
        state=present
    - name: Configure Httpd Server
      copy:
        src=./httpd.conf.j2
        dest=/etc/httpd/conf/httpd.conf
        backup=yes
    - name: Configure Httpd Website
      copy:
        src=./tt.j2
        dest=/var/www/html/tt.html
        mode=644
    - name: Service httpd server
      service:
        name=httpd
        state=started
        enabled=yes
    - name: Service Firewalld server
      service:
        name=firewalld
        state=started
    - name: configure firewalld service
      firewalld:
        zone=public
        service=http
        permanent=yes
        immediate=yes
        state=enabled
```

