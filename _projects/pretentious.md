---
layout:       project
date:         14 Jul 2023
title:        DDbnb 댕댕비엔비
caption:      펫시터, 펫맘 모집 게시판
description:  >
    🖼️ DDbnb 펫시터 게시판 화면
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
___
## 프로젝트 설명
**DDbnb 댕댕비엔비로 소셜 로그인을 통하여** <br>
**자신의 애견동물을 돌봐주는 사람을 모집하는 펫시터,** <br>
**자신에게 애견동물을 맡길 사람을 펫맘을 모집하는 게시판을 작성하는 프로젝트입니다.** <br>
**신청목록에서 신청자의 정보를 보고 쪽지를 통해 협의하고 확정, 경험한 뒤 리뷰를 작성하게 됩니다.** <br>
**마이페이지에서는 자신의 경력과 정보들을 작성하며 볼 수 있으며,** <br>
**지금까지 작성 된 리뷰와 별점이 보이기에 신용성을 확인 할 수 있습니다.**
___
## 맡은 역할
**DBA 데이터베이스 구축 설계** <br>
**소셜 로그인, 로그아웃, 탈퇴 기능** <br>
**AccessToken을 받아서 프로필 생성, 수정 기능** <br>
**CI & CD 구축과 AWD 배포 최적화**
___
# LOGIN 관련 CODE.
___
## Frontend CODE.
### `LoginPage.js`

