---
title: "2024.04.23 ansible 6일차(Chapter04) - Learning Ansible 2.7"
excerpt: "Ansible 사용법을 공부합니다."
layout: single
categories: [TIL]
tags: [Ansible, Automation, TIL, Today I Learned]
comments: false
toc: true
last_modified_at: 2024-04-23T02:47:00+09:00
---


# 2024.04.23 ansible 6일차 (Chapter04)

- 교재: Learning Ansible 2.7 - Third Edition [(packtpub)](https://www.packtpub.com/product/learning-ansible-27-third-edition/9781789954333) [(github)](https://github.com/PacktPublishing/Learning-Ansible-2.X-Third-Edition)
- ansible playbook 빠른 실행 설정: https://github.com/zasfe/Labs/blob/master/ansible/faster-ansible-playbook-execution.sh


**lab용 client container 설정하기**

```bash
#!/bin/bash
{% raw %}
# create centos container - test01 
docker run -d --privileged --name test01 zasfe/centos:7-systemd-vagrant /usr/sbin/init
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" test01` test01.fale.io" | sudo tee -a /etc/hosts
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@test01.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@`docker inspect -f "\{\{ .NetworkSettings.IPAddress \}\}" test01`
{% endraw %}

# create centos container - ws01, ws02, db01 
docker run -d --privileged --name ws01 zasfe/centos:7-systemd-vagrant /usr/sbin/init
docker run -d --privileged --name ws02 zasfe/centos:7-systemd-vagrant /usr/sbin/init
docker run -d --privileged --name db01 zasfe/centos:7-systemd-vagrant /usr/sbin/init

echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ws01` ws01.fale.io" | sudo tee -a /etc/hosts
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ws02` ws02.fale.io" | sudo tee -a /etc/hosts
echo "`docker inspect -f "{{ .NetworkSettings.IPAddress }}" db01` db01.fale.io" | sudo tee -a /etc/hosts

sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws01.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@ws02.fale.io
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@db01.fale.io

sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ws01`
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@`docker inspect -f "{{ .NetworkSettings.IPAddress }}" ws02`
sshpass -p vagrant ssh-copy-id -f -i ~/.ssh/id_rsa -o StrictHostKeyChecking=no vagrant@`docker inspect -f "{{ .NetworkSettings.IPAddress }}" db01`
```

## 여기까지 했음
  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/6/ch06lvl1sec34/working-with-conditionals
  * /home/opc/Learning-Ansible-2.X-Third-Edition-master/Chapter04

## 한일
  * CentOS 와 CentOS vagrant 컨테이너 빌드 및 docker hub 등록하도록 github action 설정 완료
    * repo: https://github.com/zasfe/docker-centos-systemd
    * container image: https://hub.docker.com/r/zasfe/centos{7:7-systemd-vagrant:8}
    * 참고: https://github.com/robertdebock/docker-centos-systemd
