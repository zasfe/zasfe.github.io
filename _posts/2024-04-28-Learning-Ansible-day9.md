---
title: "2024.04.28 ansible 9일차(Chapter08) - Learning Ansible 2.7"
excerpt: "Ansible 사용법을 공부합니다."
layout: single
categories: [TIL]
tags: [Ansible, Automation, TIL, Today I Learned]
comments: false
toc: true
last_modified_at: 2024-04-28T02:47:00+09:00
---


# 2024.04.28 ansible 9일차 (Chapter04)

- 교재: Learning Ansible 2.7 - Third Edition [(packtpub)](https://www.packtpub.com/product/learning-ansible-27-third-edition/9781789954333) [(github)](https://github.com/PacktPublishing/Learning-Ansible-2.X-Third-Edition)
- [(ansible playbook 빠른 실행)](https://github.com/zasfe/Labs/blob/master/ansible/faster-ansible-playbook-execution.sh)
- lab용 client container 설정하기 [(test01)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client-test01-centos-container.sh) [(centos ws01/ws02/db01)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_centos-container-3ea.sh) [(debian ws03)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_ws03-debian-container.sh)

## 정리

* 핸들러(roles/이름/handlers) 설정을 하면 "이름" 작업 후 실행한다.
* import_playbook 로 실행할 playbook 순서를 지정할 수 있다.(실패하면 이후 단계 실행 안됨)
* ansible-valut은 비밀번호로 코드를 암호화할 수 있음.(비밀번호는 변경가능) 
* ansible 모듈 위치: /usr/lib/python2.7/site-packages/ansible/modules
* --syntax-check : 문법에 맞는 파일인지 체크
* --diff : playbook 파일 설정 변경 사항을 표시함, --check 와 함께 사용 (+는 추가, -는 삭제)
* --list-tasks: playbook 파일의 작업 목록을 표시(제목, 태그)
* -t : playbook 파일 중 특정 태그만 실행
* --skip-tags : playbook 파일 중 특정 태그를 제외하고 실행


## 여기부터 시작
  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/11/ch11lvl1sec86/functional-testing-in-ansible
    * Chapter 8, Debugging and Error Handling
    * Chapter 9, Complex Environments
  * `cd ./Learning-Ansible-2.X-Third-Edition-master/Chapter08`

## 한일

```
├── ansible.cfg
├── hosts
├── playbooks
│   ├── assert_ls.yaml
│   ├── hosts
│   │   └── ws01.yaml
│   ├── maven_build.yaml
│   ├── setup_and_config_apache_test.yaml
│   ├── setup_and_config_apache.yaml
│   ├── setup_apache_test.yaml
│   ├── setup_apache.yaml
│   └── tags_example.yaml
├── roles
│   └── java
│       └── tasks
│           └── main.yaml
├── templates
│   └── userdir.conf
└── Vagrantfile


# ansible.cfg
[defaults]
inventory = hosts 
host_key_checking = False 
roles_path = roles 
remote_user=ansible

# hosts 
ws01


# laybooks/tags_example.yaml
- hosts: all
  tasks: 
    - name: Ensure the file /tmp/ok exists 
      file: 
        name: /tmp/ok 
        state: touch 
      tags: 
        - file_present 
    - name: Ensure the file /tmp/ok does not exists 
      file: 
        name: /tmp/ok 
        state: absent 
      tags: 
        - file_absent

ansible-playbook -u ansible -i ws01, playbooks/tags_example.yaml --list-tasks
ansible-playbook -u ansible -i ws01, playbooks/tags_example.yaml -t file_present
ansible-playbook -u ansible -i ws01, playbooks/tags_example.yaml --skip-tags file_present  


# playbooks/hosts/ws01.yaml
- hosts: ws01
  roles: 
    - java 

ansible-playbook playbooks/hosts/ws01.yaml

# playbooks/maven_build.yaml 
- hosts: all
  tasks: 
    - name: Ensure the tag variable is properly set
      fail: 'The version needs to be defined. To do so, please add: --extra-vars "version=$[TAG/BRANCH]"' 
      when: version is not defined 
    - name: Get last Project version 
      git: 
        repo: https://github.com/org/project.git 
        dest: "/tmp" 
        version: '{{ version }}' 
    - name: Maven clean install 
      shell: "cd /tmp/project && mvn clean install" 
```


```
https://github.com/PacktPublishing/Learning-Ansible-2.X-Third-Edition/tree/master/Chapter09
cd ./Learning-Ansible-2.X-Third-Edition-master/Chapter09

Development -> Testing -> Stage -> Production 

├── ansible.cfg
├── inventory
│   ├── group_vars
│   │   ├── all
│   │   ├── production
│   │   └── staging
│   ├── production
│   └── staging
├── playbooks
│   ├── groups
│   │   ├── builders.yaml
│   │   └── web.yaml
│   ├── manual
│   │   ├── hello_deploy.yaml
│   │   ├── rcs_deploy.yaml
│   │   └── rpm_deploy.yaml
│   └── variables.yaml
├── roles
│   ├── builder
│   │   ├── files
│   │   │   └── repo.conf
│   │   ├── handlers
│   │   │   └── main.yaml
│   │   └── tasks
│   │       └── main.yaml
│   ├── common
│   │   ├── tasks
│   │   │   └── main.yaml
│   │   └── templates
│   │       └── motd
│   └── webserver
│       ├── files
│       │   ├── privaterepo.repo
│       │   └── website.conf
│       ├── handlers
│       │   └── main.yaml
│       ├── tasks
│       │   └── main.yaml
│       └── templates
│           └── index.html.j2
├── spec
│   ├── demo-php-app.spec
│   └── hello-worls.spec
└── Vagrantfile

#!/bin/bash

# create centos containers
docker run -d --privileged --name ws01 zasfe/centos:7-systemd-vagrant /usr/sbin/init
docker run -d --privileged --name ws02 zasfe/centos:7-systemd-vagrant /usr/sbin/init
docker run -d --privileged --name db01 zasfe/centos:7-systemd-vagrant /usr/sbin/init
docker run -d --privileged --name ws01s zasfe/centos:7-systemd-vagrant /usr/sbin/init
docker run -d --privileged --name ws02s zasfe/centos:7-systemd-vagrant /usr/sbin/init
docker run -d --privileged --name db01s zasfe/centos:7-systemd-vagrant /usr/sbin/init
docker run -d --privileged --name builder01 zasfe/centos:7-systemd-vagrant /usr/sbin/init

echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ws01` ws01.fale.io ws01" | sudo tee -a /etc/hosts
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ws02` ws02.fale.io ws02" | sudo tee -a /etc/hosts
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" db01` db01.fale.io db01" | sudo tee -a /etc/hosts
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ws01s` ws01.staging.fale.io ws01s" | sudo tee -a /etc/hosts
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ws02s` ws02.staging.fale.io ws02s" | sudo tee -a /etc/hosts
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" db01s` db01.staging.fale.io db01s" | sudo tee -a /etc/hosts
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" builder01` builder01.fale.io builder01" | sudo tee -a /etc/hosts

ssh-keygen -R ws01.fale.io
ssh-keygen -R ws02.fale.io
ssh-keygen -R db01.fale.io
ssh-keygen -R ws01s.fale.io
ssh-keygen -R ws02s.fale.io
ssh-keygen -R db01s.fale.io
ssh-keygen -R builder01.fale.io

docker exec -u 0 ws01 /bin/bash -c "rm -f /run/nologin"
docker exec -u 0 ws02 /bin/bash -c "rm -f /run/nologin"
docker exec -u 0 db01 /bin/bash -c "rm -f /run/nologin"
docker exec -u 0 ws01s /bin/bash -c "rm -f /run/nologin"
docker exec -u 0 ws02s /bin/bash -c "rm -f /run/nologin"
docker exec -u 0 db01s /bin/bash -c "rm -f /run/nologin"
docker exec -u 0 builder01 /bin/bash -c "rm -f /run/nologin"

docker exec -u 0 ws01 /bin/bash -c "yum install -y epel-release; yum update -y; yum install -y iproute net-tools openssh-server sudo firwalld python-firewal; yum clean all;"
docker exec -u 0 ws02 /bin/bash -c "yum install -y epel-release; yum update -y; yum install -y iproute net-tools openssh-server sudo firwalld python-firewal; yum clean all;"
docker exec -u 0 db01 /bin/bash -c "yum install -y epel-release; yum update -y; yum install -y iproute net-tools openssh-server sudo firwalld python-firewal; yum clean all;"
docker exec -u 0 ws01s /bin/bash -c "yum install -y epel-release; yum update -y; yum install -y iproute net-tools openssh-server sudo firwalld python-firewal; yum clean all;"
docker exec -u 0 ws02s /bin/bash -c "yum install -y epel-release; yum update -y; yum install -y iproute net-tools openssh-server sudo firwalld python-firewal; yum clean all;"
docker exec -u 0 db01s /bin/bash -c "yum install -y epel-release; yum update -y; yum install -y iproute net-tools openssh-server sudo firwalld python-firewal; yum clean all;"
docker exec -u 0 builder01 /bin/bash -c "yum install -y epel-release; yum update -y; yum install -y iproute net-tools openssh-server sudo firwalld python-firewal; yum clean all;"


sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws01.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws02.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@db01.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws01.staging.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws02.staging.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@db01.staging.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@builder01.fale.io

sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws01
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws02
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@db01
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws01s
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws02s
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@db01s
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@builder01

# ansible.cfg 
[defaults] 
inventory = hosts 
host_key_checking = False 
roles_path = roles 

# ./playbooks/variables.yaml
- hosts: web 
  user: vagrant 
  tasks: 
    - name: Print environment name 
      debug: 
        var: env 
    - name: Print db server url 
      debug: 
        var: db_url 
    - name: Print domain url 
      debug: 
        var: domain 
- hosts: db 
  user: vagrant 
  tasks: 
    - name: Print environment name 
      debug: 
        var: env 
    - name: Print database username 
      debug: 
        var: db_user 
    - name: Print database password 
      debug: 
        var: db_pass 

# ./inventory/production 
[web] 
ws01.fale.io 
ws02.fale.io 
 
[db] 
db01.fale.io 


[builders] 
builder01.fale.io 
 
[production:children] 
db 
web 
builders

# ./inventory/staging 
[web] 
ws01.staging.fale.io 
ws02.staging.fale.io 
 
[db] 
db01.staging.fale.io 
 
[staging:children] 
db 
web 

# ./inventory/group_vars/production 
env: production 
domain: fale.io 
db_url: db.fale.io 
db_pass: this_is_a_safe_password 


ansible-playbook -i inventory/staging playbooks/variables.yaml
ansible-playbook -i inventory/production playbooks/variables.yaml  


# playbooks/groups/web.yaml 
- hosts: web 
  user: vagrant
  roles: 
    - common 
    - webserver 

# roles/common/tasks/main.yaml 
---
- name: Ensure EPEL is enabled 
  yum: 
    name: epel-release 
    state: present 
  become: True 
- name: Ensure libselinux-python is present 
  yum: 
    name: libselinux-python 
    state: present 
  become: True 
- name: Ensure libsemanage-python is present 
  yum: 
    name: libsemanage-python 
    state: present 
  become: True 
..

# roles/webserver/tasks/main.yaml
--- 
- name: Ensure the HTTPd package is installed 
  yum: 
    name: httpd 
    state: present 
  become: True 
- name: Ensure the HTTPd service is enabled and running 
  service: 
    name: httpd 
    state: started 
    enabled: True 
  become: True 
- name: Ensure HTTPd configuration is updated 
  copy: 
    src: website.conf 
    dest: /etc/httpd/conf.d 
  become: True 
  notify: Restart HTTPd 
- name: Ensure the website is present and updated 
  template: 
    src: index.html.j2 
    dest: /var/www/html/index.html 
    owner: root 
    group: root 
    mode: 0644 
  become: True 
- name: Install our private repository
  copy:
    src: privaterepo.repo
    dest: /etc/yum.repos.d/privaterepo.repo
  become: True

# roles/webserver/handlers/main.yaml
--- 
- name: Restart HTTPd 
  service: 
    name: httpd 
    state: restarted 
  become: True 


ansible-playbook -i inventory/production playbooks/groups/web.yaml  


```



## 여기까지 했음
  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/12/ch12lvl1sec96/deploying-a-web-app-with-a-revision-control-system
    * Chapter 9, Complex Environments
  * ./Learning-Ansible-2.X-Third-Edition-master/Chapter09

## 문제해결

