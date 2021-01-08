## 야후 주식 API 로 주식 시계열 데이터 불러오기

<img src="https://github.com/uu29/TIL/blob/main/images/figma_screent_shot_20200108.png?raw=true" style="zoom: 33%;" />

내가 기획한 첫번째 화면은 이렇다.

사용자가 관심종목에 추가한 종목들이 리스트형태로 보여지는 것. UI 는 토스를 많이 참조했다.

데이터를 불러오기 위해 키움 Open API, 대신증권 API 등 몇개를 찾아봤는데 다 증권사 계좌로 로그인해야 하는 것이어서, 그보다 접근성이 좋은 야후 Finance API 를 택했다.

**[야후 Finance API - Rapid API]**

![sreenshot-yahoo-finance-20200108-how-to-use-01](https://github.com/uu29/TIL/blob/main/images/sreenshot-yahoo-finance-20200108-how-to-use-01.png?raw=true)

https://rapidapi.com/apidojo/api/yahoo-finance1

나는 야후 개발자 사이트 대신 Rapid API 라는 사이트에서 문서를 봤는데 처음에는 영어천지여서 내가 원하는게 어디있는지 못찾겠지만, 눈 크게 뜨고 보다 보면 잘 읽힌다.

좌측에는 api 리스트가 있고 가운데는 인증정보, 우측에는 테스트코드와 리턴값 예시가 있다.

이 많은 api 중에 내가 첫번째로 불러올 것은 `stock/v3/get-historical-data` 이다.



#### - API 사용법

**[결과값 예시]**

api 리턴값부터 보여주면 이렇게 생겼다.

"prices" 라는 값 안에 배열이 있는데, 그 배열 안에는 해당 주식의 가격 정보를 한눈에 알 수 있는 object 이 들어있다.

자세히 보면 date, open, high, low, close, volume, adjclode 의 값이 있는데 각각 날짜, 시가, 최고가, 최저가, 종가, 볼륨, 수정종가 이다.

다른건 잘 모르겠고 우리에게 필요한 값은 시가, 최고가, 종가 이다. 사실 실시간 데이터가 필요하지만 일단 처음에는 이렇게 가보겠다.

이제 api 를 호출해보자.

```
{
    "prices": [
        {
            "date": 1610053202,
            "open": 777.6300048828125,
            "high": 816.989990234375,
            "low": 775.2000122070312,
            "close": 816.0399780273438,
            "volume": 49129918,
            "adjclose": 816.0399780273438
        },
        {
            "date": 1609943400,
            "open": 758.489990234375,
            "high": 774,
            "low": 749.0999755859375,
            "close": 755.97998046875,
            "volume": 44457500,
            "adjclose": 755.97998046875
        },
        {
            "date": 1609857000,
            "open": 723.6599731445312,
            "high": 740.8400268554688,
            "low": 719.2000122070312,
            "close": 735.1099853515625,
            "volume": 32245200,
            "adjclose": 735.1099853515625
        },
       ...
   ]
}
```



**[axios 세팅]**

get 메소드로 헤더에 부여받은 `x-rapidapi-key`, `x-rapidapi-host`를 넣고 `'useQueryString': 'ture'`를 해준다.

그리고 호출하면 끝이다. 나는 리액트네이티브에 붙이기 전에 포스트맨으로 해보았다.

```javascript
var axios = require('axios');

var config = {
  method: 'get',
  url: 'https://apidojo-yahoo-finance-v1.p.rapidapi.com/stock/v3/get-historical-data?symbol=TSLA&region=US',
  headers: { 
    'x-rapidapi-key': '7cd6735678msh7d3a80d86d9f15ep1739d8jsn0b3dc896f11c', 
    'x-rapidapi-host': 'apidojo-yahoo-finance-v1.p.rapidapi.com', 
    'useQueryString': 'true'
  }
};

axios(config)
.then(function (response) {
  console.log(JSON.stringify(response.data));
})
.catch(function (error) {
  console.log(error);
});
```



<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-08%2021.58.31_emByiw.png?raw=true" style="zoom:50%;" />

포스트맨으로 호출해보니 아주 잘~ 나온다.



이런식으로 그때그때 필요한 주식데이터를 불러오면 될 것 같다.

내가 기획하는 앱은 즐겨찾기, 검색페이지에서 활용할 수 있겠다. 개발하는 이야기는 다음 포스팅에 작성하겠다.



실시간을 포함한 더 많은 사용법은 아래 문서에서 볼 수 있다.

https://rapidapi.com/blog/how-to-use-the-yahoo-finance-api/