크론탭은 어떤 스크립트를 특정 기간 또는 시간 마다 가동시킬 때 사용하는 것으로 맥이나 리눅스에는 내장되어있다.

내 프로젝트에 크롤러가 있는데 나중에 크론탭으로 상태체크, 재실행 등을 해야할 것 같아서 오늘 테스트를 해보았다.



##### **[Mac OS에서 크론탭 실행하기]**



1. `crontab -e`

![Screenshot_2021-01-12 20.47.47_FWaYT0](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-12%2020.47.47_FWaYT0.png?raw=true)

​	터미널을 키고(나는 zsh을 사용했다) `crontab -e` 명령어를 실행한다. 돌고 있는 크론탭이 없으면 vi/vim 편집기가 열리고 빈파일이 생성된다.

2. 크론탭 스크립트 실행: 

   크론탭의 주기는 {분} {시} {일} {월} {요일} 이다. 구글링하면 더 자세히 나온다.

   (1분 마다 실행시키고 싶으면) `* * * * * {가상환경 경로} {실행시킬 파일 경로} >> {저장할 로그파일 경로} 2>&1`

   wq 로 저장 후 나간다.

   ```bash
   * * * * * ~/Projects/lazy-ant/scrapper/scrapper/bin/python ~/Projects/lazy-ant/scrapper/main.sh >> ~/Projects/lazy-ant/scrapper/main.sh.log 2>&1
   ```

   

3. `crontab -l`

   ![Screenshot_2021-01-12 22.01.16_IKs0gM.png](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-12%2022.01.16_IKs0gM.png?raw=true)

   방금 수정한 크론탭 설정이 잘 적용되었는지 확인한다.

4. `cat ~/Projects/test/python-crontab/test.sh.log`

   ![Screenshot_2021-01-12 20.52.27_ZvO4le.png](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-12%2020.52.27_ZvO4le.png?raw=true)

   (몇번의 실수 끝에)성공적으로 쌓이는 로그를 볼 수 있다.



하고 보니 방법이 아주 간단한데 이 과정에서 늘 그랬듯이 몇가지 에러가 발생해서 한큐에 성공하진 못했다.

**[크론탭 실행 과정 에러]**

1. `no crontab for {user} - using an empty one`

   `"/usr/bin/vi" exited with status 1`

   ![Screenshot_2021-01-12 20.33.02_aMFo2J.png](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-12%2020.33.02_aMFo2J.png?raw=true)

   - 원인: 배쉬 설정파일에 기본 편집기를 지정하지 않아서이다.
   - 해결: vi/vim 둘 중 하나를 지정해주자. 나는 zsh 를 쓰고 있었는데 `vim ~/.zshrc` 로 들어가서 `export EDITOR=vim` 을 추가해주었다. 해결 완료!

2. `No such file or directory`

   ![Screenshot_2021-01-12 20.37.11_MaVZfU.png](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-12%2020.37.11_MaVZfU.png?raw=true)

   - 원인: crontab 설정을 잘못해서 그런거다. 파일명/경로를 제대로 확인하자.
   - 해결: 나는 처음에 {파이썬 경로} {실행시킬 파일 경로} 로 했었는데 파이썬 경로가 잘못되었었다. 그리고나서는 테스트로 작성한 test.sh 파일을 실행해주었었는데 내가 test.py 파일로 만들었어서 당연히 파일을 못찾을 수 밖에 없었다. 왜냐? test.sh 파일은 없는 파일이니까^^

3. `/bin/sh: /Users/{user}/Projects/test/python-crontab/test.py: Permission denied`

   ![Screenshot_2021-01-12 20.40.19_IU4VU1.png](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-12%2020.40.19_IU4VU1.png?raw=true)

   - 원인: 권한문제. 크론탭이 해당 파일을 실행할 권한이 없다. Permission Error

   - 해결: 해당 파일에 실행(x) 권한을 넣어주면 된다.

     `chmod +x {파일경로}`

4. `syntax error near unexpected token 'hi' `

   ![Screenshot_2021-01-12 20.43.33_ecSw0Q.png](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-12%2020.43.33_ecSw0Q.png?raw=true)

   - 원인: 구글링해보니 스크립트가 파이썬을 해석하지 못해서 발생하는 것 같은데, 파이썬 가상환경에서 실행하지 않아서 그런 듯 하다(https://stackoverflow.com/questions/10676050/bash-syntax-error-near-unexpected-token-python)
   - 해결: 코드 맨 위에 `#!/usr/local/bin/python3` 을 삽입해주었다.

5. `ModuleNotFoundError: No module named 'requests'`

![Screenshot_2021-01-12 22.02.51_DVoFYA.png](https://github.com/uu29/TIL/blob/main/images/Screenshot_2021-01-12%2022.02.51_DVoFYA.png?raw=true)

  - - 원인: 파이썬 가상환경 경로를 지정해주지 않아서

    - 해결: crontab 에서 실행할 경로 앞에 가상환경 경로를 먼저 적어준다.

      `* * * * * {가상환경 경로} {실행시킬 파일 경로} >> {저장할 로그파일 경로} 2>&1`

