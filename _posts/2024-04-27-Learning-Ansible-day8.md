---
title: "2024.04.27 ansible 8일차(Chapter04 ~ 08) - Learning Ansible 2.7"
excerpt: "Ansible 사용법을 공부합니다."
layout: single
categories: [TIL]
tags: [Ansible, Automation, TIL, Today I Learned]
comments: false
toc: true
last_modified_at: 2024-04-27T02:47:00+09:00
---


# 2024.04.27 ansible 8일차 (Chapter04 ~ 08)

- 교재: Learning Ansible 2.7 - Third Edition [(packtpub)](https://www.packtpub.com/product/learning-ansible-27-third-edition/9781789954333) [(github)](https://github.com/PacktPublishing/Learning-Ansible-2.X-Third-Edition)
- [(ansible playbook 빠른 실행)](https://github.com/zasfe/Labs/blob/master/ansible/faster-ansible-playbook-execution.sh)
- lab용 client container 설정하기 [(test01)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client-test01-centos-container.sh) [(centos ws01/ws02/db01)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_centos-container-3ea.sh) [(debian ws03)](https://github.com/zasfe/Labs/blob/master/ansible/lab_ansible_client_ws03-debian-container.sh)

## 정리

* 핸들러(roles/이름/handlers) 설정을 하면 "이름" 작업 후 실행한다.
* import_playbook 로 실행할 playbook 순서를 지정할 수 있다.(실패하면 이후 단계 실행 안됨)
* ansible-valut은 비밀번호로 코드를 암호화할 수 있음.(비밀번호는 변경가능) 
* ansible 모듈 위치: /usr/lib/python2.7/site-packages/ansible/modules
* --syntax-check : 문법에 맞는 파일인지 체크
* --check : 실제 설치 등 변경을 하지 않고 예상되는 결과를 보여줌 
* --diff : playbook 파일 설정 변경 사항을 표시함, --check 와 함께 사용 (+는 추가, -는 삭제)




## 여기부터 시작
  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/6/ch06lvl1sec41/transforming-a-playbook-into-a-role
  * ./Learning-Ansible-2.X-Third-Edition-master/Chapter04

## 한일



```
    ├── ansible.cfg        : 폴더 구조에서 파일을 찾을 수 있는 위치를 Ansible에 설명하는 작은 구성 파일
    ├── hosts              : 이전 장에서 이미 본 호스트 파일
    ├── master.yaml        : 전체 인프라를 정렬하는 플레이북(import_playbook)
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

**import_playbook - 실행해야할 파일이 많은 경우 순서를 지정할 수 있다.**

* 문법: 순서대로 %%import_playbook: playbook yaml 파일 경로%%
* 주의
  * import_playbook 작업이 실패하면 다음 import_playbook 은 실행되지 않는다.
 
**사용 예시**
```bash
[opc@instance-20220112-0945 Chapter04]$ cat master.yaml 
--- 
- import_playbook: playbooks/groups/database.yaml 
- import_playbook: playbooks/groups/webserver.yaml 
[opc@instance-20220112-0945 Chapter04]$ cat playbooks/groups/database.yaml
--- 
- hosts: database 
  user: vagrant 
  roles: 
  - common 
[opc@instance-20220112-0945 Chapter04]$ cat playbooks/groups/webserver.yaml

--- 
- hosts: webserver 
  user: vagrant 
  roles: 
  - common 
  - webserver
[opc@instance-20220112-0945 Chapter04]$ ansible-playbook master.yaml 

PLAY [database] ************************************************************************************
TASK [Gathering Facts] *****************************************************************************ok: [db01.fale.io]
..
```


### ansible 보안관리

* Ansible Vault: 코드 일부를 비밀번호로 암호화

```bash
[opc@instance-20220112-0945 Chapter04]$ echo 'ansible' > .password 
[opc@instance-20220112-0945 Chapter04]$ ansible-vault create secret.yaml
New Vault password: 
Confirm New Vault password: 
=> test 입력
[opc@instance-20220112-0945 Chapter04]$ cat ./secret.yaml
$ANSIBLE_VAULT;1.1;AES256
66653838313932613231346665653662343334396361343333346332623733313533333238323062
3435373332626463393837363239663530653161663562320a356666623932356461316339643238
61626334333964616131663832303133376233356637373836306666383832326362653039653766
6130313033653738310a336631363037666130383236333963346565623362616337336161376261
6463
[opc@instance-20220112-0945 Chapter04]$
[opc@instance-20220112-0945 Chapter04]$ ansible-vault view --vault-password-file .password secret.yaml
test
[opc@instance-20220112-0945 Chapter04]$ ansible-vault decrypt --vault-password-file .password secret.yaml
Decryption successful
[opc@instance-20220112-0945 Chapter04]$ cat ./secret.yaml
test
[opc@instance-20220112-0945 Chapter04]$ 
[opc@instance-20220112-0945 Chapter04]$ ansible-vault encrypt --vault-password-file .password secret.yaml
Encryption successful
[opc@instance-20220112-0945 Chapter04]$ cat ./secret.yaml
$ANSIBLE_VAULT;1.1;AES256
61626637663335646664373532363435316436653335626431306437653965363338363536653938
3835666635316338393632393534666335353464666466360a343762393464303138363037343935
32306637653834313631393262613363613830316462336432333434313830643634613562323131
3131343563343436620a663761323733623664336463376439383264383535653862373439623639
6433
[opc@instance-20220112-0945 Chapter04]$ 
[opc@instance-20220112-0945 Chapter04]$ echo 'ansible2' > .newpassword 
[opc@instance-20220112-0945 Chapter04]$ ansible-vault rekey --vault-password-file .password  --new-vault-password-file .newpassword secret.yaml
Rekey successful
[opc@instance-20220112-0945 Chapter04]$ ansible-vault view --vault-password-file .password secret.yaml^C
[opc@instance-20220112-0945 Chapter04]$ cat ./secret.yaml
$ANSIBLE_VAULT;1.1;AES256
32343136353563313765623162613138373464383938303438313663633130323163313337616466
3564343265366666613030336164666261653333396334340a373937383430373631386238373764
61643132323366623736313566306134653635633139656539313832646663376239356463356436
3834633234333430320a636265326135393237353962383231633235396536386232623030626462
3032
[opc@instance-20220112-0945 Chapter04]$ ansible-vault view --vault-password-file .password secret.yaml
ERROR! Decryption failed (no vault secrets were found that could decrypt) on secret.yaml for secret.yaml
[opc@instance-20220112-0945 Chapter04]$ ansible-vault view --vault-password-file .newpassword secret.yaml
test
[opc@instance-20220112-0945 Chapter04]$ 
```

### 인프라 프로비저닝에서 ansible

* 감사 추적을 위한 서버 생성부터 현재까지 서버 이력 확인
* 다중 스테이징 환경에 서버를 프로비저닝에 도움
* 글로벌 클라우드간 서버 이동


### httpd 재시작하는 동안 Nagios httpd 모니터링을 중지하는 예시  

```yaml
---
- hosts: ws01.fale.io 
  tasks: 
    - name: Mute Nagios 
      nagios: 
        action: disable_alerts 
        service: httpd 
        host: '{{ inventory_hostname }}' 
      delegate_to: nagios.fale.io 
    - name: Stop the HTTPd service 
      service: 
        name: httpd 
        state: stopped 
    - name: Wait for 5 minutes 
      pause: 
        minutes: 5 
    - name: Start the HTTPd service 
      service: 
        name: httpd 
        state: stopped 
    - name: Unmute Nagios 
      nagios: 
        action: enable_alerts 
        service: httpd 
        host: '{{ inventory_hostname }}' 
      delegate_to: nagios.fale.io
```



### 사용자 모듈

* ./Learning-Ansible-2.X-Third-Edition-master/Chapter07

```
├── ansible.cfg                    : 라이브러리 선언
├── library
│   ├── check_user.py
│   ├── check_user_id.py
│   ├── check_user_py2.py
│   ├── check_user_py3.py
│   ├── kill_java.sh
│   ├── rsync.rb
│   └── test_check_user_id.py
└── playbooks
    ├── check_user.yaml
    ├── check_user_id.yaml
    └── check_user_py2.yaml
    
## ansible.cfg 
[defaults]
library=library
## ./playbooks/check_user_id.yaml 
---
- hosts: localhost
  connection: local
  vars:
    user: root
  tasks:
    - name: 'Retrive {{ user }} data if it exists'
      check_user_id:
        user: '{{ user }}'
      register: user_data
    - name: 'Print user {{ user }} data'
      debug:
        msg: '{{ user_data }}'
```

## 디버그와 에러 핸들러

```
├── ansible.cfg
├── hosts
├── playbooks
│   ├── assert_ls.yaml
│   ├── hosts
│   │   └── ws01.yaml
│   ├── maven_build.yaml
│   ├── setup_and_config_apache.yaml
│   ├── setup_apache.yaml
│   └── tags_example.yaml
├── roles
│   └── java
│       └── tasks
│           └── main.yaml
├── templates
│   └── userdir.conf
└── Vagrantfile
```

* --syntax-check : 문법에 맞는 파일인지 체크
* --check : 실제 설치 등 변경을 하지 않고 예상되는 결과를 보여줌 
* --diff : playbook 파일 설정 변경 사항을 표시함, --check 와 함께 사용 (+는 추가, -는 삭제)

```bash
[opc@instance-20220112-0945 Chapter08]$ cat playbooks/setup_and_config_apache_test.yaml
- hosts: all
  tasks: 
    - name: Install Apache 
      yum: 
        name: httpd 
        state: present 
      become: True
    - name: Enable Apache 
      service: 
        name: httpd 
        state: started 
        enabled: True 
      become: True
    - name: Ensure Apache userdirs are properly configured
      template:
        src: ../templates/userdir.conf
        dest: /etc/httpd/conf.d/userdir.conf
      become: True
[opc@instance-20220112-0945 Chapter08]$ ansible-playbook -i ws01.fale.io, -u ansible playbooks/setup_and_config_apache_test.yaml --diff --check

PLAY [all] *****************************************************************************************
TASK [Gathering Facts] *****************************************************************************ok: [ws01.fale.io]

TASK [Install Apache] ******************************************************************************ok: [ws01.fale.io]

TASK [Enable Apache] *******************************************************************************ok: [ws01.fale.io]

TASK [Ensure Apache userdirs are properly configured] **********************************************--- before: /etc/httpd/conf.d/userdir.conf
+++ after: /home/opc/.ansible/tmp/ansible-local-29291647gGWql/tmp2lT3Gp/userdir.conf
@@ -14,7 +14,7 @@
     # of a username on the system (depending on home directory
     # permissions).
     #
-    UserDir disabled
+    UserDir enabled
 
     #
     # To enable requests to /~user/ to serve the user's public_html

changed: [ws01.fale.io]

PLAY RECAP *****************************************************************************************ws01.fale.io               : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

[opc@instance-20220112-0945 Chapter08]$
```










## 여기까지 했음
  * https://subscription.packtpub.com/book/cloud-and-networking/9781789954333/11/ch11lvl1sec86/functional-testing-in-ansible
    * Chapter 8, Debugging and Error Handling
    * Chapter 9, Complex Environments
  * ./Learning-Ansible-2.X-Third-Edition-master/Chapter08

## 문제해결

### 주요 방화벽 정책 추가 명령

```
sudo firewall-cmd --state
sudo firewall-cmd --list-all
sudo firewall-cmd --get-default-zone
sudo firewall-cmd --zone=public --add-port=38317/tcp --permanent
sudo firewall-cmd --reload
sudo firewall-cmd --runtime-to-permanent

sudo ufw status
sudo ufw allow from any to any port 38317 proto tcp

sudo iptables -A INPUT -p tcp -m state --state NEW -m tcp --dport 38317 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 38317 -j ACCEPT
sudo iptables-save

# Oracle Cloud - Ubuntu Firewall
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 38317 -j ACCEPT
sudo netfilter-persistent save
```

* [초보자를 위한 firewalld (rockylinux)](https://docs.rockylinux.org/ko/guides/security/firewalld-beginners/)


### oracle Cloud 방화벽 정책 변경 미적용

* 증상: Oracle Cloud 에서 Ubuntu 22.04 기본 이미지 서버를 사용할 때 Firewall 에서 포트를 허용해도 접속이 되지 않음
* 원인: iptable INPUT Chain에서 reject 하단으로 정책이 추가되어 허용되지 않음
* 조치: reject 상단에 정책을이 추가되도록 number 를 지정하여 정책추가함 <br> `sudo iptables -I INPUT [INPUT 정책에 추가할 위치 줄 번호] -m state --state NEW -p tcp --dport 80 -j ACCEPT`
* 비고: Oracle Cloud 네트워크 방화벽은 별도 허용해야함
* 참고
  * https://stackoverflow.com/questions/62326988/cant-access-oracle-cloud-always-free-compute-http-port


```bash
ubuntu@instance-20220913-1731:~$ sudo iptables -L --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  anywhere             anywhere             state RELATED,ESTABLISHED
2    ACCEPT     icmp --  anywhere             anywhere            
3    ACCEPT     all  --  anywhere             anywhere            
4    ACCEPT     udp  --  anywhere             anywhere             udp spt:ntp
5    ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:ssh
6    ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:38317
7    REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited
8    ACCEPT     tcp  --  anywhere             anywhere             state NEW tcp dpt:38317

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination         
1    REJECT     all  --  anywhere             anywhere             reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    InstanceServices  all  --  anywhere             link-local/16       

Chain InstanceServices (1 references)
num  target     prot opt source               destination   
..
```
