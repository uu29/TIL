## 리액트네이티브 스크롤뷰 구현하기

웹에서는 콘텐츠가 브라우저 높이보다 길어지면 자연스럽게 스크롤바가 생기면서 콘텐츠 높이만큼 `<body>` 가 길어진다.

그러나 리액트네이티브에서는 그게 당연한게 아니다.

따로 스크롤이 가능하게 하는 컴포넌트를 추가해줘야 하는데, `<ScrollView>` 와 `<FlatList>` 두 개가 있다.



#### ScrollView

스크롤뷰는 로딩이 시작될 때 부터 스크린 밖의 컴포넌트까지 한꺼번에 렌더링한다.

화면을 벗어난 영역에 있는 컴포넌트까지 그린다는 것으로, 당연히 퍼포먼스를 떨어트릴 수 밖에 없다.

주로 Array 같은 데이터를 나열하기보다는, 어떤 Text 가 양이 길어 화면을 벗어나게 될 경우 사용한다.



#### FlatList

플랫리스트는 스크린 영역 밖의 컴포넌트는 렌더링하지 않고, 사용자의 인터렉션에 따라 스크린에 보여지게 될 컴포넌트만 렌더링한다.

예를 들어 무수히 많은 리스트가 있을 때, 첫 로딩 시에는 딱 스크린에서 보이는 영역 만큼만 화면에 그리고,

그 뒤로 고객이 아래로 스와이핑을 했을 경우 맨 위에 있던 컴포넌트는 사라지고 그 아래 있는 컴포넌트가 렌더링된다는 뜻이다.

한꺼번에 모든 컴포넌트를 렌더링하지 않으므로 메모리를 절약할 수 있기 때문에 퍼포먼스가 좋다.

따라서 무한스크롤이 필요한 경우나 길이를 예측할 수 없는 Array 같은 데이터를 렌더링 할 때 많이 쓴다.

장바구니 상품이라든가 뉴스 목록, 증권앱의 관심종목 리스트 등이 그 예다.



**[사용 예시]**

```jsx
import React from 'react';
import {View, Text, StyleSheet, FlatList} from 'react-native';

export default function ItemList() {
  const data = [
    {
      title: '테슬라',
      price: 700.77,
      upanddown: -10.59,
      percentage: -1.66,
    },
    {
      title: '엔비디아',
      price: 700.77,
      upanddown: -10.59,
      percentage: -1.66,
    },
    {
      title: '삼성전자',
      price: 700.77,
      upanddown: -10.59,
      percentage: -1.66,
    },
  ]; //길이가 긴 Array 라고 가정
  return (
    <FlatList
      data={data}
      renderItem={({item, i}) => (
        <View style={styles.container} key={i}>
          <View>
            <Text style={styles.itemNameText}>{item.title}</Text>
          </View>
          <View>
            <Text style={styles.itemPriceText}>{item.price}</Text>
            <Text style={styles.itemPercentText}>
              {item.upanddown} ({item.percentage}%)
            </Text>
          </View>
        </View>
      )}
    />
  );
}

const styles = StyleSheet.create({
  container: {
    display: 'flex',
    flexDirection: 'row',
    justifyContent: 'space-between',
    marginTop: 12,
    marginHorizontal: 12,
    paddingVertical: 12,
    paddingHorizontal: 14,
    height: 86,
    shadowColor: '#f1f2f3',
    shadowOffset: {
      width: 0,
      height: 0,
    },
    shadowOpacity: 1,
    shadowRadius: 18.95,
    elevation: 1,
    zIndex: 1,
    backgroundColor: 'white',
    borderRadius: 12,
    borderColor: '#F2F3F4',
    borderWidth: 1,
  },
  itemNameText: {
    fontSize: 20,
  },
  itemPriceText: {
    textAlign: 'right',
    fontSize: 28,
    fontWeight: '700',
  },
  itemPercentText: {
    paddingTop: 6,
    textAlign: 'right',
    fontSize: 15,
    color: '#2090F8',
    fontWeight: '500',
  },
});
```



필수 Props 는 `data`, `renderItem` 이며 data 는 렌더링할 배열, renderItem 은 데이터를 인자로 받아 컴포넌트로 리턴해주는 함수다.

`renderItem({ item, index, separators });`

renderItem 은 item, index, separators 을 인자로 갖는다.

<img src="/Users/yuriahn/TIL/images/Screenshot_2021-01-06 23.10.58_7uoYHw.png" alt="Screenshot_2021-01-06 23.10.58_7uoYHw" style="zoom:50%;" />



FlatList 로 스크롤뷰를 구현한 결과 화면이다.

<img src="https://github.com/uu29/TIL/blob/main/images/Simulator%20Screen%20Shot%20-%20iPhone%2011%20-%202021-01-07%20at%2020.31.35.png?raw=true" style="zoom:50%;" />



- 공식문서: https://reactnative.dev/docs/scrollview