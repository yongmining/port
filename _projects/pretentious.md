---
layout:       project
date:         14 Jul 2023
title:        DDbnb 댕댕비엔비
caption:      펫시터, 펫맘 모집 게시판
description:  >
image:        /assets/img/projects/DDbnbSitter.jpg
screenshot:
  src:        /assets/img/projects/DDbnbSitter.jpg
  srcset:
    1920w:    /assets/img/projects/DDbnbSitter.jpg
    960w:     /assets/img/projects/DDbnbSitter@0,5.jpg
    480w:     /assets/img/projects/DDbnbSitter@0,25.jpg
#links:
#  - title:    Demo
#    url:      https://qwtel.com/hydejack-pretentious/
featured:     true
accent_color: '#a85641'
accent_image: /assets/img/sidebar-bg.jpg
---
<hr>



## 프로젝트 설명
**DDbnb 댕댕비엔비로 소셜 로그인을 통하여** <br>
**자신의 애견동물을 돌봐주는 사람을 모집하는 펫시터,** <br>
**자신에게 애견동물을 맡길 사람을 펫맘을 모집하는 게시판을 작성하는 프로젝트입니다.** <br>
**신청목록에서 신청자의 정보를 보고 쪽지를 통해 협의하고 확정, 경험한 뒤 리뷰를 작성하게 됩니다.** <br>
**마이페이지에서는 자신의 경력과 정보들을 작성하며 볼 수 있으며,** <br>
**지금까지 작성 된 리뷰와 별점이 보이기에 신용성을 확인 할 수 있습니다.**


## 맡은 역할
**DBA 데이터베이스 구축 설계** <br>
**소셜 로그인, 로그아웃, 탈퇴 기능** <br>
**AccessToken을 받아서 프로필 생성, 수정 기능** <br>
**CI & CD 구축과 AWD 배포 최적화**

# Frontend CODE.
### `_config.yml`

~~~yml
import { IS_LOGIN } from "../modules/LoginModule";

export const callKakaoLoginAPI = (code) => {

    const requestURL = `http://${process.env.REACT_APP_RESTAPI_URL}/api/v1/login/kakaocode`;

    return async (dispatch, getState) => {

        let data = { code: code }

        const result = await fetch(requestURL, {
            method: 'POST',
            headers: {
                "Content-Type": 'application/json',
                "Accept": '*/*'
            },
            body: JSON.stringify(data)
        }).then(res => res.json());
        if (result.status === 200) {
            window.localStorage.setItem('accessToken', JSON.stringify(result.data.token));
            dispatch({ type: IS_LOGIN });
        }
    };
}
~~~
**코딩 설명**


![Typeface](../assets/img/pretentious-1.jpg){:.lead}

## Attributions

| Work                                                   | License        | Changes
|:-------------------------------------------------------|:---------------|:-
| [IMac vector.svg][11] by [Rafael Fernandez][12]        | [CC-BY-SA-3.0] | Apple logo removed, rasterized
| [iPhone 6S Rose Gold.png][21]                          | [CC-BY-SA-3.0] | Desaturated
| [iPad Air 2.png][31] by [Justinhu12][32]               | [CC-BY-SA-4.0] | Desaturated

Screenshots can be reused under [CC-BY-SA-4.0].

[11]: https://commons.wikimedia.org/wiki/File:IMac_vector.svg
[12]: https://commons.wikimedia.org/wiki/User:TheGoldenBox
[21]: https://commons.wikimedia.org/wiki/File:IPhone_6S_Rose_Gold.png
[31]: https://commons.wikimedia.org/wiki/File:IPad_Air_2.png
[32]: https://commons.wikimedia.org/wiki/User:Justinhu12

[CC-BY-SA-4.0]: https://creativecommons.org/licenses/by-sa/4.0/
[CC-BY-SA-3.0]: https://creativecommons.org/licenses/by-sa/3.0/
