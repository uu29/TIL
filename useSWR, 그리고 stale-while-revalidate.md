# useSWR, 그리고 stale-while-revalidate

Status: 진행중

useSWR을 사용하는 이유: 전역 상태관리를 위해 + 캐시를 효과적으로 사용하기 위해

# 잠깐, SWR이 뭔데?

공식문서에도 나와있지만, 다들 눈에서 자동으로 필터링 했을 것이므로 한번 더 언급하자면, useSWR에서 SWR은 `stale-while-revalidate`의 약자이다.

<aside>
📌 `stale-while-revalidate`란?

[RFC 5861](https://datatracker.ietf.org/doc/html/rfc5861)에서 정의된 용어로, 캐시가 가지고 있는 stale response가 유효한 지 백그라운드에서 재검증하는 동안 해당 stale response를 즉시 리턴하여 네트워크 지연 시간을 숨기는 전략

stale response? 최신이 아닌 낡은 데이터
📖 stale(사전적 의미): 신선하지 않은, 낡은, 썩은

쉬운 예시)
    - 유통기한: max-age
    - 실제 음식이 상했는지? stale-while-revalidate
    ⇒ 유통기한 지났다고 음식을 못먹나? 그건 No No.

배민 예시)
    나에게 보이는 화면은 “접수 전"인데
    실제 사장님은 이미 접수를 했음
    response가 전달되기 전에는 “접수 전"이라는 데이터는 stale한 상태

실제 response가 와서 상태를 업데이트 시켜주기 전까지는 client에서는 이러한 stale 데이터가 존재하기 마련.

**캐시컨트롤 예시)**
    Cache-Control: max-age=600, stale-while-revalidate=30
    (캐시 유효기간은 5분, stale 기간은 30초로 설정한 상태)

    - 600초 내에서는 이 데이터는 유효하다 → 계속 캐시에서 가져온다. (새로 요청 X)
    - 600초가 지난 후 610초가 되었다면?
        → 이미 낡은 데이터. “stale”한 데이터로 취급. 일반적으로는 loader가 돌면서 API을 다시 호출해야함.
    - 만약 stale-while-revalidate가 설정되어 있다면?
        → “캐시 유효기간이 지난 후에도 설정한 시간동안은 만료된(stale한 상태의) 캐시데이터를 받아오겠다”는 뜻
        → 610초에 조회 시 로더가 아닌 낡은 데이터를 일단 보여줌. 그동안 refetch가 진행되고 response가 오면 상태가 반영 됨.

**그래서 stale-while-revalidate가 왜 좋은건데?**
    - latency가 숨겨짐: refetching 동안 loader을 보여줘야 하는 불필요한 UI 변경을 막음
    - 좀 더 유연하게 캐시데이터를 활용할 수 있음: 만료되었다고 무조건 끝이 아니다!! (유통기한 지났다고 못먹는 음식이냐!!)

</aside>

