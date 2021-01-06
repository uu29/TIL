# [React Native] Navigation

- 기본 패키지: `yarn add react-navigation`

리액트 네이티브 앱의 대표적인 메뉴 UI 인 상단 헤더, 하단 탭메뉴, 햄버거 메뉴(슬라이드 메뉴)의 구현은 모두 네비게이션으로 한다.

이 중 상단 헤더는 Stack Navigation, 하단 탭메뉴는 Tab Navigation, 슬라이드 메뉴는 Drawer Navigation 로 구현한다.

모두 화면 전환 애니메이션이 다른데 스택네비게이션은 레이어가 겹겹이 쌓이며 스크린이 전환되는 것이고, 헤더와 더불에 헤더 타이틀 옆에 `<` 의 뒤로가기 버튼이 기본적으로 생긴다.

그리고 그 모든 UI 가 다 필요할 때는 Nested Navigation 을 써서 네비게이션을 한꺼번에 사용한다.



여기서는 Tab Navigation, Stack Navigation 을 함께 쓰는 법을 알아보고 예시 코드를 작성해볼 것이다.



#### 1. Tab Navigation

- https://reactnavigation.org/docs/tab-based-navigation

- 설치: `@react-navigation/native`,  `yarn add @react-navigation/bottom-tabs`

- Tab 객체 선언: `const Tab = createBottomTabNavigator();`

- 사용법:

  필수 속성은 아래 예시 코드와 같다.

  `<Tab.Screen>` 은 `name`, `component` 의 props 을 필수로 가진다.

  name 에는 탭의 이름, component 에는 해당 탭이 보여줄 컴포넌트를 할당해준다.

- 예시 코드

```javascript
export default function App() {
  return (
    <NavigationContainer>
      <Tab.Navigator>
        <Tab.Screen name="Home" component={HomeScreen} />
        <Tab.Screen name="Settings" component={SettingsScreen} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}
```



이렇게만 하면 아래 탭메뉴는 생기지만 스크린마다 공통 헤더를 넣을 수 없다.

각 스크린에서 헤더를 일일이 임포트해오는 것은 좀 비효율적이고, 제대로된 방법도 아닌 것 같다.

이때 사용하는 방법이 네비게이션을 연결하는 것인데 'Nesting Navigator' 라고 한다.



아주 흔한 예로 스크린 하단에는 탭메뉴가 있고 상단에는 Header 가 고정되어있는 구조가 있는데, Stack Navigation 안에 Tab Navigation 을 넣어 만든 것이다. 리액트에서 화면구조를 설계할 때 일반적으로 쓰이는 방법이므로 꼭 알아둬야 한다.



#### 2. Stack Navigation

- https://reactnavigation.org/docs/stack-navigator/
- 설치: `yarn add @react-navigation/stack`
- 사용법:

```jsx
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

function MyStack() {
  return (
    <Stack.Navigator>
      <Stack.Screen name="Home" component={Home} />
      <Stack.Screen name="Notifications" component={Notifications} />
      <Stack.Screen name="Profile" component={Profile} />
      <Stack.Screen name="Settings" component={Settings} />
    </Stack.Navigator>
  );
}
```



#### 3. Nested Navigation

실제 앱에서는 스택네비게이션과 탭네비게이션, 혹은 카테고리나 햄버거 메뉴로 많이 사용되는 드로워네비게이션 등 하나의 앱에 하나의 네비게이터만 사용하지 않고 여러 네비게이터를 사용한다.

이때 스택네비게이션 안에 탭네비게이션을 넣거나 반대로 탭네비게이션에 스택네비게이션을 넣음으로서 구현할 수 있다.

예시 코드를 보자.

- **TabNavigators in StackNavigator**

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import {createStackNavigator} from '@react-navigation/stack';
import {createBottomTabNavigator} from '@react-navigation/bottom-tabs';
import MaterialCommunityIcons from 'react-native-vector-icons/MaterialCommunityIcons';

import FavoritesScreen from './FavoritesScreen';
import SearchScreen from './SearchScreen';
import CommunityScreen from './CommunityScreen';
import NewsScreen from './NewsScreen';
import ProfileScreen from './ProfileScreen';

const Tab = createBottomTabNavigator();

const TabNavigator = () => (
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
          <MaterialCommunityIcons size={size} name={iconName} color={color} />
        );
      },
    })}
    tabBarOptions={{
      activeTintColor: '#1B2228', // 활성화 되었을 때 색
      inactiveTintColor: '#C7CDD3', // 비활성화 색
      showLabel: false, // 텍스트 숨기기
    }}>
    <Tab.Screen name="Favorites" component={FavoritesScreen} />
    <Tab.Screen name="Search" component={SearchScreen} />
    <Tab.Screen name="Community" component={CommunityScreen} />
    <Tab.Screen name="News" component={NewsScreen} />
    <Tab.Screen name="Profile" component={ProfileScreen} />
  </Tab.Navigator>
);

const Stack = createStackNavigator();

