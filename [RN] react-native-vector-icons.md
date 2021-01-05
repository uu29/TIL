# [React Native] react-native-vector-icons

SVG 이미지를 아이콘 폰트로 사용하는 가장 흔한 방법

https://medium.com/@vimniky/how-to-use-vector-icons-in-your-react-native-project-8212ac6a8f06

##### 1. 설치:

 `npm install --save react-native-vector-icons` || `yarn add react-native-vertor-icons`



##### 2. 플랫폼 세팅

[Ios]

Ios 에서 사용하려면 X-code 에서 `info.plist` 수정해야 한다. 처음에는 조금 어려울 수 있으나 천천히 따라하기만 하면 된다.

- X-code 프로젝트로 `app>ios` 를 열어서 `info.plist` 라는 파일이 있는 곳에  `Fonts` 라는 새 그룹(폴더)를 만든다.

- `node_modules/react-native-vector-icons/Fonts/*.ttf` 폴더로 가면 여러 폰트파일이 있다. 이 중 원하는 폰트를 방금 만든 `Fonts` 폴더에 넣자. 이때, 폰트의 용도를 선택하는 폼이 나오는데 아무것도 선택하지 말고 설치하자. 선택하면 빌드가 안될 수가 있다.

- X-code 에서 `info.plist` 파일을 열어보자. Key-Value 로 된 여러 설정들이 나오는데 여기에서 새로운 키를 추가해준다.

  우선 `Fonts provided by application` 이라는 Array 를 만들고, 여기에 사용하고 싶은 폰트들을 String 값으로 추가해주면 되는데, 나는 MatetialIcon 만 필요해서 하나만 추가하였다.

  Array 안에 `Item 0` 이라는 이름의 String 을 추가해줬고 Value 는 아까 Fonts 그룹에 넣었던 폰트 이름과 똑같게 해주면 된다.

  마지막으로 우측 상단 재생 버튼을 눌러 다시 빌드해준다. (컴파일)

![Screenshot_2021-01-05 22.54.25_BgFNEy](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-05%2022.54.25_BgFNEy.png?raw=true)

[Android]

안드로이드에서 사용하려면 vsc 에서 바로 할 수 있는데, `android/app/build.gradle` 파일을 열어보자.

맨 끝에 다음 코드를 쳐준다.

```java
project.ext.vectoricons = [
    iconFontNames: [ 'MaterialIcons.ttf' ] // 아까 내가 넣었던 폰트 이름
]

apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"
```

![Screenshot_2021-01-05 20.39.17_zezgTl](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-05%2020.39.17_zezgTl.png?raw=true)

다음으로 `android/settings.gradle` 을 열고 아래 코드를 붙여넣는다.

```java
include ':react-native-vector-icons'
  project(':react-native-vector-icons').projectDir = new File(rootProject.projectDir, '../node_modules/react-		native-vector-icons/android')(/Users/yuriahn/TIL/images/Screenshot_2021-01-05 20.43.27_HZ3icy.png)
```

![](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-05%2020.43.27_HZ3icy.png?raw=true)

그 다음에는 `android/app/bundle.gradle` 파일의 dependencies 블록 안에 아래 코드를 붙여넣는다.

```java
compile project(':react-native-vector-icons')
```

이렇게 하면 모든 준비는 끝났다.



##### 3. 사용

우선 그냥 화면에 아이콘을 만들어본다. 아이콘폰트마다 태그의 이름과 프로퍼티의 name 값이 다르니 이는 공식 문서를 참고해야한다.

- `Ionicons.ttf`

```jsx
import React from 'react';
import {View, Text} from 'react-native';
import Ionicons from 'react-native-vector-icons/Ionicons';

function Favorites() {
  return (
    <View style={{flex: 1, justifyContent: 'center', alignItems: 'center'}}>
      <Ionicons size={35} name="ios-star-outline" color="#000000" />
      <Text>Favorites</Text>
    </View>
  );
}

export default Favorites;
```

임포트 했을 때 아무 오류가 없어야 하고 아이콘이 이렇게 잘 뜨면 제대로 적용이 된 것이다!

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-05%2023.01.53_ug19Jg.png?raw=true" alt="Screenshot_2021-01-05 23.01.53_ug19Jg" style="zoom:33%;" />



