# 마인드맵 라이브러리 cytoscape.js

###### 노드 하나가 데이터의 단위이다.

트리형식으로 뻗어나간다고 생각하면 됨.

1. 시작할 노드를 만든다.

`GraphContainer.tsx`

```js
const newNode = {
	data: {
		id: `node-${uuidv4()}`,
		generation: newGeneration
	}
};
```

* uuidv4는 uuid를 만드는 npm 모듈임. uuid는 128비트 32자리 16진수로 표현된 숫자로, 범용고유식별자임.

하나의 노드는 data 객체 안에 표현할 수 있고 id를 기본적으로 가지며 이외 다른 정보도 object 형식으로 표현할 수 있다.

2. 데이터 구성

데이터는 `node`와 `edge`로 구성되어 있음.

node는 데이터 자체이고 edge는 노드와 노드를 이어주는 연결선임.

```js
// node
"data": {
  "id": {ID},
  "url": {url},
  "label": {라벨}
}
// edge
"data": {
  "id": {ID},
  "source": {하위 노드 주소},
  "target": {상위 노드 주소}
}
```

- edge는 source node 에서 target node를 이어준다. 따라서 `source` 와 `target` 값이 필수이며, 해당하는 노드의 주소를 가지고 있다.

이 때 주소는 id에 속한다. 즉, 연결할 노드의 id만 제대로 가지고 있으면 끝!



