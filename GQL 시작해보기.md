# GQL 시작해보기

https://alialhaddad.medium.com/beginners-guide-to-graphql-in-react-native-react-1-3-21bd431e0fc7

1. 설치

- 아무 서버에서 `mkdir server && cd server touch index.js`

- devDependencies: `npm i -D nodemon babel-cli babel-preset-es2015 babel-preset-stage-2`
- dependencies: `npm i -S apollo-server graphql graphql-tools mongoose dotenv`

2. 의존성 설명

- apollo-server: graphql을 이용하여 서버를 시작. 데이터 모양을 정의하고 가져오는데 사용됨
- graphql: graphql 스키마 언어를 활성화 함
- graphql-tools: 아폴로 서버에서 백틱(`)을 쓸 수 있도록 함
- dotenv: .env 파일을 활성화 함

3. 스크립트 명령어 추가

```javascript
"scripts": {
    "start": "nodemon --watch server --watch package.json server/index.js --exec babel-node --presets es2015,stage-2"
  },
```

`yarn start`로 서버 실행하기

<img src="https://i.imgur.com/HJjuvNf.png" style="zoom:50%;" />

4. /server/ 에서 파일 만들기

- typeDefs.ts: 스키마에 대한 타입 정의
- resolvers.ts: 스키마 필드가 실행되는 방법 설정
- connetors.ts: db 연결 및 데이터를 검색할 플레이어 모델 정의

5. DB 연결

