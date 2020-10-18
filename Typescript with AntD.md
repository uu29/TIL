# TypeScript 에서 Ant Design 사용해보기(1)

[목표] 타입스크립트 CRA 를 이용하여 Ant Design 레이아웃을 써본다.

#### (1) 세팅

- CRA로 프로젝트 설치 | `yarn create react-app antd-demo-ts --template typescript` || `npx create-react-app my-app --template typescript` || `npx create-react-app antd-demo-ts --typescript`
- 프로젝트에 antd 설치 | `yarn add antd`
- 컴포넌트에서 가져오기 | `import {원하는 컴포넌트} from 'antd';`
- css 적용하기 | css 파일 상단에 `*@import* "~antd/dist/antd.css";`



#### (2) <Button> 컴포넌트를 사용한 예시

```javascript
import React, { FC } from "react";
import { Button } from "antd";
import "./App.css";

const App: FC = () => (
  <div className="App">
    <Button type="link">Link Button</Button>
    <Button type="text">Text Button</Button>
    <Button type="ghost">Ghost Button</Button>
  </div>
);

export default App;
```



#### (3) Craco 를 사용한 커스터마이징

- antd 을 그대로 사용하기에는 실무에 무리가 있기 때문에 Craco 를 설치하여 웹팩 설정을 수정할 수 있다.

- 설치 | `yarn add @craco/craco`

- package.json 에서 스크립트 실행 명렁어를 다음과 같이 수정

  ```json
  "start": "craco start",
  "build": "craco build",
  "test": "craco test",
  ```

- 루트 디렉토리에 craco.config.js 생성, 여기에 설정을 덮어씌울 수 있다.

- less 설치 후 모든 css 를 less 로 바꾸기 | `yarn add craco-less` | `import './App.less';` | `@import '~antd/dist/antd.less';`

- 설정 파일 커스터마이징(craco.config.js)

  ```javascript
  const CracoLessPlugin = require("craco-less");
  
  module.exports = {
    plugins: [
      {
        plugin: CracoLessPlugin,
        options: {
          lessLoaderOptions: {
            lessOptions: {
              modifyVars: { "@primary-color": "#1DA57A" },
              javascriptEnabled: true,
            },
          },
        },
      },
    ],
  };
  ```

  > 이렇게 하면 메인 컬러가 '#1DA57A' 로 바뀐다!

  <img src="https://user-images.githubusercontent.com/60836178/96357629-7f363880-1139-11eb-9e5a-e2d3edca2209.png" alt="craco 로 커스텀한 이미지" style="zoom:50%;" />

#### (4) 메뉴 레이아웃 사용해보기

- 공식문서를 보고 추가로 레이아웃을 임포트 한 뒤, Header - Content - Footer 의 레이아웃을 만들어보았다.

  ```javascript
  import React, { FC } from "react";
  import { Layout, Menu, Button } from "antd";
  import "./App.less";
  
  const App: FC = () => {
    const { Header, Footer, Sider, Content } = Layout;
  
    return (
      <Layout className="App">
        <Header>
          <Menu theme="dark" mode="horizontal" defaultSelectedKeys={["2"]}>
            <Menu.Item key="1">nav 1</Menu.Item>
            <Menu.Item key="2">nav 2</Menu.Item>
            <Menu.Item key="3">nav 3</Menu.Item>
          </Menu>
        </Header>
        <Content>
          <Button type="link">Link Button</Button>
          <Button type="text">Text Button</Button>
          <Button type="ghost">Ghost Button</Button>
        </Content>
        <Footer>Footer</Footer>
      </Layout>
    );
  };
  
  export default App;
  ```

- 메인 컬러를 craco.config.js 에서 #760100 로 바꾸고 다음과 같이 완성했다.

<img src="https://user-images.githubusercontent.com/60836178/96357896-4481cf80-113c-11eb-877d-7068bbb11e0d.png" alt="커스텀 레이아웃" style="zoom:50%;" />

