웹과는 다르게 앱은 디바이스별로 크로스브라우징 해야하는데, 웹의 IE 할 때보다 더 막막하다.

사용자별로 최신기기 부터 사망하지 않는 갤2까지 엄청나게 다양한 기기와 다양한 플랫폼을 해야하기 때문..

그동안은 스택네비게이터가 헤더를 알아서 잡아줘서 별 생각없이 했었지만 오늘 만들 화면은 헤더가 없기 때문에 크로스브라우징에 신경써야 한다.



그 중 첫번째로 할 일이 앱 스크린의 안전한 영역을 구하는 것이다.

디바이스마다 상태바, 노치, 독바 등의 길이가 달라서 컨텐츠 영역의 높이값을 맞춰야 하기 때문이다.



리액트네이티브에는 `<SafeAreaView>` 라는 아주 좋은 내장 컴포넌트가 있는데 컴포넌트를 이 태그로 감싸면 디바이스의 안전한 영역(상태바, 노치 등에 가려지지 않고 온전히 스크린에 다 보이는 콘텐츠 영역)을 알아서 구해준다.

그러나 문제는 ios 11 이상에서만 지원되고 안드로이드에서는 사용할 수 없다. 그래서 외의 것들은 직접 안전한 영역을 구해줘야 하는데, 아이폰X헬퍼를 사용하면 된다.



- 설치: `yarn add react-native-status-bar-height`



아이폰도 버전별로 세이프뷰가 다른데 X 를 기점으로 노치가 생긴 것이 가장 큰 변화다. 그래서 각각 다르게 구해야 하는데 iphone x helper 를 사용하면 된다.

`react-native-iphone-x-helper` 는 iphone X 이후 버전인지 여부와 Home Indicator 의 Height 를 알려준다.

- 설치: `yarn add react-native-iphone-x-helper`

이 라이브러리의 `isIphoneX()` 함수는 아이폰 X 이후면 `true` 를 리턴한다. 먼저 디바이스 정보를 알아낸 후 기기의 height 에서 각각 다르게 빼주면 된다. 



- 아이폰 X 이전

기기의 height 에서 상태표시줄 높이 만큼 빼준다.

- 아이폰 X 이후

기기의 height - (상태표시줄 높이 + Home Indicator)



**[구현코드]**

```jsx
import React, {useState} from 'react';
import {View, Text, TextInput, StyleSheet, Dimensions} from 'react-native';
import {getStatusBarHeight} from 'react-native-status-bar-height';
import {isIphoneX, getBottomSpace} from 'react-native-iphone-x-helper';

function Index() {
	...

  const height = isIphoneX()
    ? Dimensions.get('window').height - getStatusBarHeight() - getBottomSpace()
    : Dimensions.get('window').height - getStatusBarHeight();
  ...

```







