# [Next+TS] tutorial

- 목표 | Next 공식 문서를 보면서 타입스크립트로 된 블로그를 만들어보자.



#### (1) 준비하기

- 설치 | `npx create-next-app nextjs-blog --use-npm --example "https://github.com/vercel/next-learn-starter/tree/master/basics-final"`

  > 이렇게 하면 Next + JS 로 된 블로그가 만들어진다.
  >
  > 여기에 TS 설정파일을 추가해야만 타입스크립트를 사용할 수 있다.

- TS 세팅 | 루트 경로에 tsconfig.json 파일 생성

  > tsconfig.json 만 만들고 아무것도 하지 않고 실행하면 다음과 같은 에러가 뜬다.

  <img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2020-10-18%2013.04.40_pPO1We.png?raw=true" style="zoom:50%;" />

  > TS를 설치하고 패키지 파일을 설정하라는 뜻이다.

- TS 설치 | `npm install --save-dev typescript @types/react @types/node` || `yarn add --dev typescript @types/react @types/node`

  > 이렇게 하면 tsconfig.json 파일은 마음대로 커스터마이즈 할 수 있다.(ts 설정파일)
  >
  > next-env.d.ts 을 생성하고 건들지 말 것.

- 타입스크립트 설치 후 재실행(yarn dev) 하면 에러 없이 정상 실행 되는 것을 확인할 수 있다.

  <img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2020-10-18%2013.11.11_5EiOcC.png?raw=true" style="zoom:50%;" />



#### (2) 기존에 JavaScript 로 되어있던 것들 TypeScript 로 바꾸기

- SSR 설정 시 유의할 것

  > `getStaticProps`, `getStaticPaths`, `getServerSideProps` 를 사용하려면 이렇게 해야한다.

  ```javascript
  import { GetStaticProps, GetStaticPaths, GetServerSideProps } from 'next'
  
  export const getStaticProps: GetStaticProps = async context => {
    // ...
  }
  
  export const getStaticPaths: GetStaticPaths = async () => {
    // ...
  }
  
  export const getServerSideProps: GetServerSideProps = async context => {
    // ...
  }
  ```

- App.js 파일 컨버팅하기

  > _app.js 를 _app.tsx 로 바꾸고 `AppProps` 라는 빌트인 타입을 써본다.

  ```javascript
  function App({ Component, pageProps }: AppProps) {
    return <Component {...pageProps} />
  }
  
  export default App
  ```

- 나머지 다른 파일들 모두 ~.tsx 로 바꾸기

  