## 2단계. 리액트 스택네비게이션 설치 및 구현하기(Add React Stack Navigation) (React Navigation 5.0 +)

이 글은 https://levelup.gitconnected.com/react-native-react-navigation-b27ab272e09b 을 보고 작성되었습니다.



여기서는 타입스크립트를 이용하여 아주 기본적인 리액트네이티브 스택네비게이션을 만들어보도록 한다.

시작하기에 앞서 React Navigation 은 버전마다 구현 방식이 아주 다른데 이 글은 5.0 이상 버전을 사용하였다.



먼저 결과 화면은 아래와 같다.

<img src="https://i.imgur.com/iq5iwOn.gif" alt="Jan-18-2021 20-25-28" style="zoom: 67%;" />

우리가 만들 스크린은 딱 두개다. Main, Details 스크린.

화면 하단에 More 버튼이 있고 버튼을 누르면 스택이 쌓이면서 Details 화면으로 넘어간다. 그리고 Details 에서 "<" 버튼을 눌러 메인으로 돌아올 수 있다.



#### [준비단계]

**(1) 의존성 설치**

​	`yarn add react-native-gesture-handler react-navigation react-native-safe-area-context react-native-screens @react-native-community/masked-view @react-navigation/native @react-navigation/stack`

**(2) RN 개발 의존성과 연결해주기 (RN 0.59 이하인 경우만)**

​	`react-native link react-native-gesture-handler`

**(3) 메뉴 구조**

<img src="https://i.imgur.com/OByF3zJ.png" alt="메뉴구조" style="zoom:50%;" />

​	메뉴 구조는 이렇다. `/src` 안에 앞으로 만들 모든 컴포넌트, 스크린, 스토어, 네비게이터가 들어간다.

​	`/src` 안에 먼저 네비게이터들이 들어갈 `navigators` 폴더와 스크린들이 들어갈 `screens` 폴더를 만들어주자. 그리고 각각 `navigators/HomeStackNavigators/index.tsx` , `screens/DetailsScreen/index.tsx` , `screens/MainScreen/index.tsx` 파일을 경로에 맞게 만들어준다.



#### [구현하기]

여기서부터는 먼저 완성된 코드를 주석과 함께 보여준 뒤, 각 코드를 쪼개서 설명해줄 것입니다. 급하면 주석과 전체 코드만 보시면 됩니다. 중요한 것은 무슨 뜻인지 모르겠어도 그냥 따라하는 것입니다.



#### 1. `HomeStackNavigation/index.tsx`

- 전체 코드

```tsx
import React from 'react';
// 필요한 모듈과 스크린 tsx 를 불러온다.
import {createStackNavigator} from '@react-navigation/stack';
import MainScreen from '../../screens/MainScreen'; // 메인스크린
import DetailsScreen, {DetailsParams} from '../../screens/DetailsScreen'; // 디테일스크린(주가정보)

// Home Screen 에서 필요한 스택은 2개 - 메인, 디테일

// 1. 필요한 스크린에 대해 enum 타입을 정의한다. (리듀서에서 액션타입을 지정해주는 것 처럼)
export enum HomeScreens {
  Main = 'Main',
  Details = 'Details',
}

// 2. 각 스크린 마다 필요한 파라미터 타입 정의
export type HomeStackParamList = {
  Main: undefined; // Main 스크린은 아무런 파라미터도 필요 없음
  Details: DetailsParams; // Details 스크린은 DetailsParams 라는 지정 타입의 파라미터가 필요함 => DetailsScreen 에서 지정할 것임.
};

// 3. 방금 만든 타입을 createStackNavigator 메소드 앞에 지정해주서 HomeStack 네비게이터 객체를 만들어줌.
const HomeStack = createStackNavigator<HomeStackParamList>();
const HomeStackNavigator: React.FunctionComponent = () => {
  return (
    <HomeStack.Navigator>
      <HomeStack.Screen
        name={HomeScreens.Main} // 처음에 enum 으로 지정했던 HomeScreens 에서 맞는 컴포넌트명을 가져온다.
        component={MainScreen} // 실제 보여주게 될 컴포넌트
      />
      <HomeStack.Screen name={HomeScreens.Details} component={DetailsScreen} />
    </HomeStack.Navigator>
  );
};
export default HomeStackNavigator;
```



- 설명

**(1) 라우트를 enum 으로 정의하기**

​	가장 먼저 `HomeStackNavigator/index.tsx` 에서 필요한 모든 스크린에 대해 enum 타입으로 정의한다.

