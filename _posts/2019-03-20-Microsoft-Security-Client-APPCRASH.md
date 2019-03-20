---
title: "Microsoft Security Client crashing 0x80070006"
excerpt: "Microsoft Security Client 정책버전 오류로 인한 APPCRASH, 정책강제 업데이트로 해결 "
layout: single
categories: [Computer]
tags: [문제해결, Window, SCEP, Microsoft Security Essentials]
comments: false
toc: true
last_modified_at: 2019-03-20T10:56:57+09:00
---

해당 이슈는 글로벌규모로 현재는 해결되었다는 공식 코맨트가 있습니다.

## 증상
- MsMpEng.exe 프로세스 중지(APPCRASH)

### 환경
- Windows 7
- Windows 2008, 2008 R2 (Service Pack 무관)

## 원인

- 정책버전 1.289.1521.0 의 오류로 업데이트시 빠른검사와 전체검사 중 APPCRASH 발생(글로벌이슈)

## 해결 방법

- 정책 강제업데이트 후 정상화 ( 정책버전 1.289.1573.0 이후 반영 필요 )

## 방법1 - 패턴 강제업데이트 및 빠른 검사
"C:\Program Files\Microsoft Security Client\MpCmdRun.exe" /SignatureUpdateAndQuickScan


## 방법2 - 기존 바이러스 패턴 파일 삭제
cd "C:\Program Files\Microsoft Security Client"
MPCMDRUN -RemoveDefinitions

## 방법3 - 패턴 강제업데이트(MS공식업데이트배포지활용, WSUS설정무시)
"C:\Program Files\Microsoft Security Client\MpCmdRun.exe" -removedefinitions -all
"C:\Program Files\Microsoft Security Client\MpCmdRun.exe" -signatureupdate -mmpc


## 참고

* [SCEP Service Stopped for Multiple Computers Last Night](https://www.reddit.com/r/SCCM/comments/b2wy37/scep_service_stopped_for_multiple_computers_last/)
* [SCEP Crashing PC's](https://social.technet.microsoft.com/Forums/en-US/18ab60a3-3b26-4a07-b68d-84085ce66ce5/scep-crashing-pcs?forum=ConfigMgrCompliance)
* [Windows Defender Security definition problems](https://www.askwoody.com/2019/windows-defender-security-definition-problems/)
* [Microsoft Antimalware Crashing With Error 0x800106ba on Windows 7 & 8](https://www.bleepingcomputer.com/news/security/microsoft-antimalware-crashing-with-error-0x800106ba-on-windows-7-and-8/)
