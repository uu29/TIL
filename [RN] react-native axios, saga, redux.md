## 리액트 네이티브 사가, 리덕스, axios 세팅, 스토어 저장하기

지난 시간에 사가, 리덕스를 세팅했다. 이번에는 사가에서 axios 로 api 를 호출한 뒤 각각 성공, 실패 처리를 할 것이다.

전체적인 로직은 이렇다.

(1) 컴포넌트에서 api 를 호출하는 액션을 디스패치한다.

(2) 사가에서 액션을 받아 axios 를 호출한다.

(3) axios 에서 api 를 호출하여 다시 사가로 리턴한다.

(4) 사가에서 성공의 경우 ACTION_TYPE_SUCCESS, 실패의 경우 ACTION_TYPE_FAILURE 액션을 실행한다.

(5) 리듀서에서 각각에 해당하는 액션을 받아 스토어로 저장해준다.



사가와 리덕스를 사용하게 되면 왔다갔다해서 조금만 정신을 놓아도 헷갈린다. 작업을 하다가도 내가 지금 어디있지? 뭐하고 있지? 하며 길을 잃는 경우가 많은데, 순서만 잘 알면 바로 다시 제대로 찾아올 수 있다.



일단, axios 호출 후 스토어 저장 최종 성공한 모습은 이렇다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2011.58.02_OZeyt2.png?raw=true" style="zoom:50%;" />

야후 주식 API 를 이용하여 삼성전자 시계열 데이터를 받아온 모습.



---------

### 1. custorm axios instance 생성

**[axios 세팅]**

우선 axios 설치부터 시작해보자.

- 설치: `yarn add axios`



**[instance 생성]**

이 axios 를 그냥 쓰지 않고 인터셉터(interceptor) 를 만들어 관리할 것인데, 인터셉터는 axios 를 요청, 응답 전에 가로채서 어떤 행동을 할 수 있게 하는 것인데 주로 커스텀 헤더나 공통 url 를 설정할 때 사용한다. 예를 들면 baseUrl 주소는 같고 endpoint 만 달라지는데 매번 url 을 풀로 입력하기 번거로울 때 말이다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2012.43.24_53Ec0X.png?raw=true"  />

우선 지금의 폴더 구조는 이렇다. `src/utils` 안에 `client.js` 라는 파일명으로 axios 인스턴스(axios 객체라고 생각하면 된다)를 만들 것이고 그 인스턴스에 interceptor 를 적용하여 기본 axio 를 세팅할 것이다.

```javascript
import axios from 'axios';

// axios 인스턴스 생성
const YahooClient = axios.create({
  baseURL: 'https://apidojo-yahoo-finance-v1.p.rapidapi.com',
  // timeout: 1000,
  headers: {
    // 커스텀헤더를 넣으면 오류남. 커스텀헤더에는 ' 가 생김.
    Accept: 'application/json',
    useQueryString: 'true',
  },
});

// 요청 인티셉터 (요청 전에 가로채서 axios 설정을 적용함)
YahooClient.interceptors.request.use(
  function (config) {
    console.log(config);
    // 요청 성공 직전 호출
    // axios 설정값을 넣음 (사용자 정의 설정도 추가 가능)
    return config;
  },
  function (error) {
    // 요청 에러 직전 호출
    return Promise.reject(error);
  },
);

// 응답 인티셉터 (응답 직전에 호출)
YahooClient.interceptors.response.use(
  function (response) {
    // https stauts === 200 일 때 - axios 함수에서 .then()으로 연결됨
    return response;
  },

  function (error) {
    // https stauts !== 200 일 때 - axios 함수에서 .catch()으로 연결됨
    return Promise.reject(error);
  },
);

export default YahooClient;
```

`yahooClient` 라는 이름의 axios 인스턴스를 만들 것인데, 우선 baseUrl 로 불러올 야후 finance api 주소인 https://apidojo-yahoo-finance-v1.p.rapidapi.com 를 설정하였다.