​	enum 에 관한 자세한 글은 https://medium.com/@seungha_kim_IT/typescript-enum%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-3b3ccd8e5552 을 참고바란다.

​	무슨 뜻인지 잘 모르겠어도 그냥 따라하자. 라우팅에 필요한 스크린의 이름을 미리 지정하는 것인데, 리덕스에서 액션 타입을 지정하는 것과 비슷하다고 보면 된다.

```tsx
// 1. 필요한 스크린에 대해 enum 타입을 정의한다. (리듀서에서 액션타입을 지정해주는 것 처럼)
export enum HomeScreens {
  Main = 'Main',
  Details = 'Details',
}
```

**(2) 각 스크린마다 필요한 파라미터 정의**

​	각 스크린마다 props 를 가지는 것이 있고 필요없는 것이 있다. 이 때 스택의 파라미터 리스트라는 타입을 미리 export 해야하는데 여기서 지정해놓은 타입은 나중에 스크린에서 `StackNavigationProp` 로 쓰이게 된다.

​	내가 만들 앱은 Home 화면은 아무 props 도 필요없고 Details 화면은 symbol 이라는 string 값을 넘길 예정이다. Details 에 넘길 props 에 대한 자세한 타입은 Details 스크린에서 `DetailsParams` 라는 타입으로 지정해줄 것이기 때문에 여기서는 앞으로 만들게 될 타입만 명시해준다.

​	해당 스크린에 따로 파라미터가 필요없다면 `undefined` 라고 써주면 된다.

```tsx
// 2. 각 스크린 마다 필요한 파라미터 타입 정의
export type HomeStackParamList = {
  Main: undefined; // Main 스크린은 아무런 파라미터도 필요 없음
  Details: DetailsParams; // Details 스크린은 DetailsParams 라는 지정 타입의 파라미터가 필요함 => DetailsScreen 에서 지정할 것임.
};
```

**(3) Stack Navigator 객체 만들기**

​	`createStackNavigator` 메소드로 스택네비게이션 객체를 만들어준다. 네비게이터를 만들 때 타입을 지정해줘야 하는데 바로 위에서 만든 `HomeStackParamList` 타입을 지정해준다.

​	그 다음에는 React.FunctionComponent 로 tsx 형식으로 리턴할 것이다.

​	구조를 잘 보면 이렇게 되어있다.

> ​	<네비게이터>
>
> ​			<스크린 네임={스크린 이름} 컴포넌트={들어갈 컴포넌트}/>
>
> ​			<스크린 네임={스크린 이름} 컴포넌트={들어갈 컴포넌트}/>	
>
> ​	</네비게이터>

​	여기서 스크린 네임에 해당하는 부분에 enum 으로 지정한 스크린 이름을 써주면 된다. 컴포넌트에는 앞으로 만들게 될 스크린 컴포넌트를 넣어준다.

```tsx
// 3. 방금 만든 타입을 createStackNavigator 메소드 앞에 지정해주서 HomeStack 네비게이터 객체를 만들어줌.
const HomeStack = createStackNavigator<HomeStackParamList>();
const HomeStackNavigator: React.FunctionComponent = () => {
  return (
    <HomeStack.Navigator>
      <HomeStack.Screen
        name={HomeScreens.Main} // 처음에 enum 으로 지정했던 HomeScreens 에서 맞는 컴포넌트명을 가져온다.
        component={MainScreen} // 실제 보여주게 될 컴포넌트
      />
      <HomeStack.Screen name={HomeScreens.Details} component={DetailsScreen} />
    </HomeStack.Navigator>
  );
};
export default HomeStackNavigator;
```



#### 2. `Main/index.tsx`

- 전체 코드

