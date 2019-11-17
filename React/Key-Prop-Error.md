map() 메소드로 배열을 렌더링하였더니 콘솔에서 아래와 같은 에러가 나타났다.
<pre>
Each child in an array or iterator should have a unique "key" prop.
</pre>

React에서는 DOM 트리를 업데이트할 때 Key 속성을 활용한 diff 알고리즘을 통해 렌더링을 최적화한다고 한다. 따라서 DOM 노드의 각 자식 엘리먼트에 Key 속성이 없으면 해당 diff 알고리즘을 활용하지 못하고 렌더링 성능에 영향을 줄 수 있기 때문에 위와 같은 에러가 나타나는 것이었다.

```javascript
const array = ['A', 'B', 'C'];

array.map((item) => (
  <li key={item.toString()}>{item}</li>
));
```
명확한 식별자가 없을 때 색인을 사용하는 경우 차선책으로 아래와 같이 고유 식별값을 지정해주지만 권장하지 않는다.
```javascript
const array = ['A', 'B', 'C'];

array.map((item, index) => (
  <li key={index}>{item}</li>
));
```