[https://github.com/uu29/TIL/blob/main/%5B%EC%A3%BC%EC%8B%9D%20API%5DYahoo%20Finance%20API.md](https://github.com/uu29/TIL/blob/main/[주식 API]Yahoo Finance API.md) 에서 이미 작성하였었는데 rapidapi 는 호출하려면 커스텀헤더가 필요하다.

** 각각 `x-rapidapi-key`, `x-rapidapi-host` 인데 axios 인스턴스를 생성할 때 커스텀헤더를 작성하면 오류가 난다. (참고로 prittier 같은 auto-formatter 를 쓰게될 경우 커스텀 헤더 값을 지정하면 아래 사진처럼 값에 따옴표('')가 생긴다.) 고로 여기에서는 기본 헤더값만 작성해주자.

![instance-with-custom-headers](https://github.com/uu29/TIL/blob/main/images/instance-with-custom-headers.png?raw=true)



**[custom header 설정]**

그렇다면 커스텀헤더는 어디에 작성할까? 요청 인티셉터에 작성하면 된다.

```javascript
YahooClient.interceptors.request.use(
  function (config) {
    const headers = {
      x_rapidapi_key: '{{부여받은 API 키}}',
      x_rapidapi_host: 'apidojo-yahoo-finance-v1.p.rapidapi.com',
    };

    const {x_rapidapi_key, x_rapidapi_host} = headers;
    config.headers['x-rapidapi-key'] = x_rapidapi_key;
    config.headers['x-rapidapi-host'] = x_rapidapi_host;
    config.headers['custom-test'] = 'test!';
    console.log(config);
    return config;
  },
  function (error) {
    // 요청 에러 직전 호출
    return Promise.reject(error);
  },
);
```

이렇게 config 부분에 커스텀헤더값을 변수로 넣고 혹시 몰라 `'custom-test': 'test!'` 라는 임의의 테스트 헤더값을 넣었다.

콘솔로 찍어보면 아래와 같이 헤더가 찍히는데, 정상적으로 커스텀헤더가 설정된 것을 알 수 있다.

![custom-header-test-in-interceptor](https://github.com/uu29/TIL/blob/main/images/custom-header-test-in-interceptor.png?raw=true)



이렇게 커스텀 인스턴스를 생성하면 앞으로 axios 를 부를 때 매번 헤더, url 을 설정할 필요 없이 이 인스턴스만 불러오면 된다.



### 2. 페이지별 axios 함수 만들기

이번에는 utils 폴더에서 페이지(스크린)별 폴더를 만들어서 거기에 각각 필요한 axios 호출 함수를 만들겠다.

![Screenshot_2021-01-09 12.57.37_zjZ8f1](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2012.57.37_zjZ8f1.png?raw=true)

그리고 각 폴더 안에 `index.js` 라는 이름의 파일을 만든 뒤 함수를 써주면 된다.

`favorites/index.js` 라고 하면 favorites 스크린에서 필요한 axios 모음이라는 뜻이다. 여기서 아까 만든 yahooClient 인스턴스를 사용할 것이다.

```javascript
import yahooClient from '../client';

const _fetchHistoricalData = (params) => {
  return yahooClient
    .get(`/stock/v3/get-historical-data`, {params: params})
    .then((res) => {
      return res;
    })
    .catch((err) => {
      if (err.response) return err.response;
    });
};

export {_fetchHistoricalData};
```

나는 사가에서 실행할 사가함수와의 구분을 위해 함수명 앞에 `_` 를 붙여줬다.

이 스크린에서 부를 함수는 `_fetchHistoricalData` 로, 야후 api 의 `/stock/v3/get-historical-data` 를 호출하는 함수다.

필요한 params 를 인자로 받아서 야후 인스턴스를 리턴해준다.

이 함수는 나중에 사가에서 쓰이게 될 것이다. 그럼 바로 사가를 만들어보자!



### 3. 사가파일 설정하기

favorites 스크린에서 필요한 사가들은 `sagas/favoritesSaga.js` 라는 파일을 만든 뒤 그 안에 다 넣을것이다. 기본 코드는 이렇다.

```javascript
import {put, call, takeLatest} from 'redux-saga/effects';
import {
  FETCH_HISTORICAL_DATA,
  FETCH_HISTORICAL_DATA_SUCCESS,
  FETCH_HISTORICAL_DATA_FAILURE,
} from '../reducers/favoritesReducer';
import * as api from '../utils/favorites';

// 사가 함수 실행
export default function* authSaga() {
  yield takeLatest(FETCH_HISTORICAL_DATA, fetchHistoricalData$);
}

// 사가 함수 만들기
function* fetchHistoricalData$(action) {
  const {payload} = action;
  if (!payload) return;
  try {
    const res = yield call(api._fetchHistoricalData, payload);
    const {status} = res;
    if (status !== 200) {
      return;
    }
    const {data} = res;
    yield put({type: FETCH_HISTORICAL_DATA_SUCCESS, payload: data});
  } catch (error) {
    yield put({type: FETCH_HISTORICAL_DATA_FAILURE, error: error});
    return;
  } finally {
    // 맨 마지막에 실행되는 함수
  }
}
```

먼저 필요한 액션들을 임포트해온다. 이 액션들은 리듀서에서 만들었는데, 곧 만들게 될 것이다.

`fetchHistoricalData$` 라는 이름의 사가함수를 만든다. 사가에서는 `function*` 라는 독특한 형식으로 함수를 만들어야한다. 그래서 함수네이밍을 할 때도 필수는 아니지만 보통 마지막에 `$` 을 붙인다.

이 함수에서는 action 을 인자로 받고 그 안에 payload 라는 형태로 넘어오는 파라미터를 받는다.

그리고 `yield`의 `call` 메소드를 이용하여 아까 만들어준 api 를 호출한다.

여기에서 분기처리가 되는데, status 가 200 으로 성공적으로 받아오면 성공 액션을, 실패하면 실패 액션을 `put` 메소드로 실행하게 된다. 성공 액션과 실패 액션은 첫 부분 임포트문을 통해 리듀서에서 받아왔다.

작성하고 나면 이 함수를 takeLatest 를 통해 export 하는 것을 잊지말자. 생각보다 간단하다.

그 다음에는 리듀서에서 액션을 만들어보자!



### 4. Reducer 만들기

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2013.20.43_vM7GFr.png?raw=true" alt="Screenshot_2021-01-09 13.20.43_vM7GFr" style="zoom:80%;" />

리듀서 역시 페이지별로 네이밍을 하여 `reducers/` 폴더 안에 한꺼번에 넣을 것이다. 우선 favorites 스크린에서 쓰일 리듀서만 만들어보았다.

리듀서에서 해야할 일은 3가지다.

(1) 최초상태값(initialState) 만들기

액션 실행 전의 최초 상태값으로, 기본적으로 리덕스 스토어에 저장될 값이다. 어떤 데이터가 어떤 형태로 들어올지 예측할 수 있어야 한다.

(2) 타입 만들기

보통 요청, 요청성공, 요청실패 3가지로 만들고 로딩 상태 구현을 위해 요청중, 요청완료의 타입이 추가되기도 한다.

(3) 리듀서 만들기

아까 만든 액션타입을 swtich 문으로 받아 스토어 상태를 업데이트한다.



**[예제 코드]**

```javascript
// 최초 상태값 지정해주기
export const initialState = {
  my_favorites: [
    {symbol: '005930.KS'},
    {symbol: '005380.KS'},
    {symbol: '051910.KS'},
    {symbol: '006400.KS'},
    {symbol: 'TSLA', region: 'US'},
    {symbol: 'AAPL', region: 'US'},
    {symbol: 'NVDA', region: 'US'},
    {symbol: 'ASML', region: 'US'},
    {symbol: 'AMZN', region: 'US'},
    {symbol: 'WMT', region: 'US'},
    {symbol: 'NKE', region: 'US'},
  ],
  my_favorites_error: null,
  historical_data: [],
  historical_data_error: null,
};

// 타입 만들기
export const FETCH_HISTORICAL_DATA = 'FETCH_HISTORICAL_DATA';
export const FETCH_HISTORICAL_DATA_SUCCESS = 'FETCH_HISTORICAL_DATA_SUCCESS';
export const FETCH_HISTORICAL_DATA_FAILURE = 'FETCH_HISTORICAL_DATA_FAILURE';

// 리듀서 만들기
const reducer = (state = initialState, action) => {
  switch (action.type) {
    case FETCH_HISTORICAL_DATA_SUCCESS:
      return {
        ...state,
        historical_data: action.payload,
        historical_data_error: null,
      };
    case FETCH_HISTORICAL_DATA_FAILURE:
      return {
        ...state,
        historical_data: null,
        historical_data_error: action.error,
      };
    default:
      return state;
  }
};

export default reducer;
```

나는 이렇게 구현해보았다. 최초 스토어에는 `my_favorites` 라는 이름의 객체가 있는데, 내가 만들게 될 앱에서 사용자가 즐겨찾기로 지정한 주식 종목들의 정보다. 이 데이터는 나중에 검색을 하고 즐겨찾기에 추가하고, 그 정보를 데이터에 저장하는 로직이 만들어진 뒤에야 불러올 수 있을 것 같아서 우선 내가 임의로 지정해주었다. (나중에 기능이 구현되면 처음에 내 즐겨찾기 종목 리스트를 불러오는 리듀서도 추가되어야 한다.)

관심종목에 추가한 리스트들이 `my_favorites` 에 있고, 처음에는 이 리스트들의 주가 정보를 불러와야 한다. 이 주가정보를 불러오는 액션이 `FETCH_HISTORICAL_DATA` 다. 우선 야후 api 의 시계열 데이터를 불러오는 것으로 하겠다.

성공적으로 불러왔을 때 데이터는 `historical_data` 에 객체들의 배열로 담긴다. 실패의 경우 `historical_data_error` 에 담겨 나중에 화면에서 표시할 것이다.

관심종목이 여러개라면 관심종목에 등록된 갯수만큼 api 를 호출해야된다. 그래서 배열 안에 객체로 담기는 것이다. 그렇게 하면 사가에서 api 를 동시에 호출해서 불러와야 하는데, 그 작업은 나중에 하겠다.

우선 관심종목 리스트 중 첫번째 값만 파라미터로 넘겨주어 `historicla_data` 에 담아볼 것이다.



### 5. 컴포넌트에서 데이터 불러오기

다시 처음으로 돌아가서 `screens/FavoriteScreen/index.js` 로 가면 여기가 favorites 스크린의 루트 컴포넌트이다. 여기서 이제까지 만든 사가를 호출할 것인데, 컴포넌트가 렌더링되기 전인 `useEffect` 에서 호출할 것이다.

```jsx
import React, {useEffect} from 'react';
import {View, Text, StyleSheet} from 'react-native';
import {useDispatch, useSelector} from 'react-redux';
import ItemList from '../../components/ItemList';
import {FETCH_HISTORICAL_DATA} from '../../reducers/favoritesReducer';

function FavoritesScreen() {
  const dispatch = useDispatch();
  const {
    favorites: {my_favorites},
  } = useSelector(({favorites}) => ({
    favorites: favorites,
  }));

  useEffect(() => {
    dispatch({type: FETCH_HISTORICAL_DATA, payload: my_favorites[0]});
  }, []);

  return (
    <View style={styles.container}>
      <ItemList />
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
  },
});

export default FavoritesScreen;
```



먼저 `useSelector` 를 통해 스토어의 `my_favorites` 종목의 첫번째 값을 받아온다. 그리고 `useEffect` 에서 `FETCH_HISTORICAL_DATA` 액션을 디스패치해준다. `payload` 값으로는 파라미터를 넣어주면 되는데 `my_favorites[0]` 가 그것이다.

이렇게 불러오면 RN개발자도구를 열어보자! 드디어 상태값이 변하면서 데이터가 제대로 들어간 것을 볼 수 있다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-09%2013.36.15_xxAnwB.png?raw=true" style="zoom:67%;" />

검은 화면의 왼쪽 상단 탭 중 State > Tree 에서 변한 데이터를 확인할 수 있다.

너무 아름답지 않은가? 이렇게 고생해서 얻은 소중한 데이터를 내 두 눈으로 보는 것이. 매번 성공할 때 마다 매번 짜릿하다. ㅠㅠ

또 화면의 왼쪽을 보면 FETCH_HISTORICAL_DATA, FETCH_HISTORICAL_DATA_SUCCESS 가 뜨는데 순서대로 액션이 실행되면 저렇게 찍힌다. 나중에 디버깅할 때 도움이 되니 항상 봐줘야 한다.



이렇게 RN 앱에 axios 를 사용하여 API 를 호출하였고 리덕스 스토어에도 저장해봤다.



다음시간에는 드디어 가라데이터에서 벗어나서 이 저장한 데이터들을 컴포넌트에 전달하여 실제 UI 를 렌더링 해보자!