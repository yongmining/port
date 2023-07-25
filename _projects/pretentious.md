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

# LOGIN 관련 CODE.
## Frontend CODE.
### `LoginPage.js`

~~~yml
import style from './MainStyle.module.css';

const KAKAO_REST_API_KEY = '202bf1013addf514255b52a8c9c69ebf';
const KAKAO_REDIRECT_URI = `https://${process.env.REACT_APP_LOGIN_URL}/kakao/callback`;
const KAKAO_AUTH_URI = `https://kauth.kakao.com/oauth/authorize?client_id=${KAKAO_REST_API_KEY}&redirect_uri=${KAKAO_REDIRECT_URI}&response_type=code`;

const NAVER_CLIENT_ID = 'T0mWG2VjAfBH9cYz6Qrf';
const NAVER_REDIRECT_URI = encodeURI(`https://${process.env.REACT_APP_LOGIN_URL}/login/oauth2/code/naver`);
const NAVER_STATE_STRING = 'login';
const NAVER_AUTH_URI = `https://nid.naver.com/oauth2.0/authorize?response_type=code&client_id=${NAVER_CLIENT_ID}&redirect_uri=${NAVER_REDIRECT_URI}&state=${NAVER_STATE_STRING}`;

const loginHandler = (platform) => {

    switch (platform) {
        case 'N':
            window.location.href = NAVER_AUTH_URI;
            break;
        case 'K':
            window.location.href = KAKAO_AUTH_URI;
            break;
        default:
            break;
    }
}

function LoginPage() {
    return (
        <div className={style.loginStyle}>
            <h1><img src="/img/loginTitle.png" alt="댕댕비엔비" /></h1>
            <h2> 간편로그인 </h2>

            <div onClick={() => loginHandler('N')}><img src="../../../img/naverLogin.png" alt="네이버 로그인" />
            </div>
            <br />
            <div onClick={() => loginHandler('K')}><img src="../../../img/kakaoLogin.png" alt="카카오 로그인" />
            </div>
        </div>
    )
}

export default LoginPage;
~~~
**코딩 설명**

### `KakaoPage.js`
~~~yml
import { useEffect } from "react";
import { useNavigate } from "react-router-dom";
import { callKakaoLoginAPI } from "../../api/LoginAPI";
import { useDispatch } from "react-redux";
import { getCurrentMember } from "../../api/MemberAPICalls";

function KakaoPage() {
    const code = new URL(window.location.href).searchParams.get('code');
    const navigate = useNavigate();
    const dispatch = useDispatch();

    useEffect(() => {
        dispatch(callKakaoLoginAPI(code))
            .then(() => {
                const token = JSON.parse(window.localStorage.getItem('accessToken'));
                dispatch(getCurrentMember(token.memberId))
                    .then((member) => {
                        if (member === "새로운회원") {
                            navigate("/loginprofile", { replace: true });
                        } else {
                            navigate("/", { replace: true });
                        }
                    });
            })
    }, [code, dispatch, navigate]);

    return null;
}

export default KakaoPage;
~~~
**코딩 설명**

### `LoginAPi.js`

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

export const callKakaoLogoutAPI = () => {

    const requestURL = `http://${process.env.REACT_APP_RESTAPI_URL}/api/v1/login/kakaologout`;

    return async (dispatch, getState) => {
        const accessToken = JSON.parse(window.localStorage.getItem('accessToken'));
        const result = await fetch(requestURL, {
            method: 'POST',
            headers: {
                "Content-Type": 'application/json',
                "Authorization": accessToken.access_token
            }
        }).then(res => res.json());
        if (result.status === 200) {
            window.localStorage.removeItem('accessToken');
            dispatch({ type: IS_LOGIN });
            window.location.reload();
        }
    };
}
~~~
**코딩 설명**

## BackEnd
### `LoginController.java`
~~~yml
    @PreAuthorize("permitAll()")
    @ApiOperation(value = "카카오 인가 코드 받아와서 액세스 토큰 발급")
    @PostMapping("/kakaocode")
    public ResponseEntity<?> getKakaoCode(@RequestBody Map<String, String> code) {
        /* 인가 코드로 액세스 토큰 발급 */
        KakaoAcessTokenDTO kakaoToken = loginService.getAccessToken(code.get("code"));

        /* 액세스 토큰으로 DB 저장or 확인 후 JWT 생성 */
        loginService.getJwtToken(kakaoToken);

        Map<String, Object> responseMap = new HashMap<>();
        responseMap.put("token", kakaoToken);

        /* JWT와 응답 결과를 프론트에 전달*/
        return ResponseEntity.ok().body(new ResponseDTO(HttpStatus.OK, "로그인 성공", responseMap));
    }

    @PostMapping("/kakaologout")
    public ResponseEntity<?> kakaoLogout(@RequestHeader("Authorization") String accessToken) {
        /* 카카오 로그아웃 API 호출 */
        boolean logoutSuccess = loginService.kakaoLogout(accessToken);

        if (logoutSuccess) {
            /* 로그아웃 성공 처리 */
            return ResponseEntity
                    .ok()
                    .body(new ResponseDTO(HttpStatus.OK, "로그아웃 성공", null));
        } else {
            /* 로그아웃 실패 처리 */
            return ResponseEntity
                    .badRequest()
                    .body(new ResponseDTO(HttpStatus.BAD_REQUEST, "로그아웃 실패", null));
        }
    }
