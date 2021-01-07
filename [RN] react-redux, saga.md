# [RN] react-redux, saga

리액트네이티브 스토어 관리용으로 익숙한 리덕스와 사가 미들웨어를 쓰기로 했다.

이제까지는 Nextjs 와 함께 사용해서 쉽게 세팅할 수 있었는데 리액트네이티브는 어떨지 긴장이 된다.



#### 1. 리덕스 설치 및 세팅하기

- 설치: `yarn add react-redux`

우선 `Provider` 를 임포트해온 뒤, 저번에 만들었던 네비게이션 컴포넌트를 `<Provider>` 태그로 감싼다.

```jsx
import React from 'react';
import AppStack from './src/screens';
import {Provider} from 'react-redux';

const App = () => {
  return (
    <Provider>
      <AppStack />
    </Provider>
  );
};

export default App;
```

`<Provider>` 에서 store 를 지정해주는 것이기 때문에 props 로 `store` 를 넣어줘야 한다.

`<Provider store={store}>`

이런식으로 말이다.

그 전에 store 를 만들어보도록 하자.



#### 2. Store 만들기

**[store 만들기]**

스토어를 사용하기 위해 사가를 쓰는 것이므로 store.js 를 만들어준다.

- 의존성: `redux`, `redux-saga`, `redux-logger`

  리덕스는 `applyMiddleware`, `createStore` 를 가져오기 위함이고 `redux-logger` 를 통해 `logger` 를 가져온다.

- store.js 코드 예시

```jsx
import {applyMiddleware, createStore} from 'redux';
import createSagaMiddleware from 'redux-saga';
import logger from 'redux-logger';
import reducer from './src/reducers';
import rootSaga from './src/sagas'; // 루트로 사용할 사가를 불러온다.

const sagaMiddleware = createSagaMiddleware(); // 사가미들웨어 객체 생성
const store = createStore(reducer, applyMiddleware(logger, sagaMiddleware)); // 스토어 객체 생성

sagaMiddleware.run(rootSaga); // 사가 미들웨어로 루트사가를 실행한다.

export default store;
```

스토어가 여러개일 때 여러개의 스토어를 하나로 묶어 실행해주는 역할을 하는 곳이 루트사가인데, 사가미들웨어에서 이 루트사가(`rootSaga`)를 실행시켜주면 된다.

루트사가의 위치는 프로젝트마다 다 다르며 나는 `./src/sagas/index.js` 에 만들 예정이다.

마찬가지로 여러개의 액션을 한꺼번에 실행해주는 곳이 root reducer 고 위의 코드에서 reducers 에서 가져온 reducer 에 해당하는 곳이다.

이 역시 따로 만들어줘야 하는데 아래에서 다룰 것이다.