- `MaterialCommunityIcons.ttf`

```jsx
import React from 'react';
import {View, Text} from 'react-native';
import MaterialCommunityIcons from 'react-native-vector-icons/MaterialCommunityIcons';

function Favorites() {
  return (
    <View style={{flex: 1, justifyContent: 'center', alignItems: 'center'}}>
      <MaterialCommunityIcons size={35} name="star-outline" color="#000000" />
      <Text>Favorites</Text>
    </View>
  );
}

export default Favorites;	
```



주의할 점: 아이콘 name 이 틀려 아이콘을 찾지 못했을 경우 이렇게 물음표 아이콘이 나온다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-05%2023.03.49_YT2ZFS.png?raw=true" alt="Screenshot_2021-01-05 23.03.49_YT2ZFS" style="zoom:33%;" />



이쯤에서 의문점이 하나 생겼다. 내가 못찾는건지 아니면 없는건지... 대체 아이콘팩 공식 문서는 어디에 있는가 ㅜㅜ

일일이 때려맞추기도 어렵고.. https://materialdesignicons.com/ 이런 사이트들이 나오긴 하는데 검색할 수 없고 눈으로 다 찾아야 하는거라 여간 불편한게 아니다. 아마 방법이 있을 것 같은데 모르겠다.



##### 4. 탭 네비게이션 메뉴에 아이콘 적용하기

탭에 적용하는 것은 쉬운데, 리액트를 많이 해본 사람이라면 코드만 잘 보면 어떤 용도인지 이해할 수 있다.

먼저 `<Tab.Navigator>` 의 `screenOptions` props 을 설정해주는데 `tabBarIcon` , `tabBarOptions` 등이 있다. (이름만 보고 뭔지 예상해보자)

아이콘에 관한 것은 `tabBarIcon` 로 설정해주고 아까 예시에서 만들어봤던 아이콘 컴포넌트를 리턴해주면 된다.

그리고 탭바에 텍스트 색상, 라벨 숨기기 등과 같은 나머지 속성은 `tabBarOptions` 을 이용한다.

완성된 코드는 다음과 같다.

```jsx
import 'react-native-gesture-handler';
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import {createBottomTabNavigator} from '@react-navigation/bottom-tabs';
import * as Root from './src/root';
import MaterialCommunityIcons from 'react-native-vector-icons/MaterialCommunityIcons';

const Tab = createBottomTabNavigator();

function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator
        screenOptions={({route}) => ({
          tabBarIcon: ({focused, color, size}) => {
            let iconName;
            // focused: bool, 클릭했는지 여부

            switch (route.name) {
              case 'Search':
                iconName = 'magnify';
                break;
              case 'Community':
                iconName = 'forum-outline';
                break;
              case 'News':
                iconName = 'view-dashboard-outline';
                break;
              case 'Myinfo':
                iconName = 'account-circle-outline';
                break;
              default:
                iconName = 'star-outline';
            }
            return (
              <MaterialCommunityIcons
                size={size}
                name={iconName}
                color={color}
              />
            );
          },
        })}
        tabBarOptions={{
          activeTintColor: '#1B2228', // 활성화 되었을 때 색
          inactiveTintColor: '#C7CDD3', // 비활성화 색
          showLabel: false, // 텍스트 숨기기
        }}>
        <Tab.Screen name="Favorites" component={Root.Favorites} />
        <Tab.Screen name="Search" component={Root.Search} />
        <Tab.Screen name="Community" component={Root.Community} />
        <Tab.Screen name="News" component={Root.News} />
        <Tab.Screen name="Myinfo" component={Root.MyInfo} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}

// const styles = StyleSheet.create({});

export default App;
```



그리고 의도한 대로 이런 멋진 UI를 완성할 수 있었다!

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-05%2023.42.40_BRRK2U.png?raw=true" alt="Screenshot_2021-01-05 23.42.40_BRRK2U" style="zoom:33%;" />



아이콘 일일이 디자인하기 힘들고 귀찮을때는 이렇게 나보다 훨씬 직관적이고 깔끔하게 잘 만들어주는 패키지를 사용하면 될 것 같다.

다음에는 스택 네비게이터 각 페이지에 헤더를 적용하고 실제 메뉴를 구현해볼 것이다.