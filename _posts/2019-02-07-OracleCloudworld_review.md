---
author: zasfe
date: 2019-02-07 17:40:00+09:00
layout: post
title: ORACLE CLOUDWORLD 발표자료 리뷰
categories:
- 리뷰
tags:
- ORACLE
- 세미나
---

## ORACLE CLOUDWORLD 발표자료 리뷰

* 해당 행사에 참석하지는 못했지만 발표자료를 기반으로 저희 생각을 작성하고자 합니다.

## KEYNOTE
* 다양한 환경의 변화에 대응하기 위해 오라클은 **Autonomous(자율)**에 대해서 주로 이야기를 합니다. 작년에는 Autonomous 를 기반으로 DBA가 해야 할 잡무들을 대신 해준다는 느낌이였다면, **올해에는 머신 러닝을 바탕으로 사람을 넘어서는 무언가를 향한다는 느낌**입니다. 자율운영은 비용/위험 감소를 바탕으로 리소스를 절약하여 혁신에 집중하게 해준다는 것이고 그에 맞게 다양한 WORKLOAD(운영절차)에 최적화 해준다는 것입니다. 이쯤에서 이건 무조건 해야 하는거고, 이게 현실이 되면 참 무서운 일들이 벌어질것 같습니다.

## TRACK 1 : 자율운영 데이터 관리 (Autonomous Data Management)

* 자율운영은 ORCLE 이 가장 잘하는 Data 관리 부분을 적극적으로 부각시키고 있습니다. RAC([Oracle Real Application Clusters](https://www.oracle.com/database/technologies/rac.html))와 ADG([Oracle Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)) 기반의 99.9995%(2.5분/월) 가용성, 편의성, BCP설계 제공한다고 합니다.
* 그 이외의 클라우드 인프라스트럭처의 제공 내역은 타 클라우드와 비슷합니다. 자율운영이 반영된 DBMS를 SaaS 로써 제공하여 차별화를 하고 있습니다.
* 클라우드 인프라스트럭처의 큰 차이는 데이터센터로 3개의 Fault Domain으로 구성되어 물리적인 분리가 되어 있지만, 1개 이상의 데이터 센터로 구성이 되어 있다고 합니다. 단일 데이터센터에 물리적인 분리구성만으로도 구현될수 있는 설명이기에 Azure의 리전과 비슷한 합니다. 장점은 역시 오라클 데이터베이스에 최적화 되어 최고의 성능을 제공할수 있다는 것입니다. 국내 상용 리전 런칭이 예정되어 있다고 합니다. 
* 글로벌 클라우드 벤더간 간단히 비교를 하면 아래와 같습니다. (순서는 알파벳입니다.)
  * 리전
    * AWS: 2개 이상의 가용영역 모음
    * Azure: 1개 이상의 데이터 센터로 구성 
    * Oracle: 1개 이상의 가용성 도메인(AD)로 구성, 가용성 도메인은 가용영역과 비슷한 역활을 합니다.
  * 가용영역
    * AWS: 리전내 가용영역당 3개 이상의 데이터센터로 구성
    * Azure: 고유한 물리적 위치
    * Oracle: 3개 이상의 Fault Domain(장애도메인)으로 구성



## TRACK 2 : 2세대 클라우드 (Generation 2 Cloud)

* 2세대 인프라를 강조하고 있으나 특이사항은 없다. 머신러닝을 접목시켰다 라는데, 머신러닝이라는 문구를 빼도 큰 무리가 없는 구성이라는게 함정

## TRACK 3 : 클라우드 인프라 현대화 (Cloud Infrastructure Modernization)

* 오라클의 사명은 "모두의 이익을 위해 전세계 모든 데이터를 관리하고 보호.."
* IaaS는 성장 및 빠르게 성숙기, 엔터프라이즈 SaaS 앱시장도 성숙기, 그러므로 모든것의 중짐이자 비즈니스 자산인 Data는 이제 시작!
* 데이터의 저장과 활용이라는 2가지 측면에서 트랜드가 변화해왔음, 서로 보완적인 관계

## TRACK 4 : 최신 클라우드 개발 (Modern Cloud Development)

* Enterprise-Ready Blockchain(허가형 엔터프라이즈 블록체인) 기술도 꺼내 들었습니다. 사전에 알려진 참가자으로 구성된 컨소시엄으로 구성원간의 투명성을 제공한다는 것이죠(개발형은 비트코인같이 누구나 가입 및 정보에 액세스 할수 있습니다.)
* 물론 직접 만들수도 있지만, 클라우드를 쓰면 쉽다(분석, 계정 연계등)는 이야기를 합니다.

## TRACK 5 : ERP, 새로운 변혁의 중심 (ERP, The New Core of Business)

* 이젠 챗봇 입니다.
  * Digital Assistant
  * Automate Smarter: 자동화된 프로세스 적용으로 비용 절감 및 정보 지연 방지
  * Work Smarter: 업무 시나리오 기반 사전 감지 및 조치로 부정 방지 및 컴플라리언스 이슈 예방
  * Optimize Smarter: 스마트비즈니스 프로세스 적용으로 비즈니스 민첩성과 효율 증가

## TRACK 6 : CX, 고객의 진화에 따른 마케팅 뉴 패러다임 (CX, Your Evolving Customer)

* CX 는 Consumer Experience
* 세그먼테이션 및 타게팅된 대상을 통한 광고를 지원합니다.(2019년 하반기 출시 예정)
  * 결국 데이터웨어하우스를 통한 통합 및 교차분석을 통한 시너지 활용


## TRACK 7 : HCM, 밀레니얼 시대의 인사 관리 혁신 (HCM, The Changing Workforce)

* 딱히 주제와는 상관없지만 꼿힌 부분
  - 어떤일이 발생했나? ( Data & Basic Reporting )
    * Delivered dashboards & reports
    * Role based
    * Configure your own Insights
  - 왜 발생했나? ( Cross-process and Functional Analysis )
    * Monitor KPIs in real-time
    * Intelligence in context
    * Drill down to take action
    * Trend Analysis
    * Alerts
    * Scenario Planning
  - 무엇이 발생할수 있나? ( Predictive Analysis )
    * Predictive Analysis
    * Machine Learning
    * Intelligence
    * Decision automation

## 맺음

* 오라클에서 클라우드를? 이라는 생각으로 봤지만 아직은 정말 클라우드스럽게 명확한 컨셉을 찾지 못하고 있는것 같다.
* 조금더 지켜볼 예정


# 참조
  * [ORACLE CLOUDWORLD 발표자료](http://cloudworld.co.kr/down/)
  * [AWS와 Azure 서비스 비교](https://docs.microsoft.com/ko-kr/azure/architecture/aws-professional/services)
  * [Azure에서 가용성 영역이란?](https://docs.microsoft.com/ko-kr/azure/availability-zones/az-overview)
