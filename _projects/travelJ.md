---
layout:       project
date:         26 Apr 2023
title:        TravleJ 여행제이
caption:      일본 여행 명소 추천 게시판
description:  >
    🖼️ 여행제이 메인 화면
image:        /assets/img/projects/TraveljMain.PNG
screenshot:
  src:        /assets/img/projects/TraveljMain.PNG
  srcset:
    1920w:    /assets/img/projects/TraveljMain.PNG
    960w:     /assets/img/projects/TraveljMain.PNG
    480w:     /assets/img/projects/TraveljMain.PNG
links:
  - title: Github
    url: https://github.com/project-TravleJ
    
accent_color: '#a85641'
accent_image: /assets/img/sidebar-bg.jpg
---

___

## 진행배경
![800x400](/assets/img/projects/TraveljWHY.jpg)

최근 통계에 따르면, 일본이 한국인에게 인기 있는 해외 여행지 중 단연코 1위를 차지하고 있습니다. 현재 여행 트렌드는 대중적인 관광지보다는 개인의 취향을 반영한 목적지를 선호하는 경향이 강해지고 있는데요. 이에 착안하여, 여행자 개개인이 자신만의 발견한 특별한 장소를 다른 이들과 공유하고, 함께 여행을 떠날 동료를 찾을 수 있는 커뮤니티 게시판을 구축하는 프로젝트를 시작하게 되었습니다. 이 공간에서는 각자 알고 있는 숨겨진 명소를 추천하고, 같은 관심사를 가진 여행 동반자를 찾을 수 있을 것입니다.

## 프로젝트 기간
2023.03.04. ~ 2023.04.26.

## 팀 구성 및 역할
| 이름      | 역할      | 
|:----------|:----------|
| 김용민    |   PM      |
| 신예찬    |   GIT     |
| 김지수    |   DBA     | 
| 류재현    |   DBA     |
| 이범성    |   DBA     |
|==========|============|

PM와 아이디어, Google Map API 기능, FRONT 초기세팅, <br>
관리자 게시판 신고 관리 RUD, <br>
DOCKER 및 CI & CD 세팅 <br>

## 주요내용
### 시스템 구성도
![800x400](/assets/img/projects/TraveljDiagram.PNG) 

### 시스템 DB
![800x400](/assets/img/projects/TraveljDB.jpg) 

## 결과
### 개인 결과 페이지
![800x400](/assets/img/projects/TraveljIGO.PNG)
첫 번째 이미지는 사용자들이 게시판 내에서 회원 신고, 게시글 신고 등 다양한 신고 활동을 할 수 있게 디자인된 인터페이스를 보여줍니다. 또한, 관리자는 이를 관리자 페이지를 통해 효율적으로 처리할 수 있도록 시스템을 구축했습니다. 추가로 제공된 이미지들에서는 Google Maps API를 활용하여 지도 상에 마커를 추가, 조회, 삭제하는 등의 기능들이 구현된 것을 확인할 수 있습니다. 이 기능들은 사용자가 지도에서 원하는 위치를 손쉽게 식별하고 관리할 수 있게 해 줍니다.


### 팀 결과 페이지
![800x400](/assets/img/projects/TraveljYGO.PNG)
사용자가 게시글을 작성하고 조회할 수 있는 기능을 나타냅니다. 이곳에서 사용자들은 직접 마커를 지도에 등록하여 사용하는 기능을 포함하여, 게시글의 내용을 자유롭게 수정하거나 새로 작성할 수 있는 유연성을 제공받을 수 있도록 하였습니다.
![800x400](/assets/img/projects/TraveljYGOO.PNG)
사용자가 자신의 계정 정보를 관리할 수 있는 마이페이지 기능을 보여줍니다. 여기서는 개인 정보 관리뿐만 아니라, 사용자가 작성한 게시글을 최신 순으로 조회할 수 있는 옵션도 제공됩니다. 추가적으로, 사용자가 원하는 특정 조건을 설정하여 게시글을 필터링하고 검색할 수 있는 기능도 포함되어 있어, 원하는 정보를 보다 쉽고 빠르게 찾을 수 있습니다. 
## Use Skills & Tool

| Skills & Tool                         | reason to use            
|:--------------------------------------|:---------------    
| [VSCode][11] and [intellij][12]       | Front and backend Tool   
| [Java][21] and [JavaScript][22]       | 웹프로젝트 와 백엔드
| [Spring Framework][23] and [Spring Boot][24]  | Java  
| [Spring data JPA][26]                 | JPA
| [Mysql][32]                           | 데이터베이스    
| [Github][41] and [Notion][42]         | 협업 Tool       
| [Miro][43] and [Figma][48]            | 협업 Tool
| [SourceTree][46] and [DA#5][47]       | 협업 Tool   
| [AWS EC2][51] and [Docker][52]        | 배포 기술



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