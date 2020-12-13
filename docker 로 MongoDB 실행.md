## 도커로 MongoDB 실행

맥 빅서에서 로컬로 몽고db를 실행하려다 자꾸 연결 실패하고 이상한 에러가 떠서 포기하고 도커로 시도했다.



##### 1. MongoDB 이미지 받아오기 (설치)

`docker pull mongo`



##### 2. MongoDB 서버 실행

`docker run -d --name mongo-db -v /data:/data/db -p 27017:27017 mongo:4.2`

4.2로 실행



❗️에러 발생

`The path /data is not shared from the host and is not known to Docker.`

당황하지 말자.. 권한에러다.

`/data` 경로에 도커가 접근 권한이 없다는 것이다.

![Screenshot_2020-12-13 13.19.32_hxK3hQ](https://github.com/uu29/TIL/blob/main/images/Screenshot_2020-12-13%2013.19.32_hxK3hQ.png?raw=true)

로그를 찬찬히 뜯어보고 도커 앱 설정에서 `/data` 경로를 추가해줬다.

![Screenshot_2020-12-13 13.28.45_k5R9Im](https://github.com/uu29/TIL/blob/main/images/Screenshot_2020-12-13%2013.28.45_k5R9Im.png?raw=true)



##### 3. MongoDB 컨테이너 접속

`docker exec -it mongo-db /bin/bash`

이렇게 하니 터미널창 네임이 해당 서버로 바뀐다.

`env`를 치니 db 정보가 나온다.

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2020-12-13%2013.33.26_ccRvoB.png?raw=true" alt="Screenshot_2020-12-13 13.33.26_ccRvoB" style="zoom:50%;" />

##### 4. MongoDB 접속

`mongo` 

<img src="https://github.com/uu29/TIL/blob/main/images/Screenshot_2020-12-13%2013.34.33_Y7a7Sr.png?raw=true" alt="Screenshot_2020-12-13 13.34.33_Y7a7Sr" style="zoom:50%;" />

이렇게 `>` 가 나오면 성공적으로 접속된 것이다!



#### 도움받은 곳

https://msyu1207.tistory.com/entry/Docker-docker-DB-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EA%B8%B0-mongodb-%EC%84%A4%EC%B9%98-%EC%99%B8%EB%B6%80%EC%97%90%EC%84%9C-db-%EC%A0%91%EC%86%8D