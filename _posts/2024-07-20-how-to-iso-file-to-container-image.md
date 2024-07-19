---
title: "2024.07.20 ISO 파일로 OS 컨테이너 이미지 만들기"
excerpt: "실제 OS에 애플리케이션 테스트를 위해 ISO 파일로 컨테이너 이미지를 만드는 방법을 설명합니다."
layout: single
categories: [TIL]
tags: [docker, iso, container, TIL, Today I Learned]
comments: false
toc: true
last_modified_at: 2024-07-20T11:47:00+09:00
---

# ISO 파일로 OS 컨테이너 이미지 만들기

최근 CentOS 7의 EOS로 인해 서버 이전을 해야 하는 경우가 많이 발생하고 있어 자동화하는 방법을 구상하고 있었습니다. VM을 만들고 관련 애플리케이션을 설치 및 설정하고, ... 이 과정을 매번 하려니 각 작업별로 크게 다르지 않고 내가 아닌 다른 사람들도 할 텐데 엄청 다양한 방법이 사용될 것 같고 그건 나중에 유지보수 등을 어렵게 만드는 상황이 발생할 가능성이 커보였습니다. EOS가 아니더라도 서버 이전은 빈번할 것이고 현재 VM을 수동으로 만들어야 하는 상황이지만 이건 나가질 것이고 그 이후 단계라도 자동화를 해보자 라는 생각이 들었습니다.

이제 자동화 단계를 생각하다 보니 ansible 설정을 만들다가 문득 자동화한 방법을 정기적으로 테스트를 해야 하는게 아닌가 라는 생각이 들었고 테스트를 해야 할때마다 VM을 만들고 테스트하는 것이 너무 오래 걸릴 것 같았습니다. 얼마전 github actions 를 활용하는 방법을 써보면 어떨까.. 그래서 ISO 파일을 기반으로 용량 가득한 테스트용 OS 컨테이너를 만들게 되었습니다.


## 선인들의 지식 탐색

간단히 생각해보면 ISO 파일 내용을 그대로 컨테이너 이미지에 넣을 수 있으면 될 것 같았지만 부트로더가 구동하는 환경까지는 있어야 만들 수 있겠다는 생각을 했습니다. 너무 멀리가고 있습니다. ISO 파일 내용만으로 부팅이 가능한 컨테이너를 만들면 설치형 OS 환경이 만들어지는 것이고 마치 VM 처럼 대부분의 문제가 해결될 것 같았습니다. 그래서 ISO 파일을 받아 컨테이너를 만드는 테스트를 하였습니다.

1. ISO 파일을 다운로드 합니다.
2. ISO 파일에서 기본 파일 시스템 파일은 압축되어 있어 해제합니다.
3. 압축해제된 시스템 파일을 기반으로 컨테이너를 생성합니다.
4. 컨테이너로 부팅을 하고 정상 부팅이 되고 일반적인 명령어가 실행되는지 확인합니다.


```bash make_iso_to_container-image.sh
#!/usr/bin/env bash
LANG=C

# prepare
ISO_URL="https://releases.ubuntu.com/18.04.6/ubuntu-18.04.6-live-server-amd64.iso";

# STEP1 ::: download iso file, read only filesystem tools
ISO_FILE=`echo ${ISO_URL} | awk -F "/" '{print $NF}'`;
ISO_OS_NAME=`echo ${ISO_FILE} | awk -F "-" '{print $1"-"$2}'`;

wget -O ${ISO_FILE} ${ISO_URL};
apt-get install -y squashfs-tools;

# step2 ::: iso mount
mkdir -p rootfs unsquashfs;
test -f ${ISO_FILE} && mount -o loop ${ISO_FILE} rootfs;

find . -type f | grep 'filesystem\.squashfs$';
#sudo unsquashfs -f -d unsquashfs/ rootfs/casper/filesystem.squashfs

ISO_ROOTFS=`find . -type f | grep 'filesystem\.squashfs$'`;
test -f ${ISO_ROOTFS} && unsquashfs -f -d unsquashfs/ ${ISO_ROOTFS};

# step3 ::: make container images
tar -C unsquashfs -c . | docker import - zasfe/${ISO_OS_NAME}

# step4 ::: container running test
docker run -it --rm -h myos -t zasfe/${ISO_OS_NAME} bash
```

컨테이너를 만들었으니 ISO 파일을 연결했던 설정과 압축해제한 시스템 파일도 삭제합니다.


```bash cleanAll-make_iso_to_container-image.sh
#!/usr/bin/env bash
LANG=C

MOUNT_POINT=`df -Ph | egrep "^\/.*rootfs$" | awk '{print$1}'`;

if test -z "${MOUNT_POINT}" 
then
    echo "# Not Find Mount point"
else
    echo "# Umount Mount point :${MOUNT_POINT} "
    umount -f ${MOUNT_POINT}
fi

test -d ./rootfs && rm -rf ./rootfs
test -d ./unsquashfs && rm -rf ./unsquashfs
```

이제 남은 것은 설치 자동화를 위한 ansible 설정과 OS마다 정상 동작을 하는지 확인하는 CI를 구성하는 것입니다.


## 출처

- https://stackoverflow.com/questions/42385527/how-to-create-docker-image-from-an-iso-file
- https://medium.com/@SofianeHamlaoui/convert-iso-images-to-docker-images-4e1b1b637d75
  