```tsx
import React, {useState} from 'react';
import {SafeAreaView, StyleSheet, Text, View, Button} from 'react-native';
import {StackNavigationProp} from '@react-navigation/stack';
// 아까 HomeStackNavigator 에서 export 해줬던 타입들을 가지고 온다.
import {
  HomeScreens,
  HomeStackParamList,
} from '../../navigators/HomeStackNavigators';

// MainScreen 에 필요한 파라미터들을 StackNavigationProp 으로 타입 명시해준다.
type MainScreenNavigationProps = StackNavigationProp<
  HomeStackParamList, // navigators/HomeStackNavigators/index.tsx 에서 지정했던 HomeStackParamList
  HomeScreens.Main // enum 으로 지정했던 타입 중 Main 에 해당하는 부분
>;

// MainScreenProps 에 대한 인터페이스 지정
// 인터페이스: 객체의 타입을 정의할 수 있게 하는 것
interface MainScreenProps {
  navigation: MainScreenNavigationProps; // 네비게이션 속서에 대한 타입으로 방금 지정해주었던 MainScreenNavigationProps 을 지정
}

const styles = StyleSheet.create({
  btnNextContainer: {
    alignSelf: 'flex-end',
  },
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'space-between',
    margin: 10,
  },
  welcome: {
    fontSize: 30,
  },
  welcomeContainer: {
    alignItems: 'center',
    justifyContent: 'center',
    flex: 1,
    width: '100%',
  },
});

const MainScreen: React.FunctionComponent<MainScreenProps> = (props) => {
  const {navigation} = props;
  const initialSymbol: string = '삼성전자';
  const [symbol, setSymbol] = useState<string>(initialSymbol);
  // MainScreenProps 에 navigation 이 있으니까 비구조화 할당으로 꺼내쓸 수 있음
  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.welcomeContainer}>
        <Text style={styles.welcome}>Main Screen</Text>
      </View>
      <View style={styles.btnNextContainer}>
        <Button
          title="More"
          onPress={() => navigation.navigate(HomeScreens.Details, {symbol})}
          // More 버튼을 누르면 HomeScreens 로 이동함.
        />
      </View>
    </SafeAreaView>
  );
};
export default MainScreen;
```



- 설명

**(1) 스택 네비게이션에서 지정해줬던 타입 가져와서 지정해주기**

​	까먹었겠지만 HomeStackNavigator 를 만들 때 HomeScreens 와 HomeStackParamList 라는 enum 과 type 을 export 해줬었다. 그걸 그대로 가져와서 여기에 쓴다.

​	그리고 StackNavigationProps 에 필요한 타입들을 명시해준다.

```tsx
// 아까 HomeStackNavigator 에서 export 해줬던 타입들을 가지고 온다.
import {
  HomeScreens,
  HomeStackParamList,
} from '../../navigators/HomeStackNavigators';

// MainScreen 에 필요한 파라미터들을 StackNavigationProp 으로 타입 명시해준다.
type MainScreenNavigationProps = StackNavigationProp<
  HomeStackParamList, // navigators/HomeStackNavigators/index.tsx 에서 지정했던 HomeStackParamList
  HomeScreens.Main // enum 으로 지정했던 타입 중 Main 에 해당하는 부분
>;
```

**(2) 인터페이스 정의**

​	인터페이스는 객체의 타입을 정의하는 것으로 타입과 비슷한 역할이라고 보면 되는데, 여기서는 Main 이라는 스크린에 필요한 props 를 미리 인터페이스로 지정해준다. 방금 명시한 `MainScreenNavigationProps` 를 지정해준다.

```tsx
// MainScreenProps 에 대한 인터페이스 지정
// 인터페이스: 객체의 타입을 정의할 수 있게 하는 것
interface MainScreenProps {
  navigation: MainScreenNavigationProps; // 네비게이션 속성에 대한 타입으로 방금 지정해주었던 MainScreenNavigationProps 을 지정
}
```

**(3) tsx 내보내기**

​	방금 만들어준 `MainScreenProps` 인터페이스를 타입으로 받는 React.FunctionComponent 를 만들어준다.

​	이 안에서 해야할 일은 많이 없고, `useState` 로 지정한 `symbol` 값을 받아 이 값을 Details 스크린의 props 로 넘기는 이벤트만 작성해주면 된다.

​	간단하게 하단 버튼을 눌렀을 때 `onPress` 이벤트에 네비게이션 메소드만 넣어주면 된다.

​	네비게이션은 `navigation.navigate({이동할 스크린명}, {파라미터})` 의 문법으로 작성하면 된다.

```tsx
const MainScreen: React.FunctionComponent<MainScreenProps> = (props) => {
  const {navigation} = props;
  const initialSymbol: string = '삼성전자';
  const [symbol, setSymbol] = useState<string>(initialSymbol);
  // MainScreenProps 에 navigation 이 있으니까 비구조화 할당으로 꺼내쓸 수 있음
  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.welcomeContainer}>
        <Text style={styles.welcome}>Main Screen</Text>
      </View>
      <View style={styles.btnNextContainer}>
        <Button
          title="More"
          onPress={() => navigation.navigate(HomeScreens.Details, {symbol})}
          // More 버튼을 누르면 HomeScreens 로 이동함.
        />
      </View>
    </SafeAreaView>
  );
};
export default MainScreen;
```



