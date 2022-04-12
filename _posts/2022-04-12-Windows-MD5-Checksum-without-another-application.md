---
title: "윈도우에서 별도의 프로그램 없이 파일의 MD5 체크섬을 확인하는 방법"
excerpt: "파일이 동일한지 비교를 위해 MD5 체크섬을 확인하는 방법을 설명합니다."
layout: single
categories: [Computer]
tags: [Window Checksum,Windows MD5 Checksum, MD5, checksum, certutil]
comments: false
toc: true
last_modified_at: 2022-04-12T00:28:57+09:00
---

# 윈도우에서 파일의 MD5 체크섬을 확인하는 방법

파일이 동일한지 비교를 위해 MD5 체크섬을 확인하는 방법을 설명합니다.

* 명령어: `certutil -hashfile <file> MD5`
* 실행 예시

```batch
D:\0.tmp\work>echo "zasfe" > example.txt

D:\0.tmp\work>type example.txt
"zasfe"

D:\0.tmp\work>copy example.txt example_bak.txt
        1개 파일이 복사되었습니다.

D:\0.tmp\work>certutil -hashfile example.txt
SHA1의 example.txt 해시:
72cf6335bf4a132c38ba9fe5b19dbbeb65988c6c
CertUtil: -hashfile 명령이 성공적으로 완료되었습니다.

D:\0.tmp\work>certutil -hashfile example_bak.txt
SHA1의 example_bak.txt 해시:
72cf6335bf4a132c38ba9fe5b19dbbeb65988c6c
CertUtil: -hashfile 명령이 성공적으로 완료되었습니다.

D:\0.tmp\work>
```

### 참조

  * https://portal.nutanix.com/page/documents/kbs/details?targetId=kA07V000000LWYqSAO
