## 기존 UserAgent 속성에 대해 자세히 알 수 있는 사이트

[UserAgentString.com - unknown version](http://useragentstring.com/)



## 왜 변경하는거야?

- 개인정보보호 이슈 (광고주가 정보 수집을 위해 마음대로 빼가는 경우가 많았음)
- 브라우저에 따라 콘텐츠를 다르게 보여준다? 브라우저에 상관없이 동일한 콘텐츠를 볼 수 있는 소비자의 권리
- 보안상의 문제



## 구글의 정책 변경: UserAgent 가 가고 Client Hints 가 온다

**[Client Hints] 를 사용하여 달라지는 점**

- UA에 이전처럼 많은 정보를 담지 않음
- string이 아닌 UserAgentData 에서 객체 형식으로 제공해줌
- 기존 UA에서 제공했던 구체적인 정보는 비동기 방식으로 구현
- 리퀘스트 날릴 때 CH 헤더를 설정하거나 메타태그를 설정함으로서 가져올 수 있음
- `UserAgentData`, `Client Hints` 둘 다 Https 에서만 적용됨

[NAVER D2](https://d2.naver.com/helloworld/6532276)

[UA 대신에 Client Hints 사용해보기](https://frontdev.tistory.com/entry/UA-대신에-Client-Hints-사용해보기)



## 크롬 Experiments 사이트에서 설정을 바꿔 테스트해보기

1. chrome://flags/ 로 접속

2. `Experimental Web Platform features`, `Freeze User-Agent request header` 를 모두 Enabled 로 바꾸고 테스트

   

- 바뀐 UserAgent :

전: `Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.4280.67 Safari/537.36`

후: `Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/87.0.0.0 Safari/537.36`

!! Mac 의 흔적이 온데간데없이 사라지고 Windows 로 둔갑,,



- 높은 엔트로피에 해당하는 UA 정보 비동기로 가져오는 예시

```javascript
navigator.userAgentData.getHighEntropyValues([  
  "architecture",
  "model",
  "platform",
  "platformVersion",
  "uaFullVersion",
]).then(info => {
  console.log(info);
});
```