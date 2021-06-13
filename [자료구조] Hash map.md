# [자료구조] Hash map

https://betterprogramming.pub/stop-using-objects-as-hash-maps-in-javascript-9a272e85f6a8

- Map 객체는 키-값 쌍을 저장하며 각 쌍의 삽입 순서도 기억하는 콜렉션

보통 작업할 때 object 형태의 객체를 만들어서 많이 사용함.

그러나 javascript에서 제공하는 map 형태를 사용하면 성능, 개발 편의성 면에서 더 좋다.

### 1. **key 유형이 더 많음.**

Object는 키로 string만 지정할 수 있음. but Map은 어떤 형태든 key로 사용 가능.

<img src="https://i.imgur.com/SJWpJn4.png" alt="https://i.imgur.com/SJWpJn4.png" style="zoom:50%;" />

### 2. **size 사용 가능**

Object는 size를 확인 할 수 없으나 Map은 가능

<img src="https://i.imgur.com/9yzq2gS.png" alt="https://i.imgur.com/9yzq2gS.png" style="zoom:50%;" />

`map.size` 메소드를 통해 크기 측정 가능, object는 `Object.keys` 로 돌린 뒤 length를 구해야 한다.

### 3. 성능

Map은 데이터 추가/제거에 최적화되어 설계되었기 때문에 Object보다 성능이 더 좋음. 실제 맥북프로로 1000만개로 테스트 했을 때 Object는 1.6초가 나온 반면, Map은 1ms 이하로 나옴.

### 4. 반복문 사용이 좀 더 편리

Map이 반복문 돌리기 더 편하다.

```tsx
## Object의 반복문:
for(let [key, value] of Object.entries(obj)){}

## Map의 반복문:
for(let [key, value] of map){}
```

### 5. 순서 보장

es2015 이전 문법에서 Object는 순서를 보장하지 않음.

### 6. 덮어쓰기 금지

