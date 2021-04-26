## 3단계. 리액트 탭네비게이션, 스택네비게이션으로 Nested Navigation 만들기 (React Navigation 5.0 +)

이 글은 [Stack Navigation 만들기](https://velog.io/@ant-now/React-Native-with-TS-타입스크립트로-리액트네이티브-boilder-plate-만들기-2스택네비게이션-만들기-React-Navigation-5.0) 포스팅에 이어지는 글입니다.



<img src="https://i.imgur.com/JgLOveC.png" alt="최종 네비게이션 구조" style="zoom:50%;" />

이 보일러플레이트로 만들 최종 네비게이션 구조는 이렇습니다.

앞서 만든 Main, Details 스크린이 HomeStack 이라는 네비게이션으로 이루어져있고 HomeStack 과 Search, Community, News 스크린이 모두 bottom-tab-navigator 로 묶여있습니다.

즉, Stack Navigation 와 Bottom Tab Navigation 으로 이루어져 있는 Nested Navigation 을 구현하는 것이 최종 목표입니다.

이 포스팅은 이전 스택네비게이션 만들기의 연장선으로 방법이 똑같아서 상당 부분의 설명을 생략하였습니다.

그래도 감이 안오신다면 같이 한번 진행해봅니다.

----



#### [준비단계]

**(1) 의존성 설치**

​	`yarn add @react-navigation/bottom-tabs`

**(2) 메뉴 구조**

<img src="https://i.imgur.com/k7nbs3J.png" alt="메뉴구조" style="zoom:50%;" />

​	이전 스택네비게이터만 만들때보다 메뉴 구조가 조금 복잡해졌다. 하지만 과정은 똑같으니 겁먹지 말자.



#### [구현하기]

여기서부터는 먼저 완성된 코드를 주석과 함께 보여준 뒤, 각 코드를 쪼개서 설명해줄 것입니다. 급하시면 주석과 전체 코드만 보시면 됩니다. 중요한 것은 무슨 뜻인지 모르겠어도 그냥 따라하는 것입니다.



#### 1. `TabNavigators/index.tsx`

- 전체 코드

```tsx
import React from 'react';
// 필요한 모듈과 스크린 tsx 를 불러온다.
import {createBottomTabNavigator} from '@react-navigation/bottom-tabs';
import HomeStackNavigator from '../HomeStackNavigators'; // 처음에 만들어줬던 HomeStackNavigation
import SearchScreen from '../../screens/SearchScreen'; // 검색 스크린
import CommunityScreen from '../../screens/CommunityScreen'; // 커뮤니티 스크린

// Tab Navigation 에서 필요한 스크린은 3개 - HomeStack, 검색 스크린, 커뮤니티 스크린

// 1. 필요한 스크린에 대해 enum 타입을 정의한다. (리듀서에서 액션타입을 지정해주는 것 처럼)
export enum BottomTabs {
  Home = 'Home',
  Search = 'Search',
  Community = 'Community',
}

// 2. 각 스크린 마다 필요한 파라미터 타입 정의
export type BottomTabsParamList = {
  Home: undefined; // 아무런 파라미터도 필요 없을 경우 undefined
  Search: undefined;
  Community: undefined;
};

// 3. 방금 만든 타입을 createBottomTabNavigator 메소드 앞에 지정해주서 Tabs 네비게이터 객체를 만들어줌.
const Tabs = createBottomTabNavigator<BottomTabsParamList>();
const TabsNavigator: React.FunctionComponent = () => {
  return (
    <Tabs.Navigator>
      <Tabs.Screen
        name={BottomTabs.Home} // 처음에 enum 으로 지정했던 BottomTabs 에서 맞는 컴포넌트명을 가져온다.
        component={HomeStackNavigator} // 실제 보여주게 될 컴포넌트
      />
      <Tabs.Screen name={BottomTabs.Search} component={SearchScreen} />
      <Tabs.Screen name={BottomTabs.Community} component={CommunityScreen} />
    </Tabs.Navigator>
  );
};
export default TabsNavigator;
```



- 설명

**(1) 라우트를 enum 으로 정의하기**

​	가장 먼저 `TabsNavigators/index.tsx` 에서 필요한 모든 스크린에 대해 enum 타입으로 정의한다.

​	여기서는 앞서 스택 네비게이션으로 만들었던 Home, 검색바가 들어갈 Search, 커뮤니티 게시판이 들어갈 Community 3개를 우선 넣었다.

```tsx
// 1. 필요한 스크린에 대해 enum 타입을 정의한다. (리듀서에서 액션타입을 지정해주는 것 처럼)
export enum BottomTabs {
  Home = 'Home',
  Search = 'Search',
  Community = 'Community',
}
```

**(2) 각 스크린마다 필요한 파라미터 정의**

```tsx
// 2. 각 스크린 마다 필요한 파라미터 타입 정의
export type BottomTabsParamList = {
  Home: undefined; // 아무런 파라미터도 필요 없을 경우 undefined
  Search: undefined;
  Community: undefined;
};
```

**(3) Bottom Tab Navigator 객체 만들기**

​	`createBottomTabNavigator` 메소드로 스택네비게이션 객체를 만들어준다. 네비게이터를 만들 때 타입을 지정해줘야 하는데 바로 위에서 만든 `BottomTabsStackParamList` 타입을 지정해준다.

```tsx
// 3. 방금 만든 타입을 createBottomTabNavigator 메소드 앞에 지정해주서 Tabs 네비게이터 객체를 만들어줌.
const Tabs = createBottomTabNavigator<BottomTabsParamList>();
const TabsNavigator: React.FunctionComponent = () => {
  return (
    <Tabs.Navigator>
      <Tabs.Screen
        name={BottomTabs.Home} // 처음에 enum 으로 지정했던 BottomTabs 에서 맞는 컴포넌트명을 가져온다.
        component={HomeStackNavigator} // 실제 보여주게 될 컴포넌트
      />
      <Tabs.Screen name={BottomTabs.Search} component={SearchScreen} />
      <Tabs.Screen name={BottomTabs.Community} component={CommunityScreen} />
    </Tabs.Navigator>
  );
};
export default TabsNavigator;
```



#### 2. `SearchScreen/index.tsx`

- 전체 코드

```tsx
import React from 'react';
import {SafeAreaView, StyleSheet, Text, View, Button} from 'react-native';
import {BottomTabNavigationProp} from '@react-navigation/bottom-tabs';
// 아까 TabsNavigators 에서 export 해줬던 타입들을 가지고 온다.
import {BottomTabs, BottomTabsParamList} from '../../navigators/TabsNavigators';

// SearchScreen 에 필요한 파라미터들을 BottomTabNavigationProp 으로 타입 명시해준다.
type SearchScreenNavigationProps = BottomTabNavigationProp<
  BottomTabsParamList, // navigators/TabsNavigators/index.tsx 에서 지정했던 BottomTabsParamList
  BottomTabs.Search // enum 으로 지정했던 타입 중 Search 에 해당하는 부분
>;

// SearchScreenProps 에 대한 인터페이스 지정
// 인터페이스: 객체의 타입을 정의할 수 있게 하는 것
interface SearchScreenProps {
  navigation: SearchScreenNavigationProps; // 네비게이션 속성에 대한 타입으로 방금 지정해주었던 SearchScreenNavigationProps 을 지정
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

const SearchScreen: React.FunctionComponent<SearchScreenProps> = (props) => {
  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.welcomeContainer}>
        <Text style={styles.welcome}>Search Screen</Text>
      </View>
    </SafeAreaView>
  );
};
export default SearchScreen;
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