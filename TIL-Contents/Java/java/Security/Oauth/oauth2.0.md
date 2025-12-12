# Oauth 구현

## 지금 프로젝트에서
> 지금까지는 JWT를 통해서 회원가입, 로그인, refreshToken 재발급 등 모든걸 구현했는데

여기에 추가로 Oauth를 붙이려는 상황이다.

`그런데` 블로그 정보들을 찾아보니 맨 처음부터 Oauth기준으로 JWT를 생성하고
구현하는것 밖에 존재하지 않는것이다.

### 그래서..
> 그래서 결국 GPT한테 현상황을 문의 해보았다..
그에 대한 답변으로

```
👉 “자체 로그인 + JWT + RefreshToken + WebSocket JWT 인증”이 이미 매우 잘 정리된 상태입니다.
여기에 **카카오 OAuth2만 “추가”**하려면 기존 JWT 구조는 유지하고,
OAuth2는 **‘로그인 수단 하나 더 추가’**하는 개념으로 붙이는 게 정답입니다.

아래를 순서대로 보시면 됩니다.
(중간에 “왜 이렇게 하는지” 이유도 같이 설명드리겠습니다)

0️⃣ 결론부터 요약 (중요)

✔ 기존 JWT / RefreshToken / Filter / UserDetails → 그대로 유지
✔ OAuth2는 Spring Security OAuth2 Client만 추가
✔ OAuth 로그인 성공 시

DB에 회원 저장 (또는 기존 회원 조회)

AccessToken + RefreshToken 발급

이후부터는 지금과 완전히 동일하게 JWT 기반

즉,

[일반 로그인] → JWT 발급 → 기존 구조
[카카오 로그인] → JWT 발급 → 기존 구조


JWT가 “최종 인증 수단”입니다.

1️⃣ 전체 OAuth2 로그인 흐름 (중요 개념)

카카오 OAuth2는 Spring Security가 인증만 대신 처리합니다.

1. 프론트 → /oauth2/authorization/kakao
2. 카카오 로그인 화면
3. 카카오 → /login/oauth2/code/kakao (redirect)
4. Spring Security OAuth2 인증 성공
5. 우리가 만든 SuccessHandler 실행
6. 여기서:
   - 회원 가입/조회
   - JWT 발급
   - RefreshToken 저장
   - 응답 or 리다이렉트


👉 JWT 발급 위치는 “OAuth2 성공 핸들러”

2️⃣ 의존성 추가
implementation 'org.springframework.boot:spring-boot-starter-oauth2-client'


(기존 security, jwt, jpa 의존성 그대로)

3️⃣ application.yml (카카오 설정)
spring:
  security:
    oauth2:
      client:
        registration:
          kakao:
            client-id: ${KAKAO_CLIENT_ID}
            client-secret: ${KAKAO_CLIENT_SECRET}
            redirect-uri: "{baseUrl}/login/oauth2/code/kakao"
            authorization-grant-type: authorization_code
            scope:
              - profile_nickname
              - account_email
            client-authentication-method: post
        provider:
          kakao:
            authorization-uri: https://kauth.kakao.com/oauth/authorize
            token-uri: https://kauth.kakao.com/oauth/token
            user-info-uri: https://kapi.kakao.com/v2/user/me
            user-name-attribute: id

4️⃣ 핵심 ① OAuth2UserService (카카오 사용자 정보 파싱)
@Service
@RequiredArgsConstructor
public class KakaoOAuth2UserService
        extends DefaultOAuth2UserService {

    private final MemberRepository memberRepository;

    @Override
    public OAuth2User loadUser(OAuth2UserRequest userRequest)
            throws OAuth2AuthenticationException {

        OAuth2User oauth2User = super.loadUser(userRequest);

        Map<String, Object> attributes = oauth2User.getAttributes();
        Map<String, Object> kakaoAccount =
                (Map<String, Object>) attributes.get("kakao_account");

        String email = (String) kakaoAccount.get("email");

        Member member = memberRepository.findByEmail(email)
                .orElseGet(() -> memberRepository.save(
                        Member.builder()
                                .email(email)
                                .role(Role.USER)
                                .provider(AuthProvider.KAKAO)
                                .build()
                ));

        return new CustomOAuth2User(member, attributes);
    }
}

5️⃣ CustomOAuth2User (중요)

JWT용 CustomUserDetails와 역할이 다릅니다

@Getter
public class CustomOAuth2User implements OAuth2User {

    private final Member member;
    private final Map<String, Object> attributes;

    public CustomOAuth2User(Member member, Map<String, Object> attributes) {
        this.member = member;
        this.attributes = attributes;
    }

    @Override
    public Map<String, Object> getAttributes() {
        return attributes;
    }

    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        return List.of(new SimpleGrantedAuthority(member.getRole().name()));
    }

    @Override
    public String getName() {
        return member.getEmail();
    }
}

6️⃣ 핵심 ② OAuth2 성공 핸들러 (JWT 발급 위치)
@Component
@RequiredArgsConstructor
public class OAuth2SuccessHandler
        extends SimpleUrlAuthenticationSuccessHandler {

    private final JWTUtil jwtUtil;
    private final RefreshTokenService refreshTokenService;

    @Override
    public void onAuthenticationSuccess(
            HttpServletRequest request,
            HttpServletResponse response,
            Authentication authentication) throws IOException {

        CustomOAuth2User oauthUser =
                (CustomOAuth2User) authentication.getPrincipal();

        String email = oauthUser.getMember().getEmail();
        String role = oauthUser.getMember().getRole().name();

        String accessToken = jwtUtil.createAccessToken(email, role);
        String refreshToken = jwtUtil.createRefreshToken(email);

        refreshTokenService.saveRefreshToken(email, refreshToken);

        ResponseCookie cookie = ResponseCookie.from("refreshToken", refreshToken)
                .httpOnly(true)
                .secure(false)
                .path("/")
                .sameSite("Strict")
                .maxAge(60 * 60 * 24 * 7)
                .build();

        response.setHeader(HttpHeaders.SET_COOKIE, cookie.toString());
        response.sendRedirect(
                "http://localhost:3000/oauth/callback?accessToken=" + accessToken
        );
    }
}


✔ 여기서 JWT 구조 그대로 재사용
✔ AuthController의 login과 완전히 같은 역할

7️⃣ SecurityConfig 수정 (중요 포인트)

👉 기존 JWT 필터 절대 삭제 ❌

@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    return http
            .csrf(AbstractHttpConfigurer::disable)
            .sessionManagement(session ->
                    session.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            .authorizeHttpRequests(auth -> auth
                    .requestMatchers(
                            "/oauth2/**",
                            "/login/oauth2/**"
                    ).permitAll()
                    .anyRequest().authenticated()
            )
            .oauth2Login(oauth -> oauth
                    .userInfoEndpoint(userInfo ->
                            userInfo.userService(kakaoOAuth2UserService)
                    )
                    .successHandler(oAuth2SuccessHandler)
            )
            .addFilterBefore(
                    new JwtAuthorizationFilter(jwtUtil, userDetailsService),
                    UsernamePasswordAuthenticationFilter.class
            )
            .build();
}

8️⃣ WebSocket / JwtAuthorizationFilter 영향?

✔ 전혀 영향 없음

카카오 로그인 후 발급된 JWT는
→ 기존 JwtAuthorizationFilter
→ 기존 CustomPrincipalHandshakeHandler
→ 완벽히 동일하게 동작

9️⃣ 정리 (중요)

✔ 지금 구조는 OAuth2 붙이기 최적 상태
✔ OAuth는 로그인 방식 추가일 뿐
✔ JWT가 최종 인증 수단
✔ 기존 코드 삭제/변경 거의 없음

🔥 다음 단계 (원하시면)

카카오 최초 로그인 시 추가 정보 입력 플로우

OAuth 전용 회원 탈퇴 / 연동 해제

OAuth + 일반 로그인 계정 병합 전략

모바일 앱 OAuth 대응

필요하시면 프론트 요청 URL부터 실제 통신 흐름까지 같이 그려드릴게
```