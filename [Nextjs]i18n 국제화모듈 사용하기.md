사이트에 영문화가 필요할 때 i18n 국제화모듈을 많이들 사용하는데, 설정해주는게 여간 까다로운게 아니다

그러나 넥스트 사용자들에게는 기쁜 소식이 있다. Next.js 10 버전부터 i18n 을 내장모듈로 지원하기 시작하면서 다이나믹 라우팅을 통해 사용할 수 있게 되었기 때문에 아주 편해졌다!!

만약 Next 9 버전 이상을 사용하고 있다면 9, 10 버전이 큰 차이가 없기 때문에(공식 문서에 명시되어 있음) `npm install next@latest` 명령어를 통해 버전업 할 것을 추천한다.

https://nextjs.org/docs/advanced-features/i18n-routing

공식 문서를 참고해서 적용해보자.



### 1. `next.config.js` 파일에 i18n 설정 추가하기

```jsx
module.exports = {
	i18n: {
    locales: ["en", "ko"], // 지원하는 언어 리스트
    defaultLocale: "ko", // 디폴트 값
  },
}
```



### 2. Sub-path Routing, Domain Routing

- Sub-path Routing: url 에 서브패스로 해당 언어가 보이는 것.

  ```
   e.g.) 한글 사이트: [interneteye.co.kr/payment](<http://interneteye.co.kr/payment>) (디폴트로 설정된 값의 경우 패스가 보이지 않음)
  
            영어 사이트: interneteye.co.kr/en-us/payment
  ```

