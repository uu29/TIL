# Typescript

## 1. 타입스크립트란?

- 자바스크립트의 대안
- 엄격한 타입의 사용 - 한번 지정한 타입을 마음대로 바꿀 수 없다.

## 2. 설치

설치 | `npm install -g typescript`

CRA 로 만들기 | `npx create-react-app ts-react-tutorial --typescript`

## 3. 컴포넌트 만들기

- 요즘 추세는 tsx 컴포넌트를 선언할 때 화살표 함수보다 function 함수를 많이 쓴다.

- 타입을 선언할 때는 `type` 을 써도 되고, `interfate` 를 써도 되지만 프로젝트의 일관성은 유지해야 한다.

- React.FC 는 다음과 같은 이유로 추천하지 않는다.

  > 1.  props 에 기본적으로 `children` 이 들어가있다.
  >
  > => 컴포넌트의 props 타입이 명백하지 않다. (장점의 1번이 단점이 됨)
  >
  > 2.  컴포넌트의 `defaultProps`, `propTypes`, `contextTypes` 를 설정할 때 자동완성이 될 수 있다.
  >
  > => (아직까지는) React.FC 를 사용하는 경우 `defaultProps` 가 제대로 작동하지 않는다.

- 컴포넌트 샘플 코드

```javascript
import React from "react";

type GreetingsProps = {
  name: string,
  mark: string,
  optional?: string,
  onClick: (name: string) => void,
};

function Greetings({ name, mark, optional, onClick }: GreetingsProps) {
  const handleClick = () => onClick(name);
  return (
    <div>
      Hello, {name} {mark}
      {optional && <p>{optional}</p>}
      <div>
        <button onClick={handleClick}>Click Me</button>
      </div>
    </div>
  );
}

Greetings.defaultProps = {
  mark: "!",
};

export default Greetings;
```

```javascript
import React from "react";
import Greetings from "./Greetings";

const App: React.FC = () => {
  const onClick = (name: string) => {
    console.log(`${name} says hello`);
  };
  return <Greetings name="Hello" onClick={onClick} />;
};

export default App;
```

## 4. 리액트 Hooks 관리

#### (1) useState 및 이벤트 관리

```javascript
const [count, setCount] = useState < number > 0;
```

- Generics 를 사용하여 타입을 명시해준다.

- BUT! 타입스크립트에서 알아서 타입을 유추해준다. => 안써줘도 무방함.

- 그렇다면 언제 Generics 를 쓸까?

  > 상태가 `null` 일 수도 있고 아닐 수도 있을 때
  >
  > 상태의 타입이 까다로운 구조를 가진 객체이거나 배열일 때

```javascript
type Information = { name: string; description: string };
const [info, setInfo] = useState<Information | null>(null);
type Todo = { id: number; text: string; done: boolean };
const [todos, setTodos] = useState<Todo[]>([]);
```

#### (2) Input 관리

```javascript
import React, { useState } from "react";

type MyFormProps = {
  onSubmit: (form: { name: string, description: string }) => void,
};

function MyForm({ onSubmit }: MyFormProps) {
  const [form, setForm] = useState({
    name: "",
    description: "",
  });
  const { name, description } = form;
  const onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
    const { name, value } = e.target;
    setForm({
      ...form,
      [name]: value,
    });
  };
  const handleSubmit = (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    onSubmit(form);
    setForm({
      name: "",
      description: "",
    });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" value={name} onChange={onChange} />
      <input name="description" value={description} onChange={onChange} />
      <button type="submit">등록</button>
    </form>
  );
}

export default MyForm;
```

```javascript
import React from "react";
import MyForm from "./MyForm";

const App: React.FC = () => {
  const onSubmit = (form: { name: string, description: string }) => {
    console.log(form);
  };
  return <MyForm onSubmit={onSubmit} />;
};

export default App;
```

> `input` 의 event type 은 `React.~~Event` 형태로 지정되는데 마우스를 props 에 올리면 자동으로 보여준다!

<img src="https://user-images.githubusercontent.com/60836178/96148044-d7efb080-0f42-11eb-93ab-976e21d5f641.jpg" alt="타입스크립트 자동완성" style="zoom:50%;" />

#### (3) useReducer

```javascript
import React, { useReducer } from "react";

type Action = { type: "INCREASE" } | { type: "DECREASE" };

function reducer(state: number, action: Action): number {
  switch (action.type) {
    case "INCREASE":
      return state + 1;
    case "DECREASE":
      return state - 1;
    default:
      throw new Error("Unhandled action");
  }
}

function Counter() {
  const [count, dispatch] = useReducer(reducer, 0);
  const onIncrease = () => dispatch({ type: "INCREASE" });
  const onDecrease = () => dispatch({ type: "DECREASE" });

  return (
    <div>
      <h1>{count}</h1>
      <div>
        <button onClick={onIncrease}>+1</button>
        <button onClick={onDecrease}>-1</button>
      </div>
    </div>
  );
}

export default Counter;
```

> `state` 의 타입과 함수의 리턴 타입을 동일하게 하여 실수를 방지한다!!