~~~java
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
- Kakao 와 Naver 플랫폼 이미지의 버튼들이 있습니다. 
- Kakao 버튼을 클릭하면 KAKAO_AUTH_URI의 주소 값으로 이동합니다.
- Kakao계정으로 로그인 할 수 있는 페이지가 나오게 됩니다.
![Typeface](../assets/img/projects/LoginPage.jpg)
___
### `KakaoPage.js`
~~~java
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
- Kakao 페이지에서 로그인을 하면 code를 callKakaoLoginAPI(code)로 보낸다.
- code에 있는 accessToken을 token으로 정의합니다.
- token에 있는 memberId로 멤버를 조회합니다.
-> 신규 회원일 경우 프로필을 등록하는 페이지로 가집니다.
-> 기존 회원일 경우 메인 홈페이지로 이동하게 됩니다. 
-> token.memberId가 "새로운 회원"일 경우 신규 회원
___
### `LoginAPi.js`
~~~java
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
###### 로그인
- backend에서 지정한 requestURL로 요청합니다.
- 'data' 객체를 생성하여 'code'속성 인자를 'code'값으로 할당합니다.
- fetch 함수를 사용하여 카카오 로그인 API를 호출합니다.
- 전달된 code 값과 함께 POST 요청을 보냅니다.
- 서버로부터 응답을 받아 result 변수에 할당합니다.
- 만약 서버로부터의 응답이 성공적이라면 result 객체의 data 속성에 있는 token 값을 로컬 스토리지에 저장합니다.
- Redux 액션을 디스패치하여 IS_LOGIN 타입의 액션을 발생시킵니다. 
###### 로그아웃
- backend에서 지정한 requestURL로 요청합니다.
- fetch 함수를 사용하여 카카오 로그아웃 API를 호출합니다.
- POST 요청을 보내며 서버로부터 응답을 받아 result 변수에 할당합니다.
- 응답이 성공적이라면 로컬 스토리지에 있는 엑세스 토큰을 삭제합니다.
- Redux 액션을 디스패치하여 IS_LOGIN 타입의 액션을 발생시킵니다. 
___
## BackEnd
### `LoginController.java`
~~~java
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
    public ResponseEntity<?> kakaoLogout(@RequestHeadertity<?> kakaoLogout(@RequestHeader("Authorization") String accessToken) {
        /* 카카오 로그아웃 API 호출 */
        boolean logoutSuccess = loginService.kakaoLogout(accessToken);("Authorization") String accessToken) {
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
###### 로그인
- @PreAuthorize("permitAll()") 엔드포인트에 모든 사용자가 접근할 수 있도록 허용

- @PostMapping("/kakaocode") HTTP POST 요청을 처리하는 엔드포인트

- HTTP 요청의 본문으로 들어오는 JSON 형태의 데이터를 Map<String, String> 형태로 파싱하여 code 변수에 저장합니다.

- loginService.getAccessToken(code.get("code")) 메서드를 호출하여 카카오 인가 코드를 사용하여 액세스 토큰을 발급합니다.

- loginService.getJwtToken(kakaoToken) 메서드를 호출하여 액세스 토큰을 사용하여 JWT를 생성합니다.

- 액세스 토큰과 함께 응답 데이터를 responseMap에 넣습니다.

- JWT와 응답 결과를 ResponseDTO 객체에 담아 HTTP 응답으로 반환합니다.
###### 로그아웃

- @PostMapping("/kakaologout") HTTP POST 요청을 처리하는 또 다른 엔드포인트

- HTTP 요청 헤더의 "Authorization"필드에서 액세스 토큰을 추출하여 accessToken 변수에 저장합니다.

- loginService.kakaoLogout(accessToken) 메서드를 호출하여 로그아웃 API를 호출

- 카카오 로그아웃 API의 결과에 따라 로그아웃이 성공적이었을 때와 실패했을 때 각각에 맞는 HTTP 응답을 반환합니다.
___
### `AccessTokenDTO.java`
~~~java
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
- @AllArgsConstructor 어노테이션은 모든 필드를 인자로 받는 생성자를 자동으로 생성

- @NoArgsConstructor 어노테이션은 인자 없는 기본 생성자를 자동으로 생성

- @Setter 어노테이션은 자동으로 모든 멤버 변수에 대한 Setter 메서드를 생성
-> Setter 메서드를 통해 멤버 변수의 값을 변경할 수 있습니다.

- @Getter 어노테이션은 자동으로 모든 멤버 변수에 대한 Getter 메서드를 생성. 
-> Getter 메서드를 통해 멤버 변수의 값을 읽을 수 있습니다.

- @ToString 어노테이션은 자동으로 toString() 메서드를 생성
-> toString() 메서드는 해당 클래스의 객체를 문자열로 표현할 때 사용.

- @JsonIgnoreProperties(ignoreUnknown = true) 어노테이션은 JSON 직렬화 및 역직렬화 과정에서 알 수 없는 속성들을 무시하도록 지정합니다.

- 카카오 액세스 토큰 정보를 담기 위한 데이터 전송 객체(DTO)입니다.
___
### `LoginService.java`
~~~java
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

___
# 프로필 등록, 수정
___
## FrontEnd
## 탈퇴 기능
### `MemberAPICalls.js`
~~~java
/* 현재 로그인 된 멤버 정보 가져오기 */
export const getCurrentMember = (memberId) => {

    const requestURL = `http://${process.env.REACT_APP_RESTAPI_URL}/api/v1/member/${memberId}`;
    return async (dispatch, getState) => {

        const result = await fetch(requestURL, {
            method: 'GET',
            headers: {
                "Content-Type": 'application/json',
                "Accept": '*/*'
            }
        }).then(res => res.json());

        if (result.status === 200) {
            dispatch({ type: GET_MEMBER, payload: result.data.members });
            if (result.data.members.nickname.startsWith("새로운회원")) {
                return "새로운회원";
            }
        }
    };
}

/* 현재 로그인 된 멤버 정보 업데이트 */
export const getUpdateMember = (memberId, form) => {

    const token = JSON.parse(window.localStorage.getItem('accessToken'));

    const requestURL = `http://${process.env.REACT_APP_RESTAPI_URL}/api/v1/member/${token.memberId}/postprofile`;
    return async (dispatch, getState) => {

        const result = await fetch(requestURL, {
            method: 'PUT',
            headers: {
                "Accept": '*/*',
                "Auth": token
            },
            body: form// 업데이트할 데이터를 JSON 문자열로 변환하여 요청에 포함
        }).then(res => res.json());

        if (result.status === 201) {
            dispatch({ type: PUT_MEMBER, payload: result.data });
            alert(result.message);
            window.location.href = `/mypage/${memberId}`;
        }
    };
}

/* 현재 로그인 된 멤버가 탈퇴 */
export const deleteMember = (memberId) => {

    const token = JSON.parse(window.localStorage.getItem('accessToken'));

    const requestURL = `http://${process.env.REACT_APP_RESTAPI_URL}/api/v1/member/${token.memberId}`

    return async (dispatch, getState) => {

        const result = await fetch(requestURL, {
            method: 'DELETE',
            headers: {
                "Content-Type": 'application/json',
                "Accept": '*/*',
                "Auth": token
            },
        })

        if (result.status === 200) {
            dispatch({ type: DELETE_MEMBER, payload: result.data });
            dispatch({ type: IS_LOGIN });
            window.localStorage.removeItem('accessToken');
            window.location.reload();
        }
    };
}
~~~
**코딩 설명**
###### 멤버 조회
- fetch 함수를 사용하여 서버에 HTTP GET 요청을 보냅니다. 
- requestURL로 지정된 URL로 GET 요청을 보내고, 서버로부터의 응답을 JSON 형식으로 받아 result 변수에 할당

- 만약 서버로부터의 응답이 성공적이라면 result.data.members 값을 Redux 액션과 함께 디스패치하여 GET_MEMBER 타입의 액션을 발생

- 멤버 정보에 닉네임(nickname)이 "새로운회원"으로 시작하는지 확인합니다. 이 조건이 참이면, "새로운회원" 문자열을 반환합니다.

###### 프로필 수정


- fetch 함수를 사용하여 서버에 HTTP PUT 요청을 보냅니다. 
- requestURL로 지정된 URL로 PUT 요청을 보내고, 헤더에는 Accept와 Auth 필드로 이전에 가져온 token 값을 사용합니다. 
- 업데이트할 데이터는 form 인자를 JSON 문자열로 변환하여 요청에 포함시킵니다.

- 만약 서버로부터의 응답이 성공적이라면 result.data 값을 Redux 액션과 함께 디스패치하여 PUT_MEMBER 타입의 액션을 발생시킵니다.

- 서버로부터의 응답이 201이면, 서버가 보낸 메시지를 알림으로 띄우고(alert(result.message)) 마이페이지로 리다이렉트(window.location.href = /mypage/${memberId}`)합니다.

###### 회원 탈퇴
- fetch 함수를 사용하여 서버에 HTTP DELETE 요청을 보냅니다. 
- requestURL로 지정된 URL로 DELETE 요청을 보내고, 헤더에는 Content-Type, Accept, 그리고 Auth 필드로 이전에 가져온 token 값을 사용합니다.

- 만약 서버로부터의 응답이 성공적이라면 result.data 값을 Redux 액션과 함께 디스패치하여 DELETE_MEMBER 타입의 액션을 발생시킵니다.

- 서버로부터의 응답이 성공적이면, 현재 로그인 상태를 나타내는 Redux 상태를 변경하기 위해 IS_LOGIN 타입의 액션을 발생하고 로컬 스토리지에서 "accessToken" 키를 삭제하고(window.localStorage.removeItem('accessToken')) 페이지를 다시 로드(window.location.reload())하여 로그아웃 상태로 만듭니다.

### `LoginProfilePage.js, ReviseProfilePage.js`
~~~java
import style from './ProfileStyle.module.css';
import { IoIosFemale } from 'react-icons/io';
import { IoIosMale } from 'react-icons/io';
import { useNavigate, useParams } from 'react-router-dom';
import React, { useState } from 'react';
import { useDispatch, useSelector } from 'react-redux';
import { getCurrentMember, getUpdateMember } from '../../api/MemberAPICalls';
import { useEffect } from "react";
import "../../component/modal/review/review.css";

function ReviseProfilePage() {
    const navigate = useNavigate();
    const dispatch = useDispatch();

    const { memberId } = useParams();
    const members = useSelector(store => store.memberReducer);

    const [form, setForm] = useState({
        nickname: "",
        experience: "",
        preferredArea: "",
        detailedHistory: "",
        petSitterCareer: "",
    });

    const [image, setImage] = useState()

    useEffect(
        () => {
            dispatch(getCurrentMember(memberId))
        }
        , []
    );

    useEffect(
        () => {
            setForm(members);
        }
        , [members]
    );

    const handleAction = () => {
        navigate("/mypage", { replace: true });
    }

    const handleActions = () => {
        const memberId = members?.memberId;

        const formData = new FormData();

        formData.append("newProfile", new Blob([JSON.stringify(form)], { type: "application/json" }));

        if (image) {
            formData.append("image", image);
        }

        dispatch(getUpdateMember(memberId, formData));
        // navigate(`/mypage/${memberId}`, { replace: true });
    }

    const [previewImage, setPreviewImage] = useState(null);

    const onChangeHandler = (e) => {
        let value = e.target.value;
        // 닉네임 글자수 제한을 5글자로 설정
        if (e.target.name === 'nickname') {
            value = value.substring(0, 5);
        }
        setForm({
            ...form,
            [e.target.name]: value
        });
    };
    const handleImageUpload = (event) => {
        const file = event.target.files[0];
        setImage(file);
        setPreviewImage(URL.createObjectURL(file));
    };

    /* 성별 아이콘 */
    function GenderIcon() {
        if (members?.gender === 'female' || members?.gender === 'F') {
            return <IoIosFemale />
        } else {
            return <IoIosMale />
        }
    }
    /* 소셜 로그인 아이콘 */
    function SocialIcon() {
        if (members?.socialLogin === "KAKAO") {
            return <img src='../../../img/kakao_logo.png' alt='kakao logo' width={'30px'} height={'27px'} />
        }
        if (members?.socialLogin === 'NAVER') {
            return <img src='../../../img/naver_logo.png' alt='naver logo' width={'30px'} height={'27px'} />
        }
    }
    return (
        <div className={style.profileContainer}>
            <div>
                <h2>{SocialIcon()} 프로필 수정  </h2>
                <hr />
            </div>
            <div className={style.profileBox}>
                <img src={previewImage || members?.profileImage} alt='profile' className={style.profileImageBox} />
                <div className={style.profileImageContent}>
                    <label>{members?.nickname}</label>
                    &nbsp;
                    성별
                    &nbsp;
                    <label className={style.Gender}>{GenderIcon()}</label>
                    <div>
                        <input
                            type="file"
                            id="imageUpload"
                            accept='image/jpg,image/png,image/jpeg,image/gif'
                            style={{ display: "none" }}
                            onChange={handleImageUpload}
                        />
                        <label className="yellow reviewmodal-imgbtn" htmlFor="imageUpload">
                            +
                        </label>
                    </div>
                </div>
            </div>
            <div>
                <hr />
                <br />
            </div>

            <div className={style.profiles}>
                <div className={style.profileCareer}>
                    닉네임
                </div>
                <div style={{ width: '70%' }}>
                    <input className={style.underLine}
                        type="text"
                        name='nickname'
                        value={form.nickname}
                        onChange={onChangeHandler}
                        maxLength={5}
                    />
                </div>
            </div>

            <div className={style.profiles}>
                <div className={style.profileCareer}>
                    경험견종
                </div>
                <div style={{ flexDirection: 'column', width: '70%' }}>
                    <input className={style.underLine}
                        type="text"
                        name='experience'
                        value={form.experience}
                        onChange={onChangeHandler}
                    />
                </div>
            </div>

            <div className={style.profiles}>
                <div className={style.profileCareer}>
                    펫시터 경력
                </div>
                <div style={{ width: '70%' }}>
                    <input className={style.underLine}
                        type="text"
                        name='petSitterCareer'
                        value={form.petSitterCareer}
                        onChange={onChangeHandler} />
                </div>
            </div>

            <div className={style.profiles}>
                <div className={style.profileCareer}>
                    선호 지역
                </div>
                <div style={{ width: '70%' }}>
                    <input className={style.underLine}
                        type="text"
                        name='preferredArea'
                        value={form.preferredArea}
                        onChange={onChangeHandler} />
                </div>
            </div>

            <div className={style.profiles}>
                <div className={style.profileCareer}>
                    상세 이력
                </div>
                <div style={{ width: '70%' }}>
                    <textarea className={style.textareabox}
                        type="text"
                        name='detailedHistory'
                        value={form.detailedHistory}
                        onChange={onChangeHandler}
                    />
                </div>
            </div>

            <div className={style.btnSet}>
                <button className={style.btnStyleCancle} onClick={handleAction}>
                    취소
                </button>
                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                <button className={style.btnStyleInsert} onClick={handleActions}>
                    수정
                </button>
            </div>
            <br />
        </div>
    )
}

export default ReviseProfilePage;
~~~
**코딩 설명**
- 프로필 등록과 수정은 비슷하여 더 많은 내용이 들어가는 수정화면만 설명
- 닉네임 글자수 제한을 6글자로 했으며 value 값을 바꿀 수 있게 해놨다.
-> 다른 방법으론 defaultValue로 해도 됩니다.
___
## BackEnd
### `MemberController.java`
~~~java
@ApiOperation(value = "멤버 소셜 id로 조회")
    @GetMapping("/members/{socialLogin}/{socialId}")
    public ResponseEntity<ResponseDTO> findBySocialId(@PathVariable String social_Login,
                                                      @PathVariable String social_Id) {

        HttpHeaders headers = new HttpHeaders();
        headers.setContentType(new MediaType("application", "json", Charset.forName("UTF-8")));

        Member foundMember = memberService.findBySocialId(social_Login, social_Id);

        Map<String, Object> responseMap = new HashMap<>();
        responseMap.put("member", foundMember);

        return ResponseEntity.ok().headers(headers).body(new ResponseDTO(HttpStatus.OK, "소셜 아이디 검색 성공", responseMap));
    }

        @ApiOperation("프로필 등록")
    @PutMapping("/member/{memberId}/postprofile")
    public ResponseEntity<ResponseDTO> updateprofile(@PathVariable long memberId,
                                                     @RequestPart("newProfile") MemberSimpleDTO memberSimpleDTO,
                                                     @RequestPart(value = "image", required = false) MultipartFile image) {


        return ResponseEntity.ok()
                             .body(new ResponseDTO(HttpStatus.CREATED, "프로필 수정 성공",
                                                   memberService.updateprofile(memberId, memberSimpleDTO, image)));

    }
        @DeleteMapping("/member/{memberId}")
    public ResponseEntity<?> deleteMember(@PathVariable long memberId) {

        memberService.deleteMember(memberId);

        return ResponseEntity.noContent().build();
    }

~~~
**코딩 설명**
___
### `MemberDTO.java, MemberSimpleDTO.java`
~~~java
@AllArgsConstructor
@NoArgsConstructor
@Setter
@Getter
@ToString
public class MemberDTO {

    private long memberId;
    private String nickname;
    private String profileImage;
    private String gender;

    private String experience;
    private String preferredArea;
    private String petSitterCareer;
    private String detailedHistory;

    private String starPoint;
    private String status;
    private int reportedCount;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd")
    private LocalDate signDate;
    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd")
    private LocalDate deletedDate;
    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd")
    private LocalDate lastVisitDate;

    private String socialLogin;
    private String socialId;
    private String accessToken;
    private long accessTokenExpireDate;
    private String refreshToken;
    private long refreshTokenExpireDate;
}

@AllArgsConstructor
@NoArgsConstructor
@Setter
@Getter
@ToString
public class MemberSimpleDTO {

    private long memberId;
    private String nickname;
    private String profileImage;
    private String gender;

    private String experience;
    private String preferredArea;
    private String petSitterCareer;
    private String detailedHistory;

    private String starPoint;
    private String status;
    private int reportedCount;
    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd")
    private LocalDate signDate;

    private String socialLogin;
}
~~~
**코딩 설명**
___
### `Member.java`
~~~java
@AllArgsConstructor
@NoArgsConstructor
@Setter
@Getter
@ToString
@Entity(name = "Member")
@Table(name = "MEMBER")
@SequenceGenerator(name = "member_sequence_generator", sequenceName = "sequence_member_id", initialValue = 1, allocationSize = 50)
public class Member {

    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "member_sequence_generator")
    @Column(name = "MEMBER_ID")
    private long memberId;

    @Column(name = "nickname", unique = true, nullable = false)
    private String nickname;

    @Column(name = "experience")
    private String experience;

    @Column(name = "profile_image")
    private String profileImage;

    @Column(name = "reported_count", nullable = false)
    private int reportedCount;

    @Column(name = "social_login", nullable = false)
    private String socialLogin;

    @Column(name = "social_id", nullable = false)
    private String socialId;

    @Column(name = "access_token", nullable = false)
    private String accessToken;

    @Column(name = "access_token_expire_date", nullable = false)
    private long accessTokenExpireDate;

    @Column(name = "refresh_token", nullable = false)
    private String refreshToken;

    @Column(name = "refresh_token_expire_date", nullable = false)
    private long refreshTokenExpireDate;

    @Column(name = "gender")
    private String gender;

    @Column(name = "sign_date", nullable = false)
    private LocalDate signDate;

    @Column(name = "deleted_date")
    private LocalDate deletedDate;

    @Column(name = "preferred_area")
    private String preferredArea;

    @Column(name = "pet_sitter_career")
    private String petSitterCareer;

    @Column(name = "detailed_history")
    private String detailedHistory;

    @Column(name = "star_point")
    private String starPoint;

    @Column(name = "status")
    private String status;

    @Column(name = "LAST_VISIT_DATE")
    private LocalDate lastVisitDate;
}
~~~
**코딩 설명**
___
### `MemberRepository.java`
~~~java
public interface MemberRepository extends JpaRepository<Member, Long> {

    @Query("SELECT m FROM Member AS m WHERE m.socialLogin LIKE :socialLogin AND m.socialId LIKE :socialId")
    Member findBySocialId(String socialLogin, String socialId);
    }
~~~
**코딩 설명**
___
### `MemberService.java`
~~~java
@Service
@AllArgsConstructor
public class MemberService {   
   
   //일부 멤버 조회
    public MemberSimpleDTO findMemberById(long memberId) {

        Member foundMember = memberRepository.findById(memberId).get();
        return modelMapper.map(foundMember, MemberSimpleDTO.class);
    }

    private final MemberMapper memberMapper;
    private final MemberRepository memberRepository;
    private final ModelMapper modelMapper;
    private final FileUploadUtils fileUploadUtils;

    @Transactional
    public long registNewUser(MemberDTO newMember) {
        newMember.setNickname("새로운회원" + (Math.random() * 100 + 1));
        return memberRepository.save(modelMapper.map(newMember, Member.class)).getMemberId();
    }

    @Transactional
    public MemberSimpleDTO updateprofile(long memberId, MemberSimpleDTO updateMember, MultipartFile image) {

        Member member = memberRepository.findById(memberId).get();

        member.setNickname(updateMember.getNickname());
        member.setPetSitterCareer(updateMember.getPetSitterCareer());
        member.setExperience(updateMember.getExperience());
        member.setDetailedHistory(updateMember.getDetailedHistory());
        member.setPreferredArea(updateMember.getPreferredArea());

        if (image != null) {
            try {
                String replaceFileName = fileUploadUtils.saveFile(image);
                member.setProfileImage(replaceFileName);
            } catch (IOException e) {
                throw new RuntimeException(e);
            }
        }

        return modelMapper.map(member, MemberSimpleDTO.class);
    }

    @Transactional
    public void deleteMember(long memberId) {

        Member foundMember = memberRepository.findById(memberId).get();

        memberRepository.delete(foundMember);
    }
~~~
**코딩 설명**
___
# CI & CD , AWD 설계와 구축
___
## FRONT
### `workflow.java`
~~~yml
name: React build

on: 
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout source code.   # 레포지토리 체크아웃
        uses: actions/checkout@master

      - name: Cache node modules      # node modules 캐싱
        uses: actions/cache@v1
        with:
          path: node_modules
          key: ${{ runner.OS }}-build-${{ hashFiles('**/package-lock.json') }}
          restore-keys: |
            ${{ runner.OS }}-build-
            ${{ runner.OS }}-
            
      - name: Generate Environment Variables File for Production  # 환경 변수 파일 생성
        env:
          REACT_APP_KAKAO_REST_API_KEY: ${{ secrets.REACT_APP_KAKAO_REST_API_KEY }}
          REACT_APP_KAKAO_JS_KEY: ${{ secrets.REACT_APP_KAKAO_JS_KEY }}
          # REACT_APP_NAVER_REST_API_KEY: ${{ secrets.REACT_APP_NAVER_REST_API_KEY }}
          # REACT_APP_NAVER_JS_KEY: ${{ secrets.REACT_APP_NAVER_JS_KEY }}
        run: |
          echo "REACT_APP_KAKAO_REST_API_KEY=$REACT_APP_KAKAO_REST_API_KEY">> .env.production
          echo "REACT_APP_KAKAO_JS_KEY=$REACT_APP_KAKAO_JS_KEY" >> .env.production
          # echo "REACT_APP_NAVER_REST_API_KEY=$REACT_APP_NAVER_REST_API_KEY">> .env.production
          # echo "REACT_APP_NAVER_JS_KEY=$REACT_APP_NAVER_JS_KEY" >> .env.production
          echo "hi ??"

      - name: Install Dependencies    # 의존 파일 설치
        run: npm install

      - name: Build                   # React Build
        run: CI='' npm run build

      - name: Deploy                  # S3에 배포하기
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
        run: |
          aws s3 cp \
            --recursive \
            --region ap-northeast-2 \
            build s3://ddbnb-front   #자신의 레포지토리로 변경해주세요.

      - name: CloudFront 캐시 무력화 설정
        uses: chetan/invalidate-cloudfront-action@v2
        env:
          DISTRIBUTION: ${{ secrets.AWS_CLOUDFRONT_ID }}
          PATHS: "/*"
          AWS_REGION: ${{ secrets.AWS_REGION }}
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
~~~
**코딩 설명**
___
## Backend
### `gradle.yml`
~~~yml
name: Java CI & CD

on:
  push:
    branches: [ "master" ]
  pull_request:
    branches: [ "master" ]

permissions:
  contents: read

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK 11
        uses: actions/setup-java@v3
        with:
          java-version: '11'
          distribution: 'temurin'
          
      ## create application-prod.properties
      - name: make application.yml
        run: |
          cd ./src/main/resources
          touch ./application.yml
          echo "${{ secrets.PROPERTIES_FILE }}" > ./application.yml
        shell: bash
          
      - name: Grant execute permission for gradlew
        run: chmod +x gradlew
        
      - name: Build with Gradle
        run: ./gradlew bootjar
        
      ## 웹 이미지 빌드 및 도커허브에 push
      - name: web docker build and push
        run: |
          docker login -u ${{ secrets.DOCKER_USERNAME }} -p ${{ secrets.DOCKER_PASSWORD }}
          docker build -t ${{ secrets.DOCKER_REPO }}/ddbnb ./
          docker push ${{ secrets.DOCKER_REPO }}/ddbnb:latest
      - name: Get timestamp
        uses: gerred/actions/current-time@master
        id: current-time

      - name: Run string replace
        uses: frabert/replace-string-action@master
        id: format-time
        with:
          pattern: '[:\.]+'
          string: "${{ steps.current-time.outputs.time }}"
          replace-with: '-'
          flags: 'g'

      - name: Deploy
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.HOST }}
          username: ${{ secrets.USERNAME }}
          key: ${{ secrets.PRIVATE_KEY }}
          script: |
            docker pull ${{ secrets.DOCKER_REPO }}/ddbnb:latest
            docker rm -f $(docker ps -a -q)
            docker run -d --name ddbnb -p 8080:8080 ${{ secrets.DOCKER_REPO }}/ddbnb:latest
~~~
**코딩 설명**
___
### `DockerFile`
~~~yml
FROM openjdk:11
LABEL authors="daeng"
ARG JAR_FILE=build/libs/*.jar
COPY ${JAR_FILE} app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
~~~
**코딩 설명**
___
### `Docker.aws.json`
~~~yml
{
  "AWSEBDockerrunVersion": "2",
  "Image": {
    "Name": "heemanher/ddbnb",
    "Update": "true"
  },
  "Ports": [
    {
      "ContainerPort": 8080,
      "HostPort": 8080
    }
  ]
}
~~~
**코딩 설명**
- 컨테이너를 실행하기 위해 Elastic Beanstalk 플랫폼의 버전을 지정합니다.
- Image 섹션은 배포할 Docker 이미지와 적용할 업데이트를 지정합니다.
- heemanher/ddbnb 배포할 Docker 이미지의 이름으로 지정합니다.
- 배포할 때 지정된 Docker 이미지의 최신 버전으로 업데이트하도록 지정
- Ports 섹션은 컨테이너와 호스트 간의 포트 매핑을 정의합니다.

- 컨테이너 내에서 응용 프로그램이 수신 대기하는 포트를 지정합니다.
- 호스트(Elastic Beanstalk 인스턴스)에서 컨테이너의 포트 8080을 매핑할 호스트의 포트를 지정합니다. 이는 컨테이너 내에서 실행 중인 응용 프로그램이 호스트의 포트 8080으로부터 접근 가능하도록 합니다.
___
# 프로젝트 후기
___
***저는***
___

## Skills

| Use Skills                        | reason to use            
|:----------------------------------|:---------------    
| [VSCode][11] and [intellij][12]   | Front and backend   
| [Java][21] and [JavaScript][22]   | 웹프로젝트 java 공부 
| [Oracle][31] and [MySql][32]      | 데이터베이스 사용    
| [Github][41] and [Notion][42]     | 협업을 위해서        
| [AWS][51] and [Docker][52]        | 사이트 배포중 가성비

Screenshots can be reused under [CC-BY-SA-4.0].

[11]: https://commons.wikimedia.org/wiki/File:IMac_vector.svg
[12]: https://commons.wikimedia.org/wiki/User:TheGoldenBox
[21]: https://commons.wikimedia.org/wiki/File:IPhone_6S_Rose_Gold.png
[31]: https://commons.wikimedia.org/wiki/File:IPad_Air_2.png
[32]: https://commons.wikimedia.org/wiki/User:Justinhu12

[CC-BY-SA-4.0]: https://creativecommons.org/licenses/by-sa/4.0/
[CC-BY-SA-3.0]: https://creativecommons.org/licenses/by-sa/3.0/
