---
title: "2024.04.22 ansible 5일차(Chapter04) - Learning Ansible 2.7"
excerpt: "Ansible 사용법을 공부합니다."
layout: single
categories: [TIL]
tags: [Ansible, Automation, TIL, Today I Learned]
comments: false
toc: true
last_modified_at: 2024-04-22T04:38:00+09:00
---


# 2024.04.22 ansible 5일차 (Chapter04)

- 교재: Learning Ansible 2.7 - Third Edition [(packtpub)](https://www.packtpub.com/product/learning-ansible-27-third-edition/9781789954333) [(github)](https://github.com/PacktPublishing/Learning-Ansible-2.X-Third-Edition)
- ansible playbook 을 빠르게 설정하기: https://github.com/zasfe/Labs/blob/master/ansible/faster-ansible-playbook-execution.sh
- lab용 client container 설정하기
  * test01 - centos: https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client-test01-centos-container.sh
  * ws01,ws02,db01 - centos: https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_centos-container-3ea.sh
  * ws03 - debian: https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_debian-container.sh


**여기까지 했음**

  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/6/ch06lvl1sec34/working-with-conditionals
  * /home/opc/Learning-Ansible-2.X-Third-Edition-master/Chapter04



**한일**

* lab용 client container 설정 스크립트 작성
  * test01 - centos: https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client-test01-centos-container.sh
  * ws01,ws02,db01 - centos: https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_centos-container-3ea.sh
  * ws03 - debian: https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_debian-container.sh
* debian 컨테이너 빌드 및 docker hub 등록하도록 github action 설정 완료
  * repo: https://github.com/zasfe/docker-debian-systemd
  * container image: https://hub.docker.com/r/zasfe/debian
  * 참고: https://github.com/robertdebock/docker-debian-systemd
