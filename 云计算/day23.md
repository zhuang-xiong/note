# day23

### Ansible

* 介绍

ansible是新出现的自动化运维工具，由python开发，集合了众多自动化运维工具的优点，实现了批量 系统部署、批量程序部署，批量运行命令等功能。ansible是基于模块工作的，本身没有批量部署的能 力，真正具有批量部署能力的是ansible运行的模块，ansible只是提供一个框架。

***

#### playbook

* apache官方案例

```
```



* nginx官方案例

```
1.创建   nginx.yaml 文件
- hosts: 192.168.241.20(想要访问的服务器的IP)
  remote_user: root
  vars:
    hello: Ansible
  tasks:
    - name: yum install epel-release-noarch -y(命名无所谓)
      yum:
        name: epel-release.noarch
        state: latest

    - name: Install nginx
      yum:
        name: nginx
        state: present
    - name: copy nginx configure file
      copy:
        src: /root/site.conf(这里是nginx的配置文件内容，不过是写在ansible服务器上)
        dest: /etc/nginx/conf.d/site.conf(这是目的地址，nginx服务器的配置文件)
    - name: start nginx
      service:
        name: nginx
        state: restarted
    - name: create html
      shell: echo "site" > /usr/share/nginx/html/index.html(默认首页界面)


2.本地书写配置文件
server {
listen 8080;(开放端口号)
server_name 192.168.241.20:8080;(nginx客户端的IP)
location / {
index index.html;(默认界面)
}
}

3.检查语法错误并且运行
[root@eagle ~]# ansible-playbook nginx.yaml --syntax-check
[root@eagle ~]# ansible-playbook nginx.yaml
```