[[LIVE] React Query와 상태관리 :: 2월 우아한테크세미나](https://www.youtube.com/watch?v=MArE6Hy371c)

위 컨셉을 적용한 것이 react-query, useSWR 등의 상태관리 라이브러리.

# useSWR을 사용하는 가장 큰 이유

두 가지 이유가 있다.

하나는 “**상태관리를 전역으로 하기 위해”,**

두번째는 위에서 언급한 “**캐시를 효과적으로 사용하기 위해”**이다.

알고 보면 두 개가 연결된 것인데, 첫번째부터 설명하겠다.

### 상태관리를 전역으로 하는 것이 뭔가요?

리액트와 같은 프레임워크에서 변할 수 있는 값이라면 모두 “상태”라고 지칭한다.

이 상태는 기본적으로 컴포넌트 단위로 관리가 된다.

예를 들어 유저정보를 보여주는 <UserProfile/>이라는 컴포넌트가 있고 해당 컴포넌트가 `userInfo`의 상태를 갖는다고 하자.

<UserProfile/> 컴포넌트는 대략 이렇게 생겼을 것이다.

```jsx
const UserProfile = async() => {

	const { data: userInfo } = await axios('/user/me');

	return(
		<ul>
			<li>{userInfo.name}</li>
			<li>{userInfo.email}</li>
			<li>{userInfo.regDate}</li>
		</ul>
	)
}
```

userInfo는 비동기로 `/user/me` 라는 API를 통해 가져오는 데이터다.

그런데 개발하다보면 이 유저정보 데이터가 이 <UserProfile/> 컴포넌트에서만 쓰일리가 없다.

예를 들어 유저정보를 주문정보를 보여주는 <OrderInfo/>라는 컴포넌트에서도 보여준다고 가정해보자.

이 때, <OrderInfo/> 컴포넌트에서 `userInfo`을 얻는 방법은 두 가지가 있다.

1. ajax로 API에서 가져오기
2. UserProfile에서 사용하는 userInfo을 <UserProfile/>, <OrderInfo/> 둘 다 품고 있는 부모컴포넌트로 옮기고 부모컴포넌트에서 props로 상속받기

두 방법 다 비효율적이다. 1번은 같은 API을 중복으로 호출하는 문제가 있고, 2번은 <UserProfile/>, <OrderInfo/> 모두를 품고있는 부모를 찾아 상위 컴포넌트를 거슬러 올라가야 한다. *(만약 교집합이 없다면 <App/>까지 올라갈 것이다..)*

이런 비효율을 해결하기 위해 나온 것이 아직도 널리 쓰이고 있는 **redux** 상태관리 라이브러리다.

각 컴포넌트에서 필요한 상태를 전역으로 관리하여 컴포넌트에서 독립적으로 쓸 수 있도록.

부모자식 상관없이 아무 컴포넌트에서나 막 꺼내 쓸 수 있도록 만든 것이 redux이다.

그러나 redux는 훌륭한 취지와는 다르게 사용하다보면 마냥 아릅답지만은 않다. store을 만들고, store을 업데이트 할 수 있는 액션을 정의하고, 액션을 실행하는 reducer을 만들고.. 러닝커브도 러닝커브지만, 세팅하는데 필요한 boiler-plate가 많아진다. 한가지 상태관리를 위해 코드를 최소 30줄은 짜야 한다.

비효율을 해결하기 위해 나온 기술이 더 비효율 적인 상황이 발생한 것. *~~개발자는 언제나 비효율적인 것을 못참지.~~* 

redux의 이런 한계때문에 나온 것이 react-query, useSWR 등의 새로운 상태관리 라이브러리다.

구구절절한 화려하기만 한 boiler-plate 필요없이, 한줄이면 아주 간편하게 상태관리를 할 수 있다.

```jsx
const { data } = useSWR(key);
```

### 네? 이걸로 전역 상태관리가 가능하다구요?

넵. 왜냐면 uswSWR은 여러 컴포넌트에서 데이터를 호출해도 내부적으로 캐시하여 상태를 ‘기억'하고 있거든요.

위의 <UserProfile/>, <OrderInfo/> 컴포넌트를 예시로 들자면 이렇게 쓸 수 있다.

```jsx
const UserProfile = () => {

	const { data: userInfo } = useSWR('/user/me');

	return(
		<ul>
			<li>{userInfo.name}</li>
			<li>{userInfo.email}</li>
			<li>{userInfo.regDate}</li>
		</ul>
	)
}

// OrderList은 유저의 주문정보가 담긴 컴포넌트임.
const OrderInfo = () => {

	const { data: userInfo } = useSWR('/user/me');

	return(
		<div>
			<div>{userInfo.email}</div>
			<OrderList/>
		</div>
	)
}
```

위 코드를 처음 보면 “어? /user/me가 두번 호출되는거 아니야?”라고 생각할 수 있지만, useSWR에서 내장된 캐시로 데이터를 처리하기 때문에 실제로 요청은 1번만 가게 된다.

의심이 된다면 당장 네트워크 탭을 열어 확인해보자.

실제로 필자가 만들고 있는 앱에서는 useSWR을 사용해 유저정보를 가져오는 부분이 있는데, 총 29개의 컴포넌트에서 사용하고 있으나(헤더, 푸터, 등등) 요청은 단 한번만 간다.

이유는 useSWR의 첫번째 인자로 받는 string 값(— api_enpoint가 들어가는 자리)을 `key`라고 부르는데, 이 `key`가 같을 경우 uswSWR에서 같은 데이터라고 보고 상태를 공유하게 된다.

위 예시에서는 key가 ‘/user/me’로 같으니 컴포넌트가 달라도 상태가 전역으로 공유되어 한번만 요청하는 것이다.

그러면 앞서 언급한 두 가지 문제 — API 중복 호출, 부모 컴포넌트로부터 props을 내려받아야 하는 문제가 한번에 해결된다.

redux보다 훨씬 쉽고 간편하다. *~~(리덕스 이해하는데 2달 걸림)~~*

이러한 장점때문에 리덕스의 대안으로 급부상하여 useSWR 지분이 점점 커지고 있다.

이게 첫번째 이유고, 두번째 이유는 “캐시를 효과적으로 사용하기 위해"이다.

[공식문서](https://swr.vercel.app/ko/docs/options)를 보면 알겠지만, useSWR은 데이터 캐시에 대한 아주 다양한 옵션들이 있다.

이 옵션을 통해 처음에 언급한 stale 시간, cache 시간을 설정할 수 있으며 `dedupingInterval`, `revalidateIfStale` 등을 지정하여 유연하게 사용할 수 있다.

또한 `mutate`를 사용해 데이터를 즉각적으로 갱신 또는 API Response가 오기 전에 미리 갱신할 수 있다.

두번째 이유는 useSWR 사용법과 함께 자세히 설명하겠다.

*useSWR 사용법은 다음 포스팅으로..*

— Reference

[전역 상태 관리에 대한 단상 (stale-while-revalidate)](https://jbee.io/react/thinking-about-global-state/)

[비동기 전역관리 redux 말고 편하게 SWR 로!](https://velog.io/@familyman80/%EB%B9%84%EB%8F%99%EA%B8%B0-%EC%A0%84%EC%97%AD%EA%B4%80%EB%A6%AC-redux-%EB%A7%90%EA%B3%A0-%ED%8E%B8%ED%95%98%EA%B2%8C-SWR-%EB%A1%9C)