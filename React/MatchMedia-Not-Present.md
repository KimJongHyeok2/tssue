IE9 환경에서 <a href="https://github.com/KimJongHyeok2/tssue/blob/master/React/IE9-History.md">History API 관련 Issue</a>를 해결하였더니 또 다른 오류가 발생하였다.
<pre>
matchMedia not present, legacy browsers require a polyfill
</pre>
아래의 코드를 적용하였더니 해결되었다.
<pre>
window.matchMedia = window.matchMedia || function() {
  return {
  matches : false,
  addListener : function() {},
  removeListener: function() {}
  };
};
</pre>
## 레퍼런스
https://github.com/akiran/react-slick/issues/742
