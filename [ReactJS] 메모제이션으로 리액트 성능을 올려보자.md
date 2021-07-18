## 메모제이션(Memozation)이란?

- 기존 값을 기억해놨다가 특정 액션이 실행된 뒤의 값이 **이전 값과 동일하면 이를 재활용**하는 기법.
- Memoized된 내용을 재사용하여 렌더할 시, **가상 DOM에서 바뀐 부분을 확인하지** 않아 성능이 향상됨.



## 메모제이션 전에 꼭 알아야 할 리액트 특징

- 리액트가 상태관리를 할 수 있는 이유는 가상 DOM을 만들어놓고 값이 바뀔 때 마다 **컴포넌트를 새로 그리기** 때문.

값을 비교하고 어디서 해당 값을 업데이트해줘야 하는지 찾지 않고 무조건 가상 DOM을 새로 그리기 때문에 상태관리가 쉬운 것이다. 하지만 이렇게 하면 값이 그대로인 부분도 매번 새로 그려야 한다는 성능상의 단점이 있다. 이때문에 나온 것이 리액트 메모제이션 기법. **값이 그대로인 것들은 새로 그리는 노동을 줄임으로써 성능을 아끼기 위해** 나온 것이다.



## 리액트 메모제이션 종류 및 사용법

#### 1. React.memo()

props가 같다면 컴포넌트를 새로 그리지 않는다.

```tsx
function ProductDesc({ name, desc }) {
  return (
    <div>
      <div>Product name: {name}</div>
      <div>Product description: {desc}</div>
    </div>
  );
}

export default React.memo(ProductDesc);
```

위와 같은 컴포넌트가 있다고 가정했을 때, `<ProductDesc>` 의 props인 name, desc가 같다면 이 컴포넌트가 자체가 리렌더링 되지 않는다.

그렇다면 React.memo에서는 props가 바뀐지 안바뀐지 어떻게 비교를 할까?

⇒ **얕은(shallow) 비교**를 사용한다.

즉, props로 객체 타입이 넘어왔을 때 **reference로 체크하기** 때문에 객체 안의 값들이 같더라도 항상 다른 값으로 판단하게 된다.

(자세한 문서: https://stackoverflow.com/questions/36084515/how-does-shallow-compare-work-in-react)

만약 얕은 비교방식을 사용하지 않고 직접 만들고 싶다면?

⇒ `React.memo()` 의 두번째 인자로 비교 함수를 직접 만든다.

바로 위 예시에는 scalar 값이 props로 넘어와서 상관 없지만, 만약 아래처럼 **객체 타입의 props를 넘겨준다면** shallow 비교 방식으로는 객체 안의 값이 같아도 참조 주소가 다르기 때문에 React.memo를 사용했더라도 항상 DOM을 다시 그려주게 된다.

이를 방지하려면 아래처럼 커스텀 비교 함수를 작성하여 객체의 값이 같은 경우에 true를 리턴하여 재렌더링을 하지 않게끔 할 수 있다.

```tsx
function MemoizedProduct2 ({product}){
	return (
    <div>
      <div>Product name: {product.name}</div>
      <div>Product description: {product.desc}</div>
    </div>
  );
}

function productPropsAreEqual(prevProduct, nextProduct) {
  return (
    prevProduct.name === nextProduct.name &&
    prevProduct.desc === nextProduct.desc
  );
}

const MemoizedProduct2 = React.memo(ProductEsc, productPropsAreEqual);
```

** React.memo를 쓴다고해서 성능이 무조건 나아지지 않는다는 이유가 이때문이다.

shallow level에서만 데이터를 비교하므로, 복잡한 구조의 데이터는 비교할 수 없다.



**[DO USE]**

- **컴포넌트가 리액트 트리에서 중위-상위 레벨에 해당**하는 경우 (ex: Header, Footer, Layout HOC,,,)
- props가 단순하고 자주 바뀌지 않을 경우
- props가 복잡한 Object일 때

**[DO NOT USE]**

- **props가 항상 바뀌는 경우가 대부분일 때**

⇒ 거의 모든 상황에서 props가 바뀌는데 굳이 비교함수를 먼저 실행해야 할까? [비교 함수 실행 + 컴포넌트 재렌더링]의 이중 작업을 항상 수행해야 하므로 성능이 오히려 안좋아질 수 있다.

- **custom 비교 함수를 작성하지 않은 객체 타입을 props로 넘길 때**

⇒ 위 예시처럼 props로 넘기는 object는 값이 같더라도 리액트 특성상 참조 주소가 항상 달라지므로 shallow 비교방식이 기본인 React.memo에서는 항상 다르다고 판단할 것이다. 비교함수를 일일이 작성할 것이 아니라면 굳이 사용하는 의미가 없다.



#### 2. useMemo, useCallback

컴포넌트 내부 로직에서 쓰이는 변수, 함수의 재렌더링을 막고자 할 때. (컴포넌트 자체를 새로 그리지 않게하는 React.memo와는 다르다.)

클래스형 컴포넌트에서는 쓰이지 않고, 함수형 컴포넌트에서만 쓰인다.

- useMemo: 값을 기억
- useCallback: 함수를 기억



**[How to use]**

```tsx
const memozationFunc = useCallback(({필요한 args})⇒{
	함수 내용 작성
}, [함수 내용에 영향을 미치는 변수])
```

주의할 것은 useMemo, useCallback 모두 함수 두번째 인자로 `[]` 라는 depth를 갖는데, 재렌더링 예외처리를 할 수 있는 부분이다. 이 안에 특정 변수가 채워지면 이 변수가 바뀔 때 마다 해당 함수는 재렌더링된다.



**⇒ 언제 사용하고 언제 사용하지 말아야 할까?**

**[DO USE]**

- 값을 얻어낼 때 마다 복잡한 로직을 사용해야 할 때 ex) for loop의 사용, json.parse, regex 사용 등
- 렌더링 할 때 마다 비용 소모가 클 때

**[DO NOT USE]**

- 값이 단순할 때 useMemo를 사용하면 오히려 가독성이 떨어진다.

  `const perPage = useMemo(()=> 5, []);`

  위 예시 코드는 `perPage` 라는 변수는 단순 scalar 값인 5라는 number를 리턴하는데 연산 방식이 느리지도 않고, `const perPage = 5;` 로 끝날 코드를 오히려 불필요하게 길어지게 만든다.

- 비용소모가 크지 않은 함수일 때

- 해당 함수/변수를 component scope 밖으로 옮길 수 있을 때



#### 참고한 문서들

[React - 리액트에서의 shallow compare 부터 React.PureComponent 까지](https://ideveloper2.tistory.com/159)

[[React\] Memoization, Portal](https://velog.io/@bangina/React-Memoization-Portal)

[Use React.memo() wisely](https://dmitripavlutin.com/use-react-memo-wisely/)

[memo, useMemo, useCallback으로 React 성능 최적화하기](https://im-developer.tistory.com/198)