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

인증서 서비스에 사용되는 `certutil.exe` 파일을 이용하는 것으로 커맨드라인에서 확인을 합니다.  
인터넷에서 다운받은 파일이 원본이 맞는지 확인을 위한 체크섬(Checksum)을 확인하는 방법에 많이 사용 됩니다.


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


사용 가능한 HASH 알고리즘은 `MD2` `MD4` `MD5` `SHA1` `SHA256` `SHA384` `SHA512` 가 있습니다.

```
D:\0.tmp\work>certutil -hashfile -?
사용법:
  CertUtil [옵션] -hashfile InFile [HashAlgorithm]
  파일에 암호화 해시 생성 및 표시

옵션:
  -Unicode          -- 리디렉션된 출력을 유니코드로 씁니다.
  -gmt              -- GMT로 시간 표시
  -seconds          -- 시간을 초와 밀리초로 표시
  -v                -- 자세한 정보 표시 작동
  -privatekey       -- 암호 및 개인 키 데이터 표시
  -pin PIN                  -- 스마트 카드 PIN
  -sid WELL_KNOWN_SID_TYPE  -- 숫자 SID
            22 -- 로컬 시스템
            23 -- 로컬 서비스
            24 -- 네트워크 서비스

해시 알고리즘: MD2 MD4 MD5 SHA1 SHA256 SHA384 SHA512

CertUtil -?              -- 동사 목록(명령 목록)을 표시합니다.
CertUtil -hashfile -?    -- "hashfile" 동사의 도움말 텍스트를 표시합니다.
CertUtil -v -?           -- 모든 동사의 도움말 텍스트를 모두 표시합니다.
```


### 참조
  * https://docs.microsoft.com/ko-kr/windows-server/administration/windows-commands/certutil
  * https://portal.nutanix.com/page/documents/kbs/details?targetId=kA07V000000LWYqSAO
