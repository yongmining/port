---
layout:       project
date:         10 Nov 2023
title:        You Film Face ?
caption:      안면인식 및 감정분석
description:  >
    🖼️DeepFace API 결과 화면
image:        /assets/img/projects/YFF.png
screenshot:
  src:        /assets/img/projects/YFF.png
  srcset:
    1920w:    /assets/img/projects/YFF.png
    960w:     /assets/img/projects/YFF.png
    480w:     /assets/img/projects/YFF.png
links:
  - title: Github
    url: https://github.com/yongmining/ALL-IN-Project-fin

featured:     true
accent_color: '#a85641'
accent_image: /assets/img/sidebar-bg.jpg
---

___

## 진행배경
![800x400](/assets/img/projects/yffWHY.png) 
- 감정 인식의 어려움
- 우울증 증가 및 자살률 상승
- 정신 건강 서비스 접근성
- 경제적 및 시간적 부담 <br>

먼저 여러분들은 자신의 감정이 어떠한지 정확히 알 수 있으신가요? 자신의 감정 상태를 정확히 이해하고 파악하는 것은 종종 어려울 수 있습니다. 우리는 때때로 자신이 우울하지 않다고 생각할 수 있지만, 의식하지 못하는 사이에 우울함이 쌓여 심각한 감정 문제로 발전할 가능성이 있습니다. 이는 많은 사람들에게서 나타나는 현상이며, 우리 나라가 OECD 국가 중 자살률이 가장 높다는 첫 번째 사진에서 보여지는 통계는 이를 분명히 드러냅니다.

두 번째 사진을 통해 우리는 자살률의 주요 원인 중 하나로 감정 장애가 여러 세대에 걸쳐 얼마나 큰 문제인지를 확인할 수 있습니다. 이는 감정 문제가 다른 많은 사회적 문제의 핵심 요소로 작용하고 있음을 부정할 수 없는 증거입니다.

마지막으로, 마지막 사진에서 보듯이, 우울증 환자 수가 줄어들기는커녕 매년 증가하고 있습니다. 개인이 심리 검사를 받기 위해 병원을 찾을 때, 사회적 시선의 무게를 느낄 수밖에 없는 현실입니다. 더욱이, 직장 생활을 하는 이들에게는 치료 및 검사를 받는 것이 시간적, 경제적 부담으로 이어져 치료 자체가 어려운 상황에 처할 수 있음을 알게 되었습니다. 이러한 문제의 심각성을 인지하고, 예방과 치료에 접근성을 높이기 위해 우리 프로젝트를 구상하게 되었습니다. 우리의 노력이 이러한 장벽을 낮추고, 정신 건강 치료의 접근성을 개선하는 데 기여할 수 있기를 희망합니다.

## 프로젝트 기간
2023.09.15. ~ 2023.11.10.

## 팀 구성 및 역할   
김용민 팀장/PM, Git<br>
신현강 팀원/AI<br>
이준성 팀원/DBA <br>
최명건 팀원/DBA <br>

Kakao 소셜 로그인과 비회원 로그인 기능 구현, <br>
ChatBot 기능, Feedback CRUD, Member CRUD 구현 <br>

## 주요 내용
### 시스템 구성도
![800x400](/assets/img/projects/yffDiagram.png)
### 시스템 흐름도
![800x400](/assets/img/projects/yffLogic.png)

## 결과
### 개인 결과 페이지
![800x400](/assets/img/projects/yffIGO.png)
첫번째 사진 : 카카오 소셜로그인, 비회원 로그인을 통해 접속<br>
두번째 사진 : 자신의 정보를 입력, 비회원일 경우 닉네임 고정<br>
세번째 사진 : 회원일 경우 자신의 정보를 수정 및 탈퇴 기능, 명언과 감정기록 조회<br>
네번째 사진 : 상담 컨텐츠<br>
오번째 사진 : 피드백 작성 및 관리자는 조회
![800x400](/assets/img/projects/yffChat.png)
GptTurbo 3.5를 이용한 챗봇을 이용하여 심리 상담을 할 수 있도록 설정
1. 파인튜닝을 통하여 할려 했으나 비용면으로 부담이 되어 중지
2. 랭채인 라이브러리를 사용하여 Promote 설정

### 팀 결과 페이지
![800x400](/assets/img/projects/yffTGO.png)
첫번째 사진 : DeepfaceAPI를 이용하여 안면인식 및 감정분석 <br>
두번째 사진 : 감정분석 된 결과를 통해 여러 컨텐츠 추천 및 글귀 <br>
세번째 사진 : 유튜브 컨텐츠 추천 화면<br>
네번째 사진 : 책 추천 화면<br>



## Use Skills & Tool

| Skills & Tool                         | reason to use            
|:--------------------------------------|:---------------    
| [VSCode][11] and [intellij][12]       | Front and backend Tool   
| [Java][21] and [JavaScript][22]       | 웹프로젝트 와 백엔드
| [Spring Framework][23] and [Spring Boot][24]  | Java
| [Spring data JPA][26]                 | JPA
| [Python][25]                          | Python
| [MySql][32]                           | 데이터베이스    
| [Github][41] and [Notion][42]         | 협업 Tool       
| [Miro][43] and [Draw.io][44]          | 협업 Tool
| [Slack][45] and [SourceTree][46]      | 협업 Tool   
| [DA#5][47] and [Figma][48]       | 협업 Tool   
| [AWS lightSail][51] and [Docker][52]  | 배포 기술


[11]: https://code.visualstudio.com/
[12]: https://www.jetbrains.com/
[21]: https://www.java.com/ko/
[22]: https://code.visualstudio.com/
[23]: https://spring.io/projects/spring-framework
[24]: https://spring.io/projects/spring-boot
[25]: https://www.python.org/
[26]: https://spring.io/projects/spring-data-jpa
[31]: https://www.oracle.com/kr/
[32]: https://www.mysql.com/
[41]: https://github.com/
[42]: https://www.notion.so/
[43]: https://miro.com/
[44]: https://www.drawio.com/
[45]: https://slack.com/intl/ko-kr/
[46]: https://www.sourcetreeapp.com/
[47]: https://www.uniondata.co.kr/
[48]: https://www.figma.com/
[51]: https://aws.amazon.com/
[52]: https://www.docker.com/