참고로 폴더 구조는 특별한 이유는 없고 react native best practice 포스팅을 봤을 때 추천해준 구조를 그대로 따라했다. (https://samanw.medium.com/reactnative-best-practices-with-best-folder-structure-6d2716d3d9cb)



**[rootSaga 만들기]**

여러 사가를 한 데 묶어 실행해주는 루트사가를 만든다.

경로를 지정하고 `index.js` 를 만든다. 나는 `index.js` 로 만들었지만 `saga.js` 로 만들기도 한다.

- /sagas/index.js 폼

```jsx
import {put, takeEvery, all, call} from 'redux-saga/effects'; // 사용할 사가 메소드를 불러온다.

// 사가를 만드는 곳

const rootSaga = function* rootSaga() {
  console.log('run rootSaga');
  yield all([
    // 이 안에 여러 사가를 넣어 한꺼번에 실행한다.
  ]);
};

export default rootSaga;
```

우선 `redux-saga/effects` 에서 필요한 메소드들을 불러온다. 보통 `put`, `takeEvery`, `all`, `call` 을 기본으로 사용한다.

그 아래에는 실제로 사용하게 될 사가를 만드는 곳이다. 다른 문서에서 불러올수도 있고 여기에 바로 작성할수도 있다. 나는 스토어가 없으니 일단 비워두도록 한다.

다음으로 rootSaga 객체를 만드는데, 사가의 독특한 문법인 `function*` 이 쓰인다. 이 함수 안에서 `yield all([]);` 로 실행시키면 되는데, 배열 안에 여러 사가를 한꺼번에 실행해주면 된다.

그리고 마지막으로 rootSaga 를 export 해주자.



만들어봤던 프로젝트 중에 가장 간단한 예시를 가져와보았다.

```jsx
import { all } from "redux-saga/effects";
import authSaga from "./authSaga";
import fileSage from "./fileSaga";

export default function* rootSaga() {
  yield all([authSaga(), fileSage()]);
}
```

생각보다 간단하쥬?



#### 3. Reducer 만들기

스토어에 접근할 수 있는 함수를 액션이라고 한다.

스토어에서 데이터를 가져오고 스토어를 데이터를 넣어주는 역할인데, 액션 함수만이 그 역할을 할 수 있고, 액션은 리듀서에서 만들 수 있다.

store.js 에서는 모든 액션을 reducer 로 가져와서 스토어 객체를 생성할 때 `createStore` 함수의 첫번째 인자로 넣어준다.

`./src/reducer/index.js` 를 생성하여 리듀서를 만들어주자.

reducer 역시 rootsaga 와 마찬가지로 여러 액션을 한번에 묶어 내보내준다. 폼은 생각보다 간단하다.



- reducers/index.js 폼

```jsx
import {combineReducers} from 'redux';

export default combineReducers({
  // 여기에 리듀서들을 넣어준다.
});
```

일단 이렇게 최소한으로만 작성해주고 잘 실행되는지 확인하자.



**[실행되는지 확인하기]**

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-07%2007.54.00_iMsp8l.png?raw=true" style="zoom:50%;" />

이렇게 해놓고 앱을 키면 'store does not have a valid reducer.' 라는 에러가 뜨는데 직역하면 스토어가 유효한 리듀서가 없다는 뜻이다.

즉, 스토어에 저장할 데이터가 없다는 뜻으로, 위의 코드에서 combineReducers 에 아무것도 넣지 않았기 때문이다.



**[리덕스 스토어 만들기]**

그렇다면 빠르게 임의의 리듀서와 initial state 를 만들어보자.

reducers 폴더에서 `favoritesReducer.js` 파일을 만들고 이 안에 최초 상태값(initialState)과 디폴트 액션을 지정해줄 것이다.

```jsx
const initialState = {
  test: 'hi',
};
export default function reducer(state = initialState, action) {
  switch (action.type) {
    default: {
      return state;
    }
  }
}
```

이렇게 최초 state 에 `test: hi` 라는 어이없는 객체를 지정한 뒤 다른 액션 없이 최초 state 를 그대로 리턴하는 액션을 만들어주었다.

저장 후 앱을 다시 확인해보자.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-07%2008.06.04_912vJY.png?raw=true" style="zoom:50%;" />

짠!! 드이어 앱이 정상적으로 실행되었다.

리덕스와 사가는 제대로 적용되었다는 뜻이다.



#### 4. 디버거로 확인하기

**[React Native Debugger 설치]**

https://medium.com/duckuism/react-native-%EB%94%94%EB%B2%84%EA%B9%85-%ED%99%98%EA%B2%BD-%EB%A7%8C%EB%93%A4%EA%B8%B0-7e46bfe89f6 을 참고하여 리액트네이티브 디버거를 설치하자.



- **주의: 버전 호환성 확인하기**

https://github.com/jhen0409/react-native-debugger

리액트네이티브 디버거 깃허브 페이지를 들어가보면 이렇게 호환성 정보가 나오는데 리액트네이티브 0.61 버전 이하를 쓰고 있다면 디버거는 0.10 이하 버전을 써야 한다. 안그러면 에러가 날수도 있다!

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-07%2019.44.38_XYy623.png?raw=true" style="zoom:50%;" />





앱이 설치되고 실행하면 드디어 웹개발자들에게 익숙한 구글 개발자도구와 비슷한 화면이 나온다!!

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-07%2008.17.48_axR42L.png?raw=true" style="zoom:67%;" />

그런데 보면 `Waiting for React to connect...` 라고 나오는데 리액트네이티브 앱과 연결이 안된 것이다.

ios 의 경우 `cmd + d`, android 의 경우 `cmd + m` 을 누르고 Debugger 버튼을 누르면 연결이 된다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-07%2019.46.01_Ra1kmO.png?raw=true" style="zoom:50%;" />

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-07%2019.46.52_jgfNQr.png?raw=true" style="zoom: 67%;" />

연결되면 이렇게 검은 화면에서 아까 등록한 스토어에 관한 정보가 나오는데 내가 favorites 로 등록한 `test` 라는 더미 스토어가 나온다.



<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-07%2019.49.27_UuZNZD.png?raw=true" style="zoom:80%;" />

아까 루트사가에 찍어두었던 "run rootSaga" 도 콘솔에 잘 나온다.



**[리액트네이티브앱에 디버거 익스텐션 설치]**

리액트에서도 익스텐션을 설치하듯 리액트네이티브에서도 디버깅을 위한 익스텐션을 설치해야 한다.

- 설치: `yarn add redux-devtools-extension`

- 적용:

  store 설정파일에 코드 추가

  ./store.js

  ```javascript
  ...
  import { composeWithDevTools } from 'redux-devtools-extension';
  ...
  ```

  createStore 부분에 composeWithDevTools 함수로 감싸주기

  ```javascript
  const store = createStore(
    reducer,
    composeWithDevTools(applyMiddleware(logger, sagaMiddleware)),
  );
  ```




**[기본 디버거를 크롬에서 react native debugger로 변경하기]**

- 설치: `yarn add react-native-debugger-open --save-dev`

- 적용:

  packages.json 에 스크립트 명령어 추가

  ```json
  ...
  "scripts": {
  	...
    "postinstall": "rndebugger-open"
  }
  ...
  ```

- 앱 재실행 후: `yarn postinstall`

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-07%2019.28.09_TJY9ua.png?raw=true" style="zoom:80%;" />

이렇게 뜨면 제대로 된 것이다!





이렇게 비교적 쉽게 리액트네이티브에 리덕스, 사가를 연결해보았다.

남은 부분에 리액트 작업할 때 처럼 착착 하면 될 것 같다.

개인적으로는 리액트네이티브 네비게이션이 러닝커브가 좀 높고 리덕스는 리액트와 똑같아서 리액트 리덕스를 할 줄 아는 사람이라면 쉽게 할 수 있을 것 같다.



다음에는 노드를 연결하고 api 를 만든 뒤, axios 통신을 해볼것이다.