function AppStack() {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="Stack" component={TabNavigator} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default AppStack;
```

먼저 StackNavigator 를 return 하고 그 안에 `<Stack.Screen>` 의 `component` props 값으로 `TabNavigator` 를 주면 된다.

당연히 탭네비게이션을 위한 TabNavigator 객체를 만들어놔야 한다.



<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-06%2020.10.57_ZNo1pI.png?raw=true" alt="Screenshot_2021-01-06 20.10.57_ZNo1pI" style="zoom:33%;" />

위의 코드대로 하면 이런 화면이 나온다. 상당히 친근한 UI이다.

그러나 이렇게 하면 상단 "Stack" 글자 부분, 헤더의 타이틀을 스크린에 따라 변경하지 못한다.

예를 들어 내가 Home 에 있을 때는 Home 이라고 표시되고, Profile 에 있을 때는 Profile 이라고 표시되어야 한다.

그렇게 하려면 스택네비게이터 안에 탭네비게이터를 넣어야 한다.



- **StackNavigators in TabNavigator**

이번엔 반대로 TabNavigator 을 return 하면서 컴포넌트 값으로 StackNavigator 로 만들어준 객체를 넘긴다.

하지만 주의할 점이 탭네비게이터 안에서 스택네비게이터를 넘길 때는 각 탭이 리턴하는 컴포넌트를 따로따로 StackNavigator 로 만들어줘야 한다.

예를 들면 위 코드의 `TabNavigator` 함수에서는 필요한 스택 컴포넌트를 한꺼번에 리턴했지만, 여기서는 적용이 안될 것이다.

각 탭마다 필요한 컴포넌트가 다 다르기 때문.

따라서 3개의 메뉴(탭)이 있다면 3개의 StackNavigator 객체가 있어야 하고, 5개가 있다면 5개의 스택이 있어야 한다.

구현 코드는 다음과 같다.

```jsx
import React from 'react';
import {NavigationContainer} from '@react-navigation/native';
import {createStackNavigator} from '@react-navigation/stack';
import {createBottomTabNavigator} from '@react-navigation/bottom-tabs';
import MaterialCommunityIcons from 'react-native-vector-icons/MaterialCommunityIcons';

import FavoritesScreen from './FavoritesScreen';
import SearchScreen from './SearchScreen';
import CommunityScreen from './CommunityScreen';
import NewsScreen from './NewsScreen';
import ProfileScreen from './ProfileScreen';

const Tab = createBottomTabNavigator();
const FavoritesStack = createStackNavigator();
const SearchStack = createStackNavigator();
const CommunityStack = createStackNavigator();
const NewsStack = createStackNavigator();
const ProfileStack = createStackNavigator();

const FavoritesStackNavigator = () => (
  <FavoritesStack.Navigator>
    <FavoritesStack.Screen name="Favorites" component={FavoritesScreen} />
  </FavoritesStack.Navigator>
);
const SearchStackNavigator = () => (
  <SearchStack.Navigator>
    <SearchStack.Screen name="Search" component={SearchScreen} />
  </SearchStack.Navigator>
);
const CommunityStackNavigator = () => (
  <CommunityStack.Navigator>
    <CommunityStack.Screen name="Community" component={CommunityScreen} />
  </CommunityStack.Navigator>
);
const NewsStackNavigator = () => (
  <NewsStack.Navigator>
    <NewsStack.Screen name="News" component={NewsScreen} />
  </NewsStack.Navigator>
);
const ProfileStackNavigator = () => (
  <ProfileStack.Navigator>
    <ProfileStack.Screen name="Profile" component={ProfileScreen} />
  </ProfileStack.Navigator>
);

function AppStack() {
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
              case 'Profile':
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
        <Tab.Screen name="Favorites" component={FavoritesStackNavigator} />
        <Tab.Screen name="Search" component={SearchStackNavigator} />
        <Tab.Screen name="Community" component={CommunityStackNavigator} />
        <Tab.Screen name="News" component={NewsStackNavigator} />
        <Tab.Screen name="Profile" component={ProfileStackNavigator} />
      </Tab.Navigator>
    </NavigationContainer>
  );
}

export default AppStack;
```

아까와는 다르게 `FavoritesStackNavigator = ()=>()` 식으로 스택을 리턴해준 객체를 각 탭의 컴포넌트로 할당하고 있다.

이렇게 하면 의도한대로 화면이 잘 나온다. 상단 헤더부분은 스택네비게이터에서 온 것이고, 하단 탭메뉴는 탭네비게이터에서 온 것이다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-06%2020.30.45_t8TKBK.png?raw=true" alt="Screenshot_2021-01-06 20.30.45_t8TKBK" style="zoom:33%;" />



- 헤더 스타일링

헤더 타이틀 폰트 크기나 색상을 변경하고 싶다면 `<Stack.Screen>` 의 options props 에 headerTitleStyle 을 지정하면 된다.

```jsx
<FavoritesStack.Screen
  name="Favorites"
  component={FavoritesScreen}
  options={{
    headerTitleStyle: {
      fontSize: 18,
      textAlign: 'center', // 안드로이드 디폴트는 좌측정렬이기 때문에
    },
  }}
/>
```

이런식으로 해주면 되는데, 지금 컴포넌트가 5개가 있으므로 똑같은 코드를 5번씩 써줘야 한다. 

이럴 때 `headerTitleStyle` 객체를 선언해주어 모든 스택스크린에 똑같은 객체를 할당해주면 된다.

만들어진 ios, 안드로이드 화면은 각각 이렇다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-06%2020.48.24_k5RDXj.png?raw=true" alt="Screenshot_2021-01-06 20.48.24_k5RDXj" style="zoom: 33%;" />



이렇게 하면 리액트네이티브앱 기본 레이아웃은 완성된 셈이다!