~~~
**코딩 설명**

### `AccessTokenDTO.java`
~~~yml
@AllArgsConstructor
@NoArgsConstructor
@Setter
@Getter
@ToString
@JsonIgnoreProperties(ignoreUnknown = true)
public class KakaoAcessTokenDTO {

    private long memberId;
    private String access_token;
    private String token_type;
    private String refresh_token;
    private int expires_in;
    private int refresh_token_expires_in;
    private String loginType;


}
~~~
**코딩 설명**

### `LoginService.java`
~~~yml
@Service
public class LoginService {
    private final MemberService memberService;
    private final FileUploadUtils fileUploadUtils;

    @Value("${oauth.Kakao.client-id}")
    private String KAKAO_CLIENT_ID;
    @Value("${oauth.naver.client-id}")
    private String NAVER_CLIENT_ID;
    @Value("${oauth.naver.secret}")
    private String NAVER_SECRET;


    public LoginService(MemberService memberService, FileUploadUtils fileUploadUtils) {
        this.memberService = memberService;
        this.fileUploadUtils = fileUploadUtils;
    }

    public KakaoAcessTokenDTO getAccessToken(String code) {

        RestTemplate rt = new RestTemplate();
        rt.setRequestFactory(new HttpComponentsClientHttpRequestFactory());

        HttpHeaders headers = new HttpHeaders();
        headers.add("Content-type", "application/x-www-form-urlencoded;charset=utf-8");

        MultiValueMap<String, String> params = new LinkedMultiValueMap<>();
        params.add("grant_type", "authorization_code");
        params.add("client_id", KAKAO_CLIENT_ID);
        params.add("redirect_uri", "https://www.ddbnb.site/kakao/callback");

        params.add("code", code);

        HttpEntity<MultiValueMap<String, String>> kakaoTokenRequest = new HttpEntity<>(params, headers);

        ResponseEntity<String> accessTokenResponse = rt.exchange("https://kauth.kakao.com/oauth/token", HttpMethod.POST,
                kakaoTokenRequest, String.class);

        ObjectMapper objectMapper = new ObjectMapper();
        KakaoAcessTokenDTO kakaoToken = null;
        try {
            kakaoToken = objectMapper.readValue(accessTokenResponse.getBody(), KakaoAcessTokenDTO.class);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }

        kakaoToken.setLoginType("kakao");

        return kakaoToken;
    }

    public KakaoProfileDTO findKakaoProfile(String accessToken) {
        RestTemplate rt = new RestTemplate();

        HttpHeaders headers = new HttpHeaders();
        headers.add("Authorization", "Bearer " + accessToken);
        headers.add("Content-type", "application/x-www-form-urlencoded;charset=utf-8");

        HttpEntity<MultiValueMap<String, String>> kakaoProfileRequest = new HttpEntity<>(headers);

        ResponseEntity<String> kakaoProfileResponse = rt.exchange("https://kapi.kakao.com/v2/user/me", HttpMethod.POST,
                kakaoProfileRequest, String.class);

        KakaoProfileDTO kakaoProfileDTO = new KakaoProfileDTO();
        ObjectMapper objectMapper = new ObjectMapper();

        try {
            kakaoProfileDTO = objectMapper.readValue(kakaoProfileResponse.getBody(), KakaoProfileDTO.class);
        } catch (JsonProcessingException e) {
            throw new RuntimeException(e);
        }
        return kakaoProfileDTO;
    }

    public String findProfileImage(String imageUrl) {
        RestTemplate rt = new RestTemplate();

        HttpHeaders headers = new HttpHeaders();
        headers.add("Content-type", "application/json");

        HttpEntity<MultiValueMap<String, String>> profileImageRequest = new HttpEntity<>(headers);

        ResponseEntity<byte[]> profileImageResponse = rt.exchange(
                "https://api.dicebear.com/6.x/thumbs/png?seed=" + imageUrl, HttpMethod.GET, profileImageRequest,
                byte[].class);

        byte[] profileImage = profileImageResponse.getBody();

        String replaceFileName;

        try {
            replaceFileName = fileUploadUtils.saveFile(profileImage);
        } catch (IOException e) {
            throw new RuntimeException(e);
        }

        return replaceFileName;
    }

