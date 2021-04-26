## 3단계. 리액트 네이티브 백터아이콘 설치하기(Add React Native Vector Icons)

모든 세팅은 초창기에 진행한다. RN 에서 아이콘으로 가장 많이 사용하게 될 vector icons 모듈을 적용해보자.

(1) 벡터아이콘 설치

​	`yarn add react-native-vector-icons`

​	써드파티 라이브러리를 설치할 때 이렇게 설치만 하면 안되는데, 조심스레 마우스를 위로 올려대보면 이런 경고가 나온다.

![써트파티 모듈 임포트 시 타입 지정 안해주면 생기는 경고](https://i.imgur.com/PdyAnzl.png)

​	해당 모듈에 타입이 정의되어있지 않아 `@types/` 로 시작되는모듈 정의 패키지를 설치하든가 없다면 모듈을 선언해야 한다는 뜻이다.

​	다행히 react-native-vector-icons 의 경우 타입이 있어서 설치해준다.

(2) 타입 설치하여 모듈 정의 추가해주기

​	`yarn add --dev @types/react-native-vector-icons`

(3) RN 개발 의존성과 연결해주기

​	`react-native link react-native-vector-icons`
​	이 단계는 리액트 네이티브 0.60 버전 이상이라면 필요 없다.

일단 설치만 해두고 나중에 사용하자.



## 4단계. 리액트 네이티브 앱에 리덕스 스토어 붙이기

 이번엔 리덕스 스토어를 붙여보자.

(1) 설치

`yarn add redux react-redux`

`yarn add --dev @types/redux @types/react-redux`

@types로 설치하는 라이브러리는 type definition을 추가해 주기 위해 설치하는 것이므로 dev-dependency로 설치한다. **react-redux는 React를 위한 공식 redux 바인딩이다.**