#### 3. `Details/index.tsx`

- 전체 코드

```tsx
import React from 'react';
import {SafeAreaView, StyleSheet, Text, View} from 'react-native';
import {
  HomeScreens,
  HomeStackParamList,
} from '../../navigators/HomeStackNavigators';
import {StackNavigationProp} from '@react-navigation/stack';

type DetailsScreenNavigationProps = StackNavigationProp<
  HomeStackParamList,
  HomeScreens.Details
>;

// ~/src/navigators/HomeStackNavigators/index.tsx 에서 2번 각 스크린 마다 필요한 파라미터 타입 정의해줄 때 Details 스크린에 필요한 props 로 지정해줬었음.
export type DetailsParams = {
  symbol: string; // DetailsScreen 에는 symbol 이라는 이름의 string 타입의 파라미터가 필요하다.
};

// DetailsScreen Props 의 타입들을 지정. (리액트에서 proptypes 지정하는 것 처럼)
interface DetailsScreenProps {
  route: {params: DetailsParams}; // 루트의 파라미터로 방금 지정해준 DetailsParams 타입이 온다.
  navigation: DetailsScreenNavigationProps;
}

const styles = StyleSheet.create({
  btnLoginContainer: {
    alignSelf: 'center',
  },
  container: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'space-between',
    margin: 10,
  },
  txtSignupScreen: {
    fontSize: 30,
  },
  txtSignupScreenContainer: {
    flex: 1,
    alignItems: 'center',
    justifyContent: 'center',
  },
  txtSymbol: {
    fontSize: 25,
    color: 'grey',
  },
});

const DetailsScreen: React.FunctionComponent<DetailsScreenProps> = (props) => {
  const {navigation, route} = props;
  const {params} = route;
  const {symbol} = params;
  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.txtSignupScreenContainer}>
        <Text style={styles.txtSignupScreen}>DetailsScreen</Text>
        <Text style={styles.txtSymbol}>{symbol}</Text>
      </View>
    </SafeAreaView>
  );
};
export default DetailsScreen;
```



- 설명

여기서는 위의 Main 스크린에서와 거의 동일한 방법으로 진행하면 된다. 다만 Details 스크린에서는 Main 스크린과는 다르게 `symbol` 이라는 이름의 파라미터를 받을 것이다. 이를 위해서 `DetailsParams` 라는 타입을 export 해주고 인터페이스로 지정해야 한다.

**(1) Main 스크린으로부터 파라미터로 넘겨받을 타입 지정하기 **

```tsx
// ~/src/navigators/HomeStackNavigators/index.tsx 에서 2번 각 스크린 마다 필요한 파라미터 타입 정의해줄 때 Details 스크린에 필요한 props 로 지정해줬었음.
export type DetailsParams = {
  symbol: string; // DetailsScreen 에는 symbol 이라는 이름의 string 타입의 파라미터가 필요하다.
};
```

**(2) 인터페이스 작성하기**

방금 export 해주었던 `DetailsParams` 를 포함하는 인터페이스를 작성해준다.

```tsx
// DetailsScreen Props 의 타입들을 지정. (리액트에서 proptypes 지정하는 것 처럼)
interface DetailsScreenProps {
  route: {params: DetailsParams}; // 루트의 파라미터로 방금 지정해준 DetailsParams 타입이 온다.
  navigation: DetailsScreenNavigationProps;
}
```



#### 4. `App.tsx`

마지막으로 처음에 만들었던 HomeStackNavigator 를 가져와서 NavigationContainer 로 감싸서 export 해주기만 하면 된다.

```tsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import HomeStackNavigatoer from './navigators/HomeStackNavigators';

export default function App() {
  return (
    <NavigationContainer>
      <HomeStackNavigatoer />
    </NavigationContainer>
  );
}
```



이렇게 하면 기본 스택네비게이션 화면이 완성된다.

<img src="https://i.imgur.com/Wsfj11z.png" alt="완성 메인스크린" style="zoom: 33%;" />

<img src="https://i.imgur.com/tGkdA3n.png" alt="완성 디테일 스크린" style="zoom:33%;" />



차근차근 따라하니까 생각보다 어렵지 않다.

다음 글에서는 여기서 좀 더 나아가서 스택네비게이션 + 탭네비게이션이 조합된 Nested Navigation 을 만들어보겠다.