# 리액트에서 레디스 사용하기

#### 1. 레디스 설치 및 실행

- 설치

  macOS: `brew install redis`

  linux: `sudo apt-get install redis-server`

- 실행

  `redis-server`

  6379 포트가 열리고 레디스가 실행된다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2020-11-03%2008.57.54_03XDL3.png?raw=true" alt="Screenshot_2020-11-03 08.57.54_03XDL3" style="zoom:50%;" />

- 테스트

  `redis-cli` 로 서버에 들어가서 명령어를 입력한다.

  ```bash
  set bluebottle hot-americano ## 메모리를 키-밸류로 저장하기
  > OK ## 성공적으로 저장함
  get bluebottle ## 저장한 메모리 불러오기
  "hot-americano" ## 키에 해당하는 메모리
  ```

  <img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2020-11-03%2009.02.33_q9wSDG.png?raw=true" alt="Screenshot_2020-11-03 09.02.33_q9wSDG" style="zoom:50%;" />



#### 2. 노드에서 레디스 접근하기

- 설치

  `npm init`

  `npm install redis`

  `npm install JSON`

  의존성은 다음과 같이 설치(package.json)

  ```json
  {
    "name": "redis-node",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "dependencies": {
      "JSON": "^1.0.0",
      "body-parser": "^1.19.0",
      "cookie-parser": "^1.4.5",
      "debug": "^4.2.0",
      "express": "^4.17.1",
      "jade": "^1.11.0",
      "morgan": "^1.10.0",
      "redis": "^3.0.2",
      "serve-favicon": "^2.5.0"
    },
    "devDependencies": {},
    "scripts": {
      "start": "node ./bin/www"
    },
    "author": "",
    "license": "ISC"
  }
  ```

- 웹 프로젝트 생성

  app.js

  ```javascript
  var redis = require("redis");
  var JSON = require("JSON");
  // 레디스와 연결하는 클라이언트 생성
  client = redis.createClient(6379, "127.0.0.1");
  // req.cache 객체에 클라이언트를 저장
  app.use(function (req, res, next) {
    req.cache = client;
    next();
  });
  
  app.post("/profile", function (req, res, next) {
    req.accepts("application/json");
    var key = req.body.name;
    var value = JOSN.stringify(req.body); // JSON 객체를 문자열로 변환하여 value 객체로 저장
    req.cache.set(key, value, function (err, data) {
      if (err) {
        console.log(err);
        res.send(`error: ${err}`);
        return;
      }
      req.cache.expire(key, 10); // 캐시 메모리의 유지 시간을 10초로 설정
      res.json(value);
      console.log(value);
    });
  });
  
  app.get("/profile/:name", function (req, res, next) {
    var key = req.params.name;
    req.cache.get(key, function (err, data) {
      if (err) {
        console.log(err);
        res.send(`error: ${err}`);
        return;
      }
      var value = JSON.parse(data);
      res.json(value);
    });
  });
  ```

  