- Domain Routing: 뒤의 주소가 바뀌는 것

  e.g.) [~.com](http://~.com), [~.fr](http://~.fr), [~.nl](http://~.nl)

** 커머셜사이트처럼 규모가 큰 사이트가 아니면 웬만하면 서브패스라우팅을 사용하는 것이 좋다고 한다. 왜냐면 도메인이 바뀌면 여러모로 신경써야 할 것들이 많아지니까..

![웹팩설정캡쳐](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2023.20.21_UVhvCm.png?raw=true)



### 3. 언어 자동 감지

유저가 루트경로로 접근했을 시 넥스트에서는 헤더의 `Accept Language` 값을 통해 사용자의 locale 을 유추한다.

- Accept Language 값은 요청 HTTP 헤더의 기본값으로 어떤 언어를 클라이언트가 이해할 수 있는지, 지역 설정 중 어떤 것이 더 선호되는지를 알려준다. 사용자의 ip 에 기반하여 지역을 알려주는 것이 아니라, 사용자의 경험에 기반하여 사용자에게 더 친숙한 언어가 무엇인지를 알려주는 것이다.

다만 브라우저마다 표시되는 방법이 다른데, 크롬, FF, Safari, Edge, Whale 로 테스트했을 때 다음과 같이 나왔다.



(1) Chrome

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2023.29.07_VLiGmW.png?raw=true" alt="크롬헤더" style="zoom:50%;" />

(2) FireFox

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2023.35.53_ipTBAx.png?raw=true" alt="파이어폭스헤더" style="zoom:50%;" />

(3) Edge

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2023.38.30_KHoegu.png?raw=true" alt="엣지헤더" style="zoom:50%;" />

(4) Whale

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2023.48.31_5VIpWa-Whale.png?raw=true" alt="웨일" style="zoom:50%;" />

(5) Safari

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2023.32.34_wp8DLM.png?raw=true" alt="사파리헤더" style="zoom:50%;" />

- 크롬: `ko`, `ko-KR`
- 파이어폭스: `ko-KR`, `ko`
- 엣지: `ko`
- 사파리: `ko-kr`
- 웨일: `ko-KR`, `ko`

단순히 `ko` 만 들어가냐, 뒤에 `-kr` 까지 들어가냐, 대문자 혹은 소문자냐 차이인데 공통점은 `ko` 라는 글자가 모두 들어간다는 것이다.

만약 수동으로 언어를 탐지해서 변경해야 한다면 `ko` 라는 글자가 들어있냐로 판단하는 것이 좋을 것이다.

어쨌든 이 작업을 Next.js 10 에서 자동으로 해준다니 감사하다.

그런데 만약 이 자동감지 기능을 원하지 않는다면 웹팩 설정을 통해 꺼주면 되는데, 다음과 같이 `next.config.js` 를 수정하면 된다.

```jsx
i18n: {
    localeDetection: false, // 언어자동감지 - 기본값: true
  },
```



### 4. 언어정보 알아내기

`useRouter` 를 사용해 훅에서 언어정보를 알아낼 수 있다.

```jsx
import { useRouter } from "next/router";
const { locale, locales, defaultLocale } = useRouter();
```

또 서버단인 `getServerSideProps` 나 `getStaticProps` 내에서도 언어 정보를 알아낼 수 있는데 `context` 객체에서 가져오기만 하면 된다.

```jsx
const {
    req: {
      cookies: { user, lang, ie_a_t },
    },
    locale,
    locales,
    defaultLocale,
  } = context;
```

다만 그 전에 `next.config.js` 에 미리 설정해놔야 한다. undefined 가 나오면 설정하지 않은 것이다.

- locale: 현재 활성화된 언어값
- locales: 지원하는 모든 언어 리스트
- defaultLocale: 디폴트 언어값

공식 문서에서도 확인할 수 있다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2023.57.37_Bwc0N6.png?raw=true" alt="공식문서" style="zoom:50%;" />

[next/router | Next.js](https://nextjs.org/docs/api-reference/next/router#userouter)

### 5. 언어 전환하기

`next/link` 또는 `next/router` 를 통해 언어 전환을 할 수 있다.

- `next/link` 로 전환하기

넥스트의 <Link> 컴포넌트의 `locale` 속성 값을 지정하면 해당 언어의 서브패스로 이동하게 된다.

```jsx
import Link from 'next/link'

export default function IndexPage(props) {
  return (
    <Link href="/another" locale="fr">
      <a>To /fr/another</a>
    </Link>
  )
}
```

- `next/router` 로 전환하기

`router.push()` 의 인자로 `{ locale: 'ko` }` 와 같이 변경하고자 하는 언어값 객체를 같이 보내면 된다.

```jsx
import { useRouter } from 'next/router'

export default function IndexPage(props) {
  const router = useRouter()

  return (
    <div
      onClick={() => {
        router.push('/another', '/another', { locale: 'fr' })
      }}
    >
      to /fr/another
    </div>
  )
}
```

이렇게 하면 해당하는 다이나믹 라우팅이 동작하여 해당하는 언어의 url 로 리디렉트 시킬 수 있다.



### 6. 커스텀 쿠키 사용하여 헤더의 Accept-Language 값 덮어씌우기

헤더값을 사용하고 싶지 않을때에는 `NEXT_LOCALE` 이라는 이름의 커스텀 쿠키를 브라우저에 넣으면 된다.

만약 사용자의 헤더에 'ko' 가 나오지만 'en' 으로 설정을 덮어씌우고 싶다면 `NEXT_LOCALE=en` 이라는 쿠키를 추가하면 된다.



### 7. next-translate 플러그인과 함께 사용

nextjs 10 이상의 i18n 기능은 보통 `next-translate` 라는 플러그인과 함께 사용한다.

자세한 설명은 공식 문서를 보자.

[vinissimus/next-translate](https://github.com/vinissimus/next-translate)



(1) 설치

 `yarn add next-translate`



(2) 네임스페이스 파일 생성

`/locales` 폴더를 생성한 후 그 안에 각각 필요한 언어 폴더를 만들고 그 안에 `common.json` 이라는 json 파일을 만든다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-10%2009.07.19_jeIBkb.png?raw=true" alt="네임스페이스" style="zoom:50%;" />  

이 common.json 파일은 디폴트로 인식할 언어파일인데, 페이지별로 json 파일을 추가로 만들어 i18n.json 이라는 설정파일에서 페이지별 다른 파일을 읽어오게 할 수 있다.

{pagename.json} 이라는 파일명으로 추가하면 된다.



(3) 웹팩 설정 변경 및 `i18n.json` 설정파일 생성

- `/i18n.json` 파일 생성

루트폴더에 `i18n.json` 파일을 생성 후, 처음에 웹팩에 추가한 `locales`, `defaultLoclae` 설정들을 그대로 옮겨줄 것이다.

또 `pages` 값을 추가하여 페이지별로 어떤 언어파일을 읽게 할 것인지 설정할 수 있다.

언어 설정을 변경하고 나서 적용하려면 로컬서버를 종료 후 다시 실행해야 한다.

```jsx
{
  "locales": ["ko", "en"],
  "defaultLocale": "ko",
  "pages": {
    "*": ["common"],
    "/": ["home"],
    "/payment": ["payment"],
    "/product": ["product"],
    "/mypage": ["mypage"],
    "/company": ["company"]
  }
}
```

- `next.config.json` 설정 변경

이제 넥스트 웹팩 설정을 아래와 같이 변경해주자.

```jsx
const nextTranslate = require("next-translate");

module.exports = {
  ...nextTranslate(),
};
```

- 주의할 점: `i18n.json` 설정과 네임스페이스 파일의 폴더구조를 정확히 일치시키자.

!! 중요한 것은 반드시 설정파일에 명시된대로 폴더 구조를 만들어놔야 한다는 것이다.

예를 들어 locales 에 "en-US" 라고 지정해놓고 네임스페이스 폴더명은 "en" 로 해놓으면 넥스트에서 인식을 못한다. 이럴 경우 페이지는 i18n.json 에 설정된 en-US 로 이동하지만 네임스페이스 변수가 없는 것으로 인식하기 때문에 t 객체를 변수로 읽는 것이 아니라 텍스트로 읽게 된다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-10%2009.19.37_QgxSZL.png?raw=true" alt="잘못된예" style="zoom:50%;" />

네임스페이스 잘못 설정한 예

또 만약 언어 설정을 변경했는데 404 not found 로 이동한다면 해당 언어 설정을 안해줬거나 제대로 해주지 않은 것으로 `i18n.json` 의 `locales` 를 다시 확인해야 한다.



(4) 컴포넌트에 적용

- `useTranslation` 메소드 임포트
- `{ t }` 객체 이용하여 json 형식으로 텍스트 추가

```jsx
import useTranslation from "next-translate/useTranslation";

function Home(){
  let { t } = useTranslation("");
	return (
		<div>
			{t("common:greeting")}
       <br />
      {t("common:test")}			
		</div>
	)
}
```

`useTranslation` 메소드는 인자로 기본으로 찾을 네임스페이스를 받는다.

위 코드처럼 아무것도 설정하지 않았을 경우 JSX 에서 사용할  때 `{t("common:greeting")}` 처럼 읽어오고자 하는 네임스페이스 변수 앞에 파일명 "common:" 을 적어줘야 한다.

인자를 지정해줄 경우 아래와 같이 사용할 수 있다.

```jsx
let { t } = useTranslation("common");
...
{t("greeting")}
	<br />
{t("test")}
```

`useRouter` 메소드로 페이지 라우터 값을 가져와 변수로 설정하여 `useTranslattion` 의 인자로 넣어줘도 되겠다.



(5) 언어 전환 이벤트 추가

모든 설정을 마쳤으니 언어 전환 이벤트만 추가하면 되는데 아주 쉽다. 앞서 말한 `next/link` 나 `next/router` 를 이용하여 하면 되는데 만약 헤더에서 언어를 선택하는 식이라면 이런식으로 하면 된다.

```jsx
import { useRouter } from "next/router";

function Header() {
	// 라우터에서 언어 설정값 불러오기
	const router = useRouter();
	const { locale, locales, defaultLocale } = router;
	// 현재 언어 설정 지정
	const now_locale = locale || defaultLocale || locales[0];
	// 현재 패스(위치) 알기
	const { pathname } = router;

	// 언어 설정 전환 이벤트
	const onChangeLocale = useCallback((lang) => {
    router.push(pathname, pathname, { locale: lang });
  }, []);

	return (
		...
			<div>
		    {now_locale.toUpperCase()}
		  </div>
		  {locales.map((other_locale, i) => {
		    return (
		      <React.Fragment key={i}>
		        {now_locale !== other_locale && (
		          <button type="button" onClick={() => onChangeLocale(other_locale)}>
		            {other_locale.toUpperCase()}
		          </button>
		        )}
		      </React.Fragment>
		    );
		  })}
		)
		...
	}
```



먼저 `locale, locales, defaultLocale` 값을 `useRouter` 에서 비구조화 할당 해준 뒤, 현재 설정된 언어를 나타내는 `now_locale` 을 지정해준다. 뒤에 `|| defaultLocale` 을 해준 이유는 간혹 넥스트를 최초에 로딩하면 locale 이 undefined 로 나오는 경우가 있기 때문이다. 그리고 이렇게 해도 `defaultLocale` 이 undifined 가 나오는 경우가 있어서 뒤에 `|| locales[0]` 을 하나 더 붙여 리스트 중 가장 첫번째 언어가 나오도록 했다. (undifined 로 나오는 원인은 추후 더 알아보겠다.)

그리고 `router.push` 를 사용해 이동할거라면 현재 위치를 알기 위해 `pathname` 값을 추가로 받아온다.

(UI에 따라 다르겠지만)JSX 에서는 `now_locale` 을 가장 상단에 보여주고 뒤로 나머지 선택 가능한 언어들을 보여주기 위해 map 메소드를 이용해 `locales` 를 표시한다. 그리고 버튼의 onClick 이벤트로 `onChangeLocale` 를 걸었다. 이 함수는 보여주고 싶은 locale 값을 인자로 받아 해당 값으로 페이지 라우팅이 되게 하면 된다.



**[결과화면]**

로컬 서버에 테스트 결과 각각 defaultLocale 인 ko의 경우 `localhost:8080` 로, en의 경우 `localhost:8080/en` 로 잘 라우팅 되는 것을 확인할 수 있다.

번역 또한 잘 된다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-10%2009.47.19_U2P8Lc.png?raw=true" alt="한국어" style="zoom:50%;" />

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-10%2009.47.32_x3wK2r.png?raw=true" alt="영어" style="zoom:50%;" />

나머지 페이지들도 네임스페이스 파일을 만들어서 같은 방법으로 처리하면 페이지별로 언어 json 값들을 나눠 관리할 수 있고 어떤 언어들이 있는지 한눈에 알 수 있기 때문에 참 편리할 것이다.

이 외에도 컴포넌트에서 특정 텍스트를 지정하여 네임스페이스 파일에서 변수로 불러올 수도 있는데 자세한건 https://github.com/vinissimus/next-translate 을 참고하도록 하자.

(사실 그 전에는 하나의 language 파일에 페이지에 나오는 모든 언어를 object 로 다 때려박았었는데... 내용이 많아지다보니 파일이 엄청나게 길어지고 ctrl F 로 찾기에도 한계가 있으니까 영문화 할 때마다 너무 노가다같고 싫었다.)

이렇게 좋은 방법이 있었다니 이제라도 알아서 다행이다. 역시 끊임없이 발전하는 넥스트..! 마음에든다.







