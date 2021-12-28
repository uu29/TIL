# [NextJS] Link 태그와 href의 관계

next.js에서 Link는 페이지 이동을 위해 꼭 써야 하는 컴포넌트다.

일반적으로 바닐라 자바스크립트에서는 `<a>` 태그로 쓰지만, next에서는 Link라는 컴포넌트를 제공한다.

Link 컴포넌트에 관한 자세한 내용은 공식 문서를 참고해보자. (https://nextjs.org/docs/api-reference/next/link)



Link 태그를 쓸 때 아주 간단하지만 중요한 것이 바로 `href` 속성이다.

다음의 두가지를 새겨두자.



#### 1. 일반적으로 `<Link>` 태그 안에 텍스트만 넣어도 `<a>` 태그가 자동으로 생성된다.

---

아래와 같이 다이나믹 라우팅을 활용한 간단한 Link 태그가 있다고 했을 때,

```typescript
<Link href={{ pathname: 'post', query: { id: post_id } }} passHref>
    jlory 블로그 입니다.
</Link>
```

실제 렌더링된 html 마크업은 이렇게 나온다.

![평범한next_link](https://i.imgur.com/fGHyeoB.png)

`<a>` 태그 없이 next.link 아래 string만 채워줬는데도 자동으로 `<a>` 태그가 생성됨을 알 수 있다.



그렇다면 Link 태그 하위로 커스텀 컴포넌트를 넣으면 next.js에서 어떻게 받아들일까?



#### 2. `<Link>`의 자식으로 커스텀 컴포넌트 - ex) styled component - 을 사용할 때 꼭 `passHref` 속성을 써주자.

---

Link의 스타일링을 위해 css-in-js로 styled component나 emotion을 많이 쓸 것이다.

이때 꼭 설정해줘야 하는 것이 `passHref`다.

**`passHref`는 next Link에서 하위 컴포넌트로 href 속성을 전달해주는 역할**을 한다.

`passHref`가 없을 때, 있을 때를 비교해보자.



- `passHref` 없을 때

  ```typescript
  <Link href={{ pathname: "post", query: { id: post_id } }}>
      <StyledA>jlory 블로그입니다.</StyledA>
  </Link>
  ```

![passHref 없을 때](https://i.imgur.com/6Z5xmj5.png)

`<Link>` 태그 하위로 `StyledA`라는 스타일드 컴포넌트를 전달했더니 마크업은 제대로 나오나, a 태그의 **href가 빠져있다.**

클릭해보면 잘 이동하긴 한다. 그러나 이렇게 되었을 때 몇가지 치명적인 점이 생겨난다.



#### Q. a 태그에 href가 빠지면 무슨 일이 발생하나요?

**(1) UX(사용자 경험)에 좋지 않아요.**

우선 해당 링크의 `Shift + Click | Ctrl + Click` 이 안된다. 새 창(또는 새 탭)으로 보기가 안된다. 이건 브라우저의 특징인 것 같은데, 보통 쉬프트나 컨트롤과 같이 누르면 새 창 또는 새 탭에서 링크를 열 수 있다. 그러나 href가 빠진 a 태그는 더이상 anchor text의 역할을 하지 않기 때문에 이동(라우팅)은 되지만, 새 창(탭)에서 열리지 않는다. 웹에서 링크를 마주쳤을 때 현재 탭에서 여는 유저라면 상관없겠지만, 나같이 항상 새 탭으로 열어야 하는 유저라면 사용자 경험이 좋을 수가 없다.

**(2) SEO에 치명적이에요.**

구글(을 포함한 검색엔진)이 사이트를 색인(인덱싱)할 때 새로운 페이지가 있는지 확인하는 두 가지 방법이 있는데, 하나는 사이트 맵(sitemap.xml)으로 하는 법, 나머지 하나는 **페이지 내 모든 `<a>` 태그를 찾아 방문**하는 방법이다. 이 때, 검색엔진은 `<a>` 태그의 **`href` 속성을 읽어 페이지 콘텐츠를 인덱싱** 한다.

따라서 href가 없는 링크는 (인덱싱이 필요한 페이지라면) 검색엔진이 추적할 수 없으니 SEO에 매우 안좋다고 할 수 있다.

[구글 검색센터](https://developers.google.com/search/docs/advanced/guidelines/links-crawlable?hl=ko)에서도 이렇게 명시하고 있다.

![올바른 a 태그 사용](https://i.imgur.com/Gi9ghjB.png)



(그리고 애초에 typescript를 사용한다면 이렇게 경고가 나온다.)

`passHref is missing. See https://nextjs.org/docs/messages/link-passhref`

![타입스크립트 경고](https://i.imgur.com/ypBR9iD.png)



passHref가 이렇게나 중요한 것이었다니!

이제 passHref가 있을 때 어떻게 되는지 직접 확인해보자.



- `passHref` 있을 때

```typescript
<Link href={{ pathname: "post", query: { id: post_id } }} passHref>
    <StyledA>jlory 블로그입니다.</StyledA>
</Link>
```

![href있을 때](https://i.imgur.com/BfEu6SH.png)



깔끔하게 `<a>` 태그에 href 속성이 생겼다.



웹표준을 지키면서, 시맨틱 마크업으로 링크를 구현하고 싶다면 네이버나 구글 등 대형 검색엔진 사이트를 참고해보자.



출처 & 참고

https://crong-dev.tistory.com/50

https://stackoverflow.com/questions/10510191/valid-to-use-a-anchor-tag-without-href-attribute

https://moz.com/learn/seo/anchor-text

https://developers.google.com/search/docs/beginner/seo-starter-guide

















