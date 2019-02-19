---
title:  "테마 변경은 언제나 즐거워"
excerpt: "테마를 변경하면서 겪은 내용을 기록합니다. "
layout: posts
categories: [logs]
tags: [jekyll theme, TIL, Today i Learned ]
comments: false
toc: true
toc_label: "table of contents"
toc_icon: "heart"
last_modified_at: 2019-02-19T23:40:00+09:00
---


# 테마란 무엇인가


## 그건 ... 이렇습니다.

좀 더 의욕을 높이고자(?) 테마를 변경하였습니다. 기존에는 이런 테마를 가지고 노는 것을 너무 좋아해서 그야말로 여기저기 좋아보이는 기능을 붙이는걸 좋아했습니다. 

하지만 무언가 저장을 하지 않고 경험으로만 여기다보니 다시금 같은 걸 하고 싶을때 기억도 안날뿐더러 이해가 안가는 상황도 발생하였습니다.

이걸 어떻게 생각해야 할지 참 난감했습니다. 매일 매일 새로운 기술이 나오고 조금이라도 기억에 남길 방법이 필요했습니다. 그러다 생각한 것이 이 TIL(Today I Learned) 입니다.

무작정 다른 개발자분이 하는 것을 따라 해보면 좋지 않을까 생각하고 알아보던중 쓸만해 보이는걸 발견했고 마침 이뻐보이는 테마를 발견해서 fork 의 의미도 모르고 해버렸습니다. 저는 시스템관리를 주로 해서 이런 개발 환경이나 프로세스는 잘 알지 못합니다.

이러한 테마를 변경하면서 겪은 내용을 정리하고 추후 같은 일을 반복할때 조금이라도 기억하길 기대하면서 기록합니다.

## 너로 결정했다.

정을 들이기 위해 가장 쉽게 접근할수 있는 [Jekyll Themes](http://jekyllthemes.org/) 사이트를 정독(!) 하기로 했습니다.


역시 가장 먼저 봐서 그런지 "이거다!" 라는 느낌이 드는 것은 없었습니다. 그래서 시작된 구글 이미지 검색, 검색어는 ``jekyll themes``


그러다가 발견한 것이 바로 이 테마 [minimal-mistakes](https://mmistakes.github.io/minimal-mistakes/) 입니다. 


선택한 이유는 몇가지 있습니다.

* 목차가 있어야 한다.
* 카테고리와 태그를 지원해야 한다.
* 년도별 작성 내역을 아카이브 한다.
* 제목과 글이 눈에 바로 들어올 정도로 이미지 사용을 기본적으로 최대한 자제 한다.


이렇게 해서 일단 fork!


## 고생은 원래 사서 해야 제맛!

여기서 아차 했습니다.

정말 그만둘뻔했지만 [도움말](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/)을 참고해서 기본 설정을 조금씩 손보면서 하나하나 수정해나가기 시작했습니다.

일단 TIL 용이니 https://zasfe.github.io/TIL 같은 경로에서 사용을 하도록 실행 경로부터 변경을 해야 했습니다. 일단 제 사이트에서 돌아가는 모습을 보고 싶었기 때문입니다.

그리고 처음 마주하게된 사이트는 생각보다 근사했습니다. 아직 글도 없고, 아무것도 없는데도 상당히 근사해보였습니다.

그렇게 만족하면서 하루를 보냈습니다. 아직 고생은 시작하지 않았습니다.


### 1. 기본 구조

#### 원본
- URL: https://mmistakes.github.io/minimal-mistakes/
- Repo : https://github.com/mmistakes/minimal-mistakes

#### My
- URL : http://zasfe.github.io/Today-I-learned
- Repo : https://github.com/zasfe/Today-I-learned


### 2. 수정사항
-  ``_config.yml``' 수정
  * locale, title 등등
- 상단 네비게이션 영역 수정: Articles, Categories, Tags, About
  * About 은 https://zasfe.github.io/ 로 링크
- ``Articles`` 영역 신규 생성
  * 구조는 Tags.html + home.html
  * 생성파일
    *  ``_layouts\articles.html``
    * ``__pages\articles.md``

``Articles``는 사실 원래 테마에는 없는 기능입니다. 같은 제작자님의 [다른 테마](https://github.com/mmistakes/so-simple-theme)에 있는 걸 보고 여기도 있으면 좋겠다 싶어서 만들었습니다.

만들면서 jekyll 문법을 보게되었고 뭔가 php 같으면서 아닌 묘한 느낌을 받았습니다. 언제가 될지 모르겠지만 다음에는 jekyll 을 좀 더 알아봐야 할것 같습니다.


## 원래 끝은 다 그렇다.  

작성하다보니 어느덧 예상했던 시간을 훌쩍 넘어버렸습니다. 

저는 체력이 좋지 않아 시간이 지날수록 기억력이 급감하는 것 같습니다.

## 맺음

* 아직 여정은 끝나지 않았습니다.
  * 왜 작성 시간은 표시가 되지 않을까요?
* 작성은 윈도우 환경에서 VisualStudioCode 로 하고 있으며, github 의 pages 기능을 활용 합니다.
* gitpages 에러가 눈에 잘 띄지 않아 ``bundle exec jekyll serve`` 로 로컬 테스트를 먼저 하고 ``push`` 하는 방법을 사용합니다.
  * push 한줄 명령 : ``git add . & git add -u & git commit -a -m "update" & git push origin master``

# 참조
* [minimal-mistakes](https://mmistakes.github.io/minimal-mistakes/)
* [so-simple-theme](https://github.com/mmistakes/so-simple-theme)