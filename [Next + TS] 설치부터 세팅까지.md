# [Next.js + TypeScript] 설치부터 세팅까지

Status: Nextjs

### 1. CNA 로 프로젝트 만들기

---

`yarn create next-app {PROJECT-NAME}`

### 2. 타입스크립트 설치하기

---

- 넥스트에서 설정한 TS로 만들고 싶으면 next-env.d.ts 를 생성
- 세팅을 커스텀하고 싶으면 tsconfig.json 생성
- 둘 다 하고 싶으면 둘 다 생성해주자.
    
    `touch next-env.d.ts tscondig.json`
    
- 패키지 설치 | `npm install --save-dev typescript @types/react @types/node` || `yarn add --dev typescript @types/react @types/node`
- 이렇게 하면 설치 끝.

### 3. 기존 설정 타입스크립트로 전환(컨버팅)

---

- /pages 에 _app.tsx 생성 후 다음과 같이 작성
    
    ```jsx
    import { AppProps } from 'next/app'
    
    function App({ Component, pageProps }: AppProps) {
      return <Component {...pageProps} />
    }
    
    export default App
    ```
    
- 여기까지 하면 기존 실행되던 앱에서 오류가 나있을 것이다. 왜냐면 _app.js 로 실행되고 있기 때문. ⇒ 당황하지 말고 종료 후 재시작하자.
- 재실행하면 tsconfig.json 이 알아서 다음과 같이 세팅 되어있을 것이다.
    
    ```jsx
    {
      "compilerOptions": {
        "target": "es5",
        "lib": [
          "dom",
          "dom.iterable",
          "esnext"
        ],
        "allowJs": true,
        "skipLibCheck": true,
        "strict": false,
        "forceConsistentCasingInFileNames": true,
        "noEmit": true,
        "esModuleInterop": true,
        "module": "esnext",
        "moduleResolution": "node",
        "resolveJsonModule": true,
        "isolatedModules": true,
        "jsx": "preserve"
      },
      "include": [
        "next-env.d.ts",
        "**/*.ts",
        "**/*.tsx"
      ],
      "exclude": [
        "node_modules"
      ]
    }
    ```
    
    타입스크립트 설정 세부 내용은 구글링으로 확인하자.
    
    (이미 엄청 많이 봤지만 까먹음..)
    
- index.js 를 index.tsx 로 바꾸고 다음과 같이 작성해보자.
    
    ```jsx
    import React, { useState } from "react";
    import Head from "next/head";
    
    export default function Home() {
      const [text, setText] = useState<string>("자바스크립트");
    
      setTimeout(() => {
        setText("타입스크립트");
      }, 1000);
    
      return (
        <div className="container">
          <div>
            <span>{text} 적용 완료</span>
          </div>
        </div>
      );
    }
    ```
