# Javascript 호이스팅에 관하여

호이스팅이란? 함수를 먼저 실행시키고 함수선언문(Function Declarations)을 나중에 작성해도 자바스크립트에서 해당 함수를 실행시킬 수 있는 것.

→ JS 컴파일 시점에 나중에 선언된 함수에 대한 메모리를 할당해놓은 상태이기 때문에, 실행 시점에 선언된 함수의 식별자 정보를 이미 알고 있음.

- 선언된 함수는 상단에서 참조, **호출**이 가능하다.
- 선언된 var는 상단에서 참조, **할당**이 가능하다.
- 선언된 let, const는 상단에서 참조, 할당이 불가능하다.

🙋 왜 var는 되고 let, const는 안되나?
변수가 할당되기까지는 선언/초기화/할당의 3가지 단계를 거치는데, var는 호이스팅이 발생하면 선언과 **초기화**가 거의 동시에 이루어져서 실행 시점에 스코프 최상단에서 해당 변수에 대한 메모리가 살아있기 때문에 참조, 할당이 가능한 반면, let, const는 호이스팅이 발생 시 선언만 이루어지기 때문에 _선언부를 만나기 전 까지는 초기화가 이루어지지 않은_ 상태. 따라서 해당 변수에 대한 메모리가 존재하지 않기 때문에 선언하기전에는 참고, 할당이 불가능

\*\* let, const 선언문 전에 호출하는 경우 ⇒ 참조에러 발생

![https://i.imgur.com/g3MH456.png](https://i.imgur.com/g3MH456.png)

_실제로 let 또는 const로 선언한 함수표현식을 상단에서 사용하려고 하면 초기화 하기 전에 접근할 수 없다는 ReferenceError가 나온다._

→ 위 현상을 TDZ(Temporal Dead Zone), 일시적 사각지대라고 한다. 변수는 존재하지만 초기화되어 있지 않은 상태이다.

\*\* var에 함수를 할당하고 나중에 호출하는 경우 ⇒ 타입에러 발생

![https://i.imgur.com/UOTshiP.png](https://i.imgur.com/UOTshiP.png)

_var 변수 sayHi는 참조는 가능하지만, 초기화만 이루어지고 함수가 할당이 되지 않은 상태이기 때문에 TypeError가 발생_

<aside>
📌 **선언 → 초기화 → 할당**
선언: 파싱 과정에서 변수 객체가 변수에 대한 식별자들을 수집하는 일
*초기화: 식별자에 메모리를 할당하고 undefined 상태를 부여함
할당: 변수 안에 직접 값을 넘거줌

</aside>


- 출처
  [Arrow function | PoiemaWeb](https://poiemaweb.com/es6-arrow-function)
  [[JS] ReferenceError / TypeError](https://velog.io/@exploit017/JS-ReferenceError-TypeError)
  [함수 표현식 vs 함수 선언식](https://joshua1988.github.io/web-development/javascript/function-expressions-vs-declarations/)
  [호이스팅에 대한 오해와 진실](https://tecoble.techcourse.co.kr/post/2021-04-25-hoisting/)
  [JavaScript, 인터프리터 언어일까?](https://oowgnoj.dev/review/advanced-js-1)
