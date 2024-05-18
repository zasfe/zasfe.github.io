---
title: "2024.05.19 도커 데스크탑 없이 윈도우 컨테이너 사용하기"
excerpt: "Docker Desktop 라이선스가 무료에서 변경되어 Docker Desktop 없이 윈도우 컨테이너와 리눅스 컨테이너를 구성하는 방법을 테스트 하였습니다."
layout: single
categories: [TIL]
tags: [Docker, Docker Desktop, Windows containers, Linux containers, Containers, TIL, Today I Learned]
comments: false
toc: true
last_modified_at: 2024-05-19T10:47:00+09:00
---


# 2024.05.19 도커 데스크탑 없이 윈도우 컨테이너 사용하기

> Markus Lippert 의 글인 [Running Windows and Linux containers without Docker Desktop](https://lippertmarkus.com/2021/09/04/containers-without-docker-desktop/) 를 기반으로 테스트하고 결과를 기록합니다.


회사나 집에서 윈도우를 기본으로 사용하고 있습니다. 

이제까지는 WSL(Linux용 Windows 하위 시스템)로 리눅스 OS에서 컨테이너를 이용해서 테스트를 했지만 윈도우 컨테이너 테스트가 필요했고 Docker.com 는 윈도우에서 컨테이너를 사용하는 경우 Docker Desktop을 사용하도록 매뉴얼을 제공하고 있습니다.

하지만 Docker Desktop 라이선스가 이전 무료에서 250인 이상 또는 매출 등 기준을 초과하는 경우 비용이 발생하도록 변경되어 대안을 찾아야 했습니다.

이 글은 윈도우 호스트 PC에 윈도우 컨테이너와 리눅스 컨테이너를 사용할 수 있는 환경을 구성하고 사용하는 방법을 테스트한 방법을 정리한 것입니다.


**테스트 환경**
```
# 윈도우 호스트 PC
Microsoft Windows 10 Pro (10.0.19045 N/A 빌드 19045) 


# WSL 버전
WSL 버전: 2.1.5.0
커널 버전: 5.15.146.1-2
WSLg 버전: 1.0.60
MSRDC 버전: 1.2.5105
Direct3D 버전: 1.611.1-81528511
DXCore 버전: 10.0.25131.1002-220531-1700.rs-onecore-base2-hyp
Windows 버전: 10.0.19045.4412

# WSL OS
Ubuntu 20.04.3 LTS(Focal Fossa)
```



## Windows Containers 기능 설치

윈도우 기능 중 컨테이너와 Hyper-V 를 사용가능하도록 설정이 필요하며, 설정간 재부팅이 필요할 수 있습니다.

컴파일되어 있는 Docker.com 에서 제공하는 바이너리 파일을 이용해서 도커 서비스를 등록 및 시작합니다.

```powershell
## Optionally enable required Windows features if needed
Enable-WindowsOptionalFeature -Online -FeatureName containers -All
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
 
curl.exe -o docker.zip -LO https://download.docker.com/win/static/stable/x86_64/docker-20.10.13.zip 
Expand-Archive docker.zip -DestinationPath C:\
[Environment]::SetEnvironmentVariable("Path", "$($env:path);C:\docker", [System.EnvironmentVariableTarget]::Machine)
$env:Path = [System.Environment]::GetEnvironmentVariable("Path","Machine")
dockerd --register-service
Start-Service docker
docker run hello-world
```


## wsl 버전 업그레이드

컨테이너 기술은 리눅스 계열에서 먼저 시작되어 윈도우에서 적극적으로 적용을 하고 있는 만큼 윈도우에서 사용하려면 WSL version 0.67.6 이상이 되도록 업데이트를 해야 합니다.

```
# https://devblogs.microsoft.com/commandline/systemd-support-is-now-available-in-wsl/#how-can-you-get-systemd-on-your-machine
## wsl --version
## WSL version 0.67.6 and higher

wsl --update
```

## Linux Containers 설정 - wsl

WSL로 설치한 우분투를 systemd 로 구동할 수 있도록 설정하고 리눅스 컨테이너가 구동할 수 있도록 관련 패키지를 설치합니다.

윈도우 호스트 PC에서 리눅스 컨테이너도 함께 다룰 수 있도록 특정포트에서 도커가 응답할 수 있도록 설정도 추가합니다.

```
## /etc/wsl.conf
[boot]
systemd=true

sudo apt-get install -y apt-transport-https ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo docker run hello-world
# Hello from Docker!
 
# Automatically start on startup
sudo systemctl enable docker.service
sudo systemctl enable containerd.service

## Easily run Windows and Linux containers simultaneously
sudo cp /lib/systemd/system/docker.service /etc/systemd/system/
sudo sed -i 's/\ -H\ fd:\/\//\ -H\ fd:\/\/\ -H\ tcp:\/\/127.0.0.1:2375/g' /etc/systemd/system/docker.service
sudo systemctl daemon-reload
sudo systemctl restart docker.service
 
### 만약 오류가 난다면...(아마 systemd=false 인 경우)
echo "DOCKER_OPTS=\" -H tcp:127.0.0.1:2375 \"" | sudo tee -a /etc/default/docker
/etc/init.d/docker restart
```


## docker context 설정

환경 구성이 거의 끝났습니다. 

윈도우 호스트 PC에서 리눅스 컨테이너를 관리할 수 있도록 context 설정을 합니다. WSL에서 리눅스 컨테이너 중 nginx를 실행하고 윈도우 호스트 PC에서 윈도우 컨테이너 중 nanoserver를 실행합니다.



```powershell
## On Windows via PowerShell
docker context create lin --docker host=tcp://127.0.0.1:2375

## Window Container create
docker run --rm --name wincontainer mcr.microsoft.com/windows/nanoserver:1809 ping -t localhost
 
## Linux Container create
docker -c lin run --rm -d --name lincontainer nginx
```

**실행 예시**

```
> docker ps
CONTAINER ID   IMAGE                                       COMMAND               CREATED              STATUS              PORTS     NAMES
9b770862430c   mcr.microsoft.com/windows/nanoserver:1809   "ping -t localhost"   About a minute ago   Up About a minute             wincontainer
 
> docker -c lin ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
283a057dfe48   nginx     "/docker-entrypoint.…"   18 seconds ago   Up 17 seconds   80/tcp    lincontainer
```

이렇게 윈도우 호스트 PC에서 도커 데스크탑 없이 컨테이너를 생성하고 사용하는 방법을 테스트하고 그 결과를 기록합니다.

EOD
