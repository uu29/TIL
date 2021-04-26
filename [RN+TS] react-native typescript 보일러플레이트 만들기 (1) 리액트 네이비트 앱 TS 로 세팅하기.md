이 글은 https://medium.com/@vdelacou/set-up-react-native-app-with-typescript-6c775e512336 을 보고 작성되었습니다.



이번 주 목표는 기존에 JS 로 작성하고 있던 주식앱을 TS 로 마이그레이션 하는 것이다.

원래 기존 프로젝트 코드를 TS 로 탈바꿈 하려고 했으나... 온갖 빨간줄과 버그와 혈투끝에 앱도 정상적으로 작동되게 하지 못하고 저버렸다.

몇시간의 삽질 끝에 깃을 다시 원복하고 이럴거면 아예 새로 보일러플레이트를 만들어보는게 낫겠다 싶었다.

적당한 블로그를 찾아 그대로 따라해보았다.



## 1단계. 리액트 네이티브 앱 Typescript 로 세팅하기 (React Native set JS to TS)

(1) 타입스크립트로 세팅된 RN 앱 설치

` react-native init myapp --template typescript`

(2) ts lint 설치

`npm install tslib tslint tslint-react tslint-config-standard tslint-config-prettier tslint-react-hooks tsutils tslint-etc --save-dev`

(3) 루트 경로에 `tslint.json` 생성하여 린트 파일 설정하기 (본인의 스타일에 맞게 커스텀하면 된다)

```json

{
  "extends": [
    "tslint:recommended",
    "tslint-config-standard",
    "tslint-etc",
    "tslint-react",
    "tslint-react-hooks",
    "tslint-config-prettier"
  ],
  "rules": {
    // types related rules
    "no-inferrable-types": true,
    "no-namespace": {
      "options": ["allow-declarations"]
    },
    "interface-name": {
      "options": "never-prefix"
    },
    "no-unsafe-any": true,
    // core lint rules
    "ban": {
      "options": [
        {
          "name": "parseInt",
          "message": "use #type-coercion -> Number(val)"
        },
        {
          "name": "parseFloat",
          "message": "use #type-coercion -> Number(val)"
        },
        {
          "name": "Array",
          "message": "use #array-constructor"
        },
        {
          "name": ["describe", "only"],
          "message": "don't focus spec blocks"
        },
        {
          "name": ["it", "only"],
          "message": "don't focus tests"
        }
      ]
    },
    "no-require-imports": true,
    "no-boolean-literal-compare": true,
    "no-invalid-this": {
      "options": "check-function-in-method"
    },
    "no-invalid-template-strings": true,
    "ordered-imports": true,
    "prefer-template": true,
    "newline-before-return": true,
    "match-default-export-name": true,
    "no-parameter-reassignment": true,
    "file-name-casing": [true, "kebab-case"],
    "switch-default": true,
    // tslint:recommended overrides
    "no-any": true,
    "member-access": {
      "options": ["no-public"]
    },
    "object-literal-sort-keys": false,
    "interface-over-type-literal": false,
    // tslint:tslint-config-standard overrides
    //
    // tslint-etc rules
    "no-unused-declaration": true,
    "no-missing-dollar-expect": true,
    "no-assign-mutated-array": true,
    // tslint-react rules
    "jsx-boolean-value": {
      "options": "never"
    }
  },
  // apply the same rules for any JS if allowJS is gonna be used
  "jsRules": true
}
```

(4)  `./src` 폴더 만들어서 `App.tsx`  파일 이동 후 클래스형에서 함수형 컴포넌트로 바꿔주기

```tsx
import React, { FC } from "react";
import { Platform, StyleSheet, Text, View } from "react-native";
const instructions = Platform.select({
  ios: `Press Cmd+R to reload,\n Cmd+D or shake for dev menu`,
  android: `Double tap R on your keyboard to reload,\n Shake or press menu button for dev menu`
});
const App: FC = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.welcome}>Welcome to React Native!</Text>
      <Text style={styles.instructions}>To get started, edit App.tsx</Text>
      <Text style={styles.instructions}>{instructions}</Text>
    </View>
  );
};
export default App;
const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: "center",
    alignItems: "center",
    backgroundColor: "#F5FCFF"
  },
  welcome: {
    fontSize: 20,
    textAlign: "center",
    margin: 10
  },
  instructions: {
    textAlign: "center",
    color: "#333333",
    marginBottom: 5
  }
});
```

(5) 루트 경로의 `index.js` 내용 바꿔주기

- {} 컬리브레이스가 있다면 빼주고 App 임포트 경로 바꿔주기

`import App from './src/App';`

(6) `__test__` 폴더 내 `App-test.js` 파일명 `app-test.tsx` 로 바꿔주기, 안에 있는 App 임포트 경로도 맞게 수정

it 에 빨간줄과 함께 모듈이 없다는 경고가 나오면 ` npm i --save-dev @types/jest`

<img src="https://i.imgur.com/GKXgTXV.png" alt="it 에 경고" style="zoom:50%;" />

(7) `package.json` 스크립트에 다음 명령어 추가

​	`"lint": "tslint src/**/*.ts* --project tsconfig.json"`

(8) 허스키 설치: `npm install husky --save-dev`

​	`package.json` 에 추가

```json
"husky": {
    "hooks": {
      "pre-commit": "npm run lint",
      "pre-push": "npm run lint"
    }
  }
```



위의 과정을 거치면 이제 내 앱이 타입스크립트로 성공적으로 실행이 된다.

아래는 완성된 앱의 기본 UI다.

<img src="https://i.imgur.com/mmx1DJq.png" alt="완성된 UI" style="zoom: 33%;" />
