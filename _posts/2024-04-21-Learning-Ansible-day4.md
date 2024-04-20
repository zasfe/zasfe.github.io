---
title: "2024.04.21 ansible 4일차 - Learning Ansible 2.7"
excerpt: "Ansible 사용법을 공부합니다."
layout: single
categories: [Computer]
tags: [Ansible,Automation]
comments: false
toc: true
last_modified_at: 2024-04-21T04:38:00+09:00
---


# 2024.04.21 ansible 4일차

- 교재: Learning Ansible 2.7 - Third Edition [(packtpub)](https://www.packtpub.com/product/learning-ansible-27-third-edition/9781789954333) [(github)](https://github.com/PacktPublishing/Learning-Ansible-2.X-Third-Edition)


**여기까지 했음**
  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/5/ch05lvl1sec28/working-with-variables
  * /home/opc/Learning-Ansible-2.X-Third-Edition-master/Chapter03


## collections 스크립트

* Docker
  * https://docs.ansible.com/ansible/latest/collections/community/docker/index.html#plugins-in-community-docker
* Amazon Web Services ( AWS )
  * https://docs.ansible.com/ansible/latest/collections/amazon/aws/index.html
  * https://docs.ansible.com/ansible/latest/collections/community/aws/index.html#plugins-in-community-aws
* Kubernetes
  * https://docs.ansible.com/ansible/latest/collections/kubernetes/index.html
* Openstack 
  * https://docs.ansible.com/ansible/latest/collections/openstack/index.html
  * https://docs.ansible.com/ansible/latest/collections/openstack/cloud/index.html#plugins-in-openstack-cloud
* Microsoft
  * https://docs.ansible.com/ansible/latest/collections/microsoft/index.html
  * https://docs.ansible.com/ansible/latest/collections/community/windows/index.html#plugins-in-community-windows





## Error

### Python Module not found: firewalld and its python module are required for this module

* 환경
  * OS: Oracle Linux Server release 7.9
  * APP: ansible 2.9.27(python version = 2.7.5)
* 증상: 방화벽 정책에서 http, https 정책 2개를 한꺼번에 설정하는 과정에 오류가 발생함
* 원인: firewalld 모듈이 없음
* 조치: python-firewall 설치
* 비고: Python 3.x 인 경우 python3-firewall 패키지 설치가 필요함
