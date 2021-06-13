# [RN] react-native-seoul 카카오 로그인

**1. 의존성: 리액트 네이티브 서울 카카오 설치하기**

`yarn add @react-native-seoul/kakao-login`

**2. Info.plist 설정하기**

- 앱 실행 허용 목록에 카카오 등록

![](https://i.imgur.com/Y9VsfhS.png)

- URL Types에 `URL Schemes` 등록

카카오 네이티브 앱 키를 URL 스키마에 등록한다. 이 설정은 커스텀 스킴 생성 시 사용된다.

`kakao{NATIVE_APP_KEY}` 형식으로 등록해준다.

<img src="https://i.imgur.com/2mzNXVP.png" style="zoom:80%;" />

- `info.plist` 코드에서 확인

여기까지 설정하면 `info.plist` 에서 다음과 같이 카카오 관련 설정이 추가된 것을 확인할 수 있다.

<img src="https://i.imgur.com/nIbZ5np.png" style="zoom:50%;" />

- Xcode에서 빌드해주기

설정이 끝나면 Xcode에서 빌드를 한번 더 해준다.

**3. Swift Bridge Header 파일 만들기**

(빌드 에러 방지)프로젝트 루트 폴더에 file > new file, Swift File을 생성해준다.

파일명은 `SwiftBridge` 라고 작성하면 자동으로 스위프트 브릿징 헤더를 만들거냐는 팝업창이 뜨는데, 확인을 눌러주면 된다.

파일 생성 후 다시 앱을 빌드해준다.

![](https://i.imgur.com/m7XHZ8H.png)

**4. `AppDelegate.m`에 코드 추가 하기**

```swift
#import <RNKakaoLogins.h>

- (BOOL)application:(UIApplication *)app
     openURL:(NSURL *)url
     options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options {
 if([RNKakaoLogins isKakaoTalkLoginUrl:url]) {
    return [RNKakaoLogins handleOpenUrl: url];
 }

 return NO;
}
```

참고: https://github.com/react-native-seoul/react-native-kakao-login/issues/193

