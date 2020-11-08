

# **1. HTTP Headers Security Analysis**

헤더 설정 확인(아래 중 편한 방법으로)

```bash
curl -I {YOUR-URL.com}
curl -k {YOUR-URL.com} -D curl_text.txt
cat curl_text.txt
curl -s -D- {YOUR-URL.com} | grep -i {KEYWORD}
```

<br>

## GDPR 항목에 각각 적용하기

#### (1) Strict-Transport-Security

```nginx
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload;"
# max-age | HSTS가 브라우저에 설정될 시간. 초단위로 설정함.
# includeSubdomains | HSTS가 적용될 도메인의 서브도메인까지 모두 적용함.
# preload: HSTS적용이 클라이언트측에서 preload로 이루어짐.
```

* 웹에 접속할 때 강제적으로 HTTPS Protocol로만 접속하게 하는 기능
* HTTP로 접속했을 때 302 Redirect를 이용하여 전환시켜줌

- max-age: HTTPS 리다이렉트를 강제하는 기간 | 0으로 설정시 비활성, HTTP접속 허용

![https://i0.wp.com/rsec.kr/wp-content/uploads/2017/07/2017-07-28_08-51-02.png?resize=447%2C245&ssl=1](https://i0.wp.com/rsec.kr/wp-content/uploads/2017/07/2017-07-28_08-51-02.png?resize=447%2C245&ssl=1)

- HSTS 적용여부 확인 툴: https://gf.dev/hsts-test 또는 curl명령어로 확인 가능

<br>

#### (2) X-Frame-Options

```nginx
add_header X-Frame-Options "SAMEORIGIN";
# deny | 모두 거부
# sameorigin | 동일한 사이트의 frame에서만 보여짐
# allow-from origin | 지정된 특정 uri의 frame에서만 보여짐
```

- 클릭재킹(clickjacking, 사용자가 웹 사이트의 링크 클릭 시 실제 보이는 것이 아닌 다른 것을 누르게 함) 공격 방어

- 옵션을 통해 브라우저가 <frame>,<iframe>,<object>태그를 렌더링/차단할지 여부를 알려줌

<br>

#### (3) X-XSS-Protection

```nginx
add_header X-XSS-Protection "1; mode=block";
# 0 | XSS 필터링 비활성화
# 1 | XSS공격 감지 시 안전하지 않은 영역 제거 후 렌더링
# 1; mode=block | XSS공격 감지 시 페이지 렌더링 자체를 중단
# 1; report={reporting-URI} | XSS공격 감지 시 페이지 렌더링 차단 후 CSP report-uri 지시문 기능 사용하여 보고함(Chromium ONLY)
```

- XSS공격 탐지시 페이지 로딩 중지

<br>

#### (4) X-Content-Type-Options

```nginx
add_header X-Content-Type-Options "nosniff";
```

- 지정된 MIME형식 이외의 다른 용도로 사용하고자 하는 것 차단

<br>

#### (5) Expect-CT

```nginx
add_header Expect-CT 'max-age=86400, enforce, report-uri="https://foo.example/report"'
# max-age | 브라우저가 Expect-CT 설정을 캐시하는 시간
# report-uri | 유효한 CT정보를 얻지 못하면 브라우저가 보고서를 보내는 곳(Optional)
# enforce | 브라우저가 보고서만 보낼지, 설정된 값에 위반되는지를 방문자에게도 알릴지 여부(Optional)
```

- 가짜 인증서 공격 방어

- 더이상 지원하지 않는 HKPK의 대체제(2018년 출시)

<br>

#### (6) Feature-Policy

```nginx
add_header Feature-Policy "autoplay 'none'; camera 'none'";
```

- 사이트가 브라우저에서 어떤 피쳐들을 사용할 수 있을지 정함

<br>

#### (7) Content-Security-Policy

```nginx
add_header Content-Security-Policy "default-src 'self' *.{YOUR-URL}.com;
script-src 'self''unsafe-inline' {domain.com};
style-src 'self' 'unsafe-inline' {domain.com};
img-src 'self' 'unsafe-inline' {domain.com};
font-src 'unsafe-inline' https://fonts.gstatic.com/ https://use.fontawesome.com/";
add_header Referrer-Policy "same-origin";
# default-src | 디폴트 설정
# connect-src | 연결할 수 있는 URL제한(ajax, websockets 등)
# script-src | 스크립트 관련 권한 제어
# child-src | iframe 태그에서 사용
# style-src | 스타일 시트 관련 권한 제어
# font-src | 웹 폰트를 제공하는 출처 지정
# img-src | 이미지 관련 권한 제어, data:;의 경우 base64로 인코딩된 이미지파일 허용
# report-uri | 콘텐츠 보안정책 위반 시 브라우저가 보고서를 보낼 uri
############## VALUES ##############
# uri | 접근 허용 도메인 (ex: https://developers.google.com/ https://*.cloudflare.com/)
# none | 모든 것을 차단
# *.{YOUR-URL}.com | 자기 자신과 서브 도메인까지 허용
# * | 모든 도메인 허용
# self | 자기자신(현재 도메인)만 허용, 서브 도메인은 제외됨
# unsafe-inline | 소스코드 내 인라인 자바스크립트 및 CSS 허용
```

- XSS 공격 완화 및 보호

- 패킷스니핑 공격 완화

- js, img, css 등을 CDN을 이용할 시 CDN 서버 주소를 꼭 적어줘야 함

<br>

#### (8) Referrer-Policy

```nginx
add_header Referrer-Policy "same-origin"
# no-referrer | 출처로 보내지 않음
# unsafe-url | default
# origin | 도메인만 전송(ex: https://noscam.co.kr)
# strict-origin | 대상 주소가 https일 때만 도메인만 전송(http인 곳에는 전송하지 않음)
# no-referrer-when-downgrade | 대상 주소가 https일 때만 전체 주소 전송(ex: https://noscam.co.kr/privacyleak)
# same-origin | 같은 홈페이지일 때만 전송(같은 웹사이트 내의 이동시에만)
# origin-when-cross-origin | 같은 홈페이지일 때는 전체 주소, 다른 사이트일 때는 도메인만 전송
# strict-origin-when-cross-origin | 같은 홈페이지일 때는 전체 주소, 다른 https 사이트로 갈 때는 도메인 주소만, http는 전송하지 않음
```

<br>

#### **[실제 적용 예시]**

위의 항목들을 모두 적용한 NGINX 설정은 다음과 같다.

(1) default파일에서 general_security_headers.conf를 상속받음

```nginx
server {
        listen 80;
        listen [::]:80;
 
        large_client_header_buffers  4 10k;
 
        root /var/www/html;
 
        index index.html index.htm index.nginx-debian.html;
 
			  server_name {YOUR-URL.com};
        include /etc/nginx/general_security_headers.conf;
 
        location / {
                try_files $uri @uwsgi;
                include /etc/nginx/general_security_headers.conf;
                #proxy_cookie_path / "/; secure; httpOnly; SameSite=None";
        }
 
        location @uwsgi {
                include uwsgi_params;
                uwsgi_pass unix:/tmp/uwsgi.sock;
                client_max_body_size 24M;
                client_body_buffer_size 128k;
        }
 
}
```

<br>

(2) 실제 설정파일은 /etc/nginx/general_security_headers.conf

```nginx
add_header Strict-Transport-Security "max-age=63072000; includeSubdomains; preload";
add_header X-Frame-Options "SAMEORIGIN";
add_header X-XSS-Protection "1; mode=block";
add_header X-Content-Type-Options "nosniff";
add_header Expect-CT "max-age=86400";
add_header Feature-Policy "autoplay 'none'; camera 'none'";
add_header Content-Security-Policy "default-src 'self' *.{YOUR-URL}.com; script-src 'self' 'unsafe-inline' {허용할 third-party library CDN}; img-src 'self' 'unsafe-inline' {허용할 third-party  source}; font-src 'unsafe-inline' {허용할 third-party library font-source}; frame-ancestors 'none'";
add_header Referrer-Policy "same-origin";
```

<br>

# **2. 서버 버전 노출하지 않기**

> ❗️ 사용중인 어플리케이션의 버전을 노출하는 것은 보안상 좋지 않다.

엔진엑스 버전 정보만 숨길 수도 있고, 서버 정보를 아예 감춰버리거나 이름을 바꿔버릴 수도 있다.

- 서버 버전 감추기 |  `server_tokens off;`

서버명을 바꾸려면 엔진엑스 익스트라 모듈을 설치해야 한다.

- 익스트라 모듈 설치 | `sudo apt-get install nginx-extras`
- 서버명 감추기(또는 바꾸기) |`more_set_headers "Server: {New_Server_Name}"`

```nginx
more_clear_headers Server;
server_tokens off;
```

<br>

curl -I 로 찍어보면 다음과 같이 서버 버전이 노출되는지 알 수 있다.

|                       on일 때(default)                       |                           off일 때                           |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![20200706_server_tokens_on](/Users/yuriahn/TIL/images/20200706_server_tokens_on.png) | ![20200706_server_tokens_off](/Users/yuriahn/TIL/images/20200706_server_tokens_off.png) |

<br>

# **3. 출처**

- [MDN HSTS 공식문서](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Strict-Transport-Security)
- [nginx HSTS 공식문서](https://www.nginx.com/blog/http-strict-transport-security-hsts-and-nginx/)
- [HSTS 설명](https://m.blog.naver.com/PostView.nhn?blogId=aepkoreanet&logNo=221575708943&proxyReferer=https:%2F%2Fwww.google.com%2F)
- [HSTS 설정방법](https://rsec.kr/?p=315)
- [HSTS 체크하는 방법](https://www.namecheap.com/support/knowledgebase/article.aspx/9711/38/how-to-check-if-hsts-is-enabled)
- [Nginx에서 X-Frame-Options 추가하기](https://geekflare.com/add-x-frame-options-nginx/)
- [MDN X-Frame-Options 공식문서](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-Frame-Options)
- MDN [X-XSS-Protection 공식문서](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/X-XSS-Protection)
- [MIME 형식의 보안위협 완화](https://webhack.dynu.net/?idx=20161120.001)
- [nginx 보안헤더 설정 시 주의점](https://puripia.com/10811/쇼핑몰-보안-http-보안-헤더security-headers-적용-時-주의점/)
- [HPKP와 Expect-CT](https://atinove.com/post/5e6d87f1d215fd0a9d45f005/)
- [구글 크롬, 내년부터 HPKP 지원하지 않는다](https://www.boannews.com/media/view.asp?idx=57786)
- [MDN CSP 공식문서](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [CSP 보안설정 티스토리 블로그](https://simjaejin.tistory.com/31)
- [CSP 개념과 우회방법](https://w01fgang.tistory.com/147)
- [CSP 설정 예시](https://ncube.net/content-security-policy-설정/)
- [HTML) Referrer 관리하기](https://hi098123.tistory.com/152)