    @Transactional
    public void getJwtToken(KakaoAcessTokenDTO kakaoToken) {

        KakaoProfileDTO kakaoProfileDTO = findKakaoProfile(kakaoToken.getAccess_token());

        /* 해당 유저의 가입 이력이 없을 경우 */
        if (memberService.findBySocialId("KAKAO", String.valueOf(kakaoProfileDTO.getId())) == null) {
            MemberDTO newMember = new MemberDTO();

            newMember.setSocialLogin("KAKAO");
            newMember.setSocialId(String.valueOf(kakaoProfileDTO.getId()));

            newMember.setRefreshToken(kakaoToken.getRefresh_token());
            newMember.setRefreshTokenExpireDate(kakaoToken.getRefresh_token_expires_in() + System.currentTimeMillis());

            newMember.setAccessToken(kakaoToken.getAccess_token());
            newMember.setAccessTokenExpireDate(kakaoToken.getExpires_in() + System.currentTimeMillis());

            newMember.setStatus("정상");
            newMember.setProfileImage(findProfileImage(newMember.getSocialId()));

            newMember.setSignDate(LocalDate.now());
            newMember.setLastVisitDate(LocalDate.now());

            if (kakaoProfileDTO.getKakao_account().getGender() != null) {
                newMember.setGender(kakaoProfileDTO.getKakao_account().getGender());
            }

            newMember.setStarPoint("0");
            memberService.registNewUser(newMember);
        }

        /* 소셜 아이디로 멤버가 있는지 조회해 가져옴 */
        Member foundmember = memberService.findBySocialId("KAKAO", String.valueOf(kakaoProfileDTO.getId()));
        kakaoToken.setMemberId(foundmember.getMemberId());

        /* 액세스토큰, 리프레시 토큰 업데이트 */
        foundmember.setRefreshToken(kakaoToken.getRefresh_token());
        foundmember.setAccessToken(kakaoToken.getAccess_token());
        foundmember.setRefreshTokenExpireDate(kakaoToken.getRefresh_token_expires_in() + System.currentTimeMillis());
        foundmember.setAccessTokenExpireDate(kakaoToken.getExpires_in() + System.currentTimeMillis());
        foundmember.setLastVisitDate(LocalDate.now());

        Date accessExpireDate = new Date(foundmember.getAccessTokenExpireDate());

        if (accessExpireDate.before(new Date(System.currentTimeMillis()))) {

            RenewTokenDTO renewedToken = renewKakaoToken(foundmember);

            if (renewedToken.getRefresh_token() != null) {

                foundmember.setRefreshToken(renewedToken.getRefresh_token());
                foundmember.setRefreshTokenExpireDate(
                        renewedToken.getRefresh_token_expires_in() + System.currentTimeMillis());
            }

            foundmember.setAccessToken(renewedToken.getAccess_token());
            foundmember.setAccessTokenExpireDate(renewedToken.getExpires_in() + System.currentTimeMillis());

        }
    }


    public RenewTokenDTO renewKakaoToken(Member foundMember) {

        RestTemplate rt = new RestTemplate();

        HttpHeaders headers = new HttpHeaders();
        headers.add("Content-type", "application/x-www-form-urlencoded;charset=utf-8");

        MultiValueMap<String, String> params = new LinkedMultiValueMap<>();
        params.add("grant_type", "refresh_token");
        params.add("client_id", KAKAO_CLIENT_ID);
        params.add("refresh_token", foundMember.getRefreshToken());


        HttpEntity<MultiValueMap<String, String>> kakaoTokenRequest = new HttpEntity<>(params, headers);

        ResponseEntity<String> renewTokenResponse = rt.exchange("https://kauth.kakao.com/oauth/token", HttpMethod.POST,
                kakaoTokenRequest, String.class);

        ObjectMapper objectMapper = new ObjectMapper();
        RenewTokenDTO renewToken = null;
        try {
            renewToken = objectMapper.readValue(renewTokenResponse.getBody(), RenewTokenDTO.class);
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }

        return renewToken;
    }

    public boolean kakaoLogout(String accessToken) {

        RestTemplate rt = new RestTemplate();
        HttpHeaders headers = new HttpHeaders();

        headers.add("Authorization", "Bearer " + accessToken);
        MultiValueMap<String, String> params = new LinkedMultiValueMap<>();

        HttpEntity<MultiValueMap<String, String>> kakaoLogoutRequest = new HttpEntity<>(params, headers);

        ResponseEntity<String> kakaoLogoutResponse = rt.exchange("https://kapi.kakao.com/v1/user/logout",
                //                "https://kapi.kakao.com/v1/user/unlink",
                HttpMethod.POST, kakaoLogoutRequest, String.class);

        return kakaoLogoutResponse.getStatusCode().is2xxSuccessful();
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
