2024-04-26-Learning-Ansible-day6.md

---
title: "2024.04.26 ansible 7일차(Chapter04) - Learning Ansible 2.7"
excerpt: "Ansible 사용법을 공부합니다."
layout: single
categories: [TIL]
tags: [Ansible, Automation, TIL, Today I Learned]
comments: false
toc: true
last_modified_at: 2024-04-26T02:47:00+09:00
---


# 2024.04.23 ansible 6일차 (Chapter04)

- 교재: Learning Ansible 2.7 - Third Edition [(packtpub)](https://www.packtpub.com/product/learning-ansible-27-third-edition/9781789954333) [(github)](https://github.com/PacktPublishing/Learning-Ansible-2.X-Third-Edition)
- [(ansible playbook 빠른 실행)](https://github.com/zasfe/Labs/blob/master/ansible/faster-ansible-playbook-execution.sh)
- lab용 client container 설정하기 [(test01)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client-test01-centos-container.sh) [(centos ws01/ws02/db01)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_centos-container-3ea.sh) [(debian ws03)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_ws03-debian-container.sh)

## 여기까지 했음
  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/6/ch06lvl1sec34/working-with-conditionals
  * /home/opc/Learning-Ansible-2.X-Third-Edition-master/Chapter04

## 한일

**Gathering Facts 기반으로 when 연산자를 사용해서 실행여부를 설정한다.**

```yaml
..
    - name: Ensure the httpd package is updated 
      yum: 
        name: httpd 
        state: latest 
      become: True 
      when: ansible_os_family == 'RedHat' 
    - name: Ensure the apache2 package is updated 
      apt: 
        name: apache2 
        state: latest 
      become: True 
      when: ansible_os_family == 'Debian' 
..
```

**when 연산자는 변수도 설정할 수 있다. (파리미터: --extra-vars="변수내용")**

```yaml
--- 
- hosts: all 
  remote_user: vagrant
  vars: 
    backup: True 
  tasks: 
    - name: Copy the crontab in tmp if the backup variable is true 
      copy: 
        src: /etc/crontab 
        dest: /tmp/crontab 
        remote_src: True 
      when: backup 
```

**변수를 사용할때는 반드시 선언되어 있는지 확인해야 한다. (예시. when: 변수 is not defined or 변수 == "")**

```yaml
--- 
- hosts: all 
  remote_user: ansible 
  vars: 
    backup: True 
  tasks: 
    - name: Check if the backup_folder is set 
      fail: 
        msg: 'The backup_folder needs to be set' 
      when: backup_folder is not defined or backup_folder == “” 
    - name: Copy the crontab in tmp if the backup variable is true 
      copy: 
        src: /etc/crontab 
        dest: '{{ backup_folder }}/crontab' 
        remote_src: True 
      when: backup 
```

**다른 코드를 재사용해서 유지관리하기 쉬운 코드를 작성할 수 있음.**

```yaml
- inclode: FILENAME.yaml
- inclode: FILENAME.yaml variable1="value1" variable2="value2"
```

### 핸들러 작업

* 복잡한 배포 처리 | 4/26 구간 완료 | 15% | [(link)](https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/6/ch06lvl1sec36/working-with-handlers)

- notify 에 해당하면 핸들러 작업은 플레이북 끝에서 실행됩니다.
  - why? 구성을 변경할때 마다 재시작하는 것이 아니라 미자막에만 1번 재시작도록 설정한다 


### 프로젝트 구성

[(link)](https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/6/ch06lvl1sec38/organizing-a-project)

```
    ├── ansible.cfg        : 폴더 구조에서 파일을 찾을 수 있는 위치를 Ansible에 설명하는 작은 구성 파일
    ├── hosts              : 이전 장에서 이미 본 호스트 파일
    ├── master.yaml        : 전체 인프라를 정렬하는 플레이북
    ├── playbooks          : 플레이북과 그룹 ​​관리용 폴더가 포함됩니다. 한번만 실행함
    │   ├── firstrun.yaml
    │   └── groups
    │       ├── database.yaml
    │       └── webserver.yaml
    └── roles               : 여기에는 필요한 모든 역할이 포함됩니다.
        ├── common          : 모든 서버에 대해 수행되어야 하는 모든 작업
        │   └── tasks       : roles의 필수 폴더
        |       └── main.yaml : 실행할 작업 목록이 될 파일
        │   └── templates   : 템플릿을 저장
        |       └── motd    : 사용되는 파일을 저장
        ├── database
        │   └── tasks
        |       └── main.yaml
        └── webserver

```

## 여기까지 했음
  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/6/ch06lvl1sec41/transforming-a-playbook-into-a-role
  * /home/opc/Learning-Ansible-2.X-Third-Edition-master/Chapter04

