- 설치: `yarn add @emotion/react @emotion/native`
- 사용:

```
import styled, { css } from '@emotion/native';
...

const Percent = styled.Text`
  padding-top: 6px,
  text-align: 'right',
  font-size: 15px,
  font-weight: 500,
  color: ${(props) => (props.isNegative ? '#2090F8' : '#DF281D')}
`;

...

export default function ItemList({item}) {
  return (<Percent
              isNegative={Math.sign(item.rMarketChange) === -1 ? true : false}>
              {item.rMarketChange}
           </Percent>       
  );
}
```

그냥 react jsx에서 하던 것과 똑같이 하면 된다.