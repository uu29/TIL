이 글은 https://joshua1988.github.io/ts/etc/convert-js-to-ts.html#%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EC%97%90-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%EC%A0%81%EC%9A%A9%ED%95%98%EB%8A%94-%EC%A0%88%EC%B0%A8 을 보고 작성되었습니다.



#### 주의할 점

- 기능적인 변경 절대 금지
- 처음부터 엄격하게 타입 정하지 않기.
- 우선 any 타입으로 선언한 뒤 점진적으로 적당한 타입으로 변경해라.



#### 작업 순서

1. 타입스크립트 환경설정
2. any 타입 설정
3. any 타입을 적당한 타입으로 변경
4. 빨간 줄 고치기



----------------

#### [JS=>TS]내 RN 앱에 적용하기

1. 설치: 차례대로

   `yarn add typescript`

   `yarn add --dev react-native-typescript-transformer`

   `yarn tsc --init --pretty --jsx react-native`

   `touch rn-cil.config.js`

   `yarn add --dev @types/react @types/react-native`

2. `rn-cli.config.js` 에 추가(react-native 0.58 이상 버전에서는 작성할 필요가 없음)

```javascript
module.exports = {
  getTransformModulePath() {
    return require.resolve('react-native-typescript-transformer');
  },
  getSourceExts() {
    return ['ts', 'tsx'];
  },
};
```

3. `ts.config.json` 생성하여 타입스크립트 설정해주기

```json
{
  "compilerOptions": {
    "allowJs": true,
    "target": "ES5",
    "outDir": "./dist",
    "moduleResolution": "Node",
    "lib": ["ES2015", "DOM", "DOM.Iterable"]
  },
  "include": ["./src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

4. `index.js` 를 제외한! 프로젝트의 `.js` 파일을 모두 `.ts` 로 변경해서 제대로 컴파일 되는지 테스트해보기, 컴포넌트인 경우 `.tsx` 로 바꾸기

5. `ts.config.js ` 에서 compilerOptions 로  `noImplicitAny: true` 설정하기

6. 가능한 모든 곳에 타입 적용하기

7. 써드파티 모듈에 모듈 정의 추가하기

   써드파티 모듈을 임포트해오는 부분에 빨간 줄이 쫙쫙 그어져 있는 경우가 있는데, 조심스레 마우스를 위로 올려대보면 이런 경고가 나온다.

   ![써트파티 모듈 임포트 시 타입 지정 안해주면 생기는 경고](https://i.imgur.com/PdyAnzl.png)

   `@types/` 로 시작되는모듈 정의 패키지를 설치하든가 없다면 모듈을 선언해야 한다는 뜻이다.

   다행히 내가 쓰는 메테리얼커뮤니티 아이콘은 ts 모듈 정의도 있었기에 `yarn add --dev @types/react-native-vector-icons` 로 설치해주니 빨간 줄이 사라졌다.



#### 에러 나던 코드

- 코드

```typescript
const headerTitleStyle = {
  fontSize: 18,
  textAlign: 'center', // 안드로이드 디폴트는 좌측정렬이기 때문에
};

const options: StackNavigationOptions = {
  headerTitleStyle: headerTitleStyle,
  cardStyle: {backgroundColor: 'white'},
};
```

- 에러 내용

<img src="/Users/yuriahn/TIL/images/Screenshot_2021-01-17 13.15.10_yMX57G.png" alt="Screenshot_2021-01-17 13.15.10_yMX57G" style="zoom:80%;" />

```
(property) headerTitleStyle?: false | RegisteredStyle<TextStyle> | Animated.Value | Animated.AnimatedInterpolation | Animated.WithAnimatedObject<TextStyle> | Animated.WithAnimatedArray<...> | null | undefined
Style object for the title component.

Type '{ fontSize: number; textAlign: string; }' is not assignable to type 'false | RegisteredStyle<TextStyle> | Value | AnimatedInterpolation | WithAnimatedObject<TextStyle> | WithAnimatedArray<...> | null | undefined'.
  Type '{ fontSize: number; textAlign: string; }' is not assignable to type 'WithAnimatedObject<TextStyle>'.
    Types of property 'textAlign' are incompatible.
      Type 'string' is not assignable to type '"center" | Value | AnimatedInterpolation | "left" | "right" | "auto" | "justify" | undefined'.ts(2322)
```

대충 파파고를 돌려보면 `'Styles' 유형은 'With Animated Object' 유형에 할당할 수 없습니다.TextStyle>'입니다.` 라고 나온다.

headerTitleStyle 이 `<TextStyle>` 타입이라는 얘기 같다. 타입을 지정해주자.

```typescript
const headerTitleStyle: TextStyle = {
  fontSize: 18,
  textAlign: 'center', // 안드로이드 디폴트는 좌측정렬이기 때문에
};
```

TextStyle 이라는 타입을 그냥 지정해주면 빨간줄이 나온다. 마우스를 대보면 `Can't not find name 'TextStyle'` 이라고 나오는데, 리액트 네이티브 내장 컴포넌트인 TextStyle 을 임포트해오면 된다.

- 해결 코드

````typescript
import {TextStyle} from 'react-native';

const headerTitleStyle: TextStyle = {
  fontSize: 18,
  textAlign: 'center', // 안드로이드 디폴트는 좌측정렬이기 때문에
};

const options: StackNavigationOptions = {
  headerTitleStyle: headerTitleStyle,
  cardStyle: {backgroundColor: 'white'},
};
````



