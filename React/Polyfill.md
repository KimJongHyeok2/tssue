React는 기본적으로 IE9, IE11 같은 오래된 웹브라우저를 지원하지 않는 사실을 알게되었다. 그리고 이 것을 해결할 방법은 있으나 성능 등의 문제가 있을 수 있다고 한다.

<pre>
npm install react-app-polyfill
</pre>

index.js 최상단에 import 한다.

<h3>Internet Explorer 9</h3>
<pre>
import 'react-app-polyfill/ie9';
</pre>
<h3>Internet Explorer 11</h3>
<pre>
import 'react-app-polyfill/ie11';
</pre>

react-app-polyfill에 대한 자세한 내용은 <a href="https://github.com/facebook/create-react-app/tree/master/packages/react-app-polyfill">여기서</a> 확인
