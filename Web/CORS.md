React 환경에서 외부 서버의 경로로 Ajax 요청을 보내 보았는데 아래와 같은 메시지를 보게 되었다. (크롬 기준)
<pre>
Failed to load https://serverhost.com/: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'https://www.zerocho.com' is therefore not allowed access. If an opaque response serves your needs, set the request's mode to 'no-cors' to fetch the resource with CORS disabled.
</pre>

이는 자바스크립트 엔진 표준 스펙에 동일 출처 정책(Same-Origin Policy)이라는 보안 규칙이 있기 때문이다. 이 정책에 의해서 자바스크립트(XMLHttpRequest)로 다른 웹페이지에 접근하는 경우 같은 페이지만 접근이 가능하다. 같은 출처라는 것은 프로토콜, 호스트명, 포트가 같다는 것을 의미한다. 즉, 쉽게 말하면 웹페이지의 스크립트는 그 페이지와 같은 서버에 있는 주소로만 Ajax 요청을 할 수 있다는 것이다.

## CORS(Cross Origin Resource Sharing)
브라우저의 현재 웹 페이지가 이 페이지를 받은 서버가 아닌 다른 서버의 자원을 호출하는 것을 의미한다. 가장 쉬운 예로 CDN에 배포되어 있거나 공용 이미지 등을 그냥 단순 Link를 걸어서 사용하는 것도 CORS라고 할 수 있다.

<h3>클라이언트단에서 해결</h3>
<ul>
  <li>
    1. 웹 브라우저에서 동일 출처 정책 확인X
    <ul>
      <li>동일 출처 정책 확인은 클라이언트에서 하는 것이다보니, 브라우저에서 확인을 안하겠다고 하면 Response를 확인할 수 있다.</li>
    </ul>
  </li>
  <li>
    2. 크롬 확장플러그인
    <ul>
      <li>크롬 웹 스토어에서 CORS를 검색하여 확장 플러그인을 설치하면 된다.</li>
    </ul>
  </li>
  <li>
    3. JSONP 방식의 요청
    <ul>
      <li>웹 브라우저에서 리소스(CSS, JS) 파일들은 SOP 영향없이 로딩이 가능하다. 이런 점을 이용해 서버 응답을 JSON 파일로 바꿔 로딩하는 방식이다. 단점은 리소스 파일을 Get으로만 읽어오기 때문에, GET 방식의 API 요청만 가능하다.</li>
    </ul>
  </li>
</ul>

<h3>서버단에서 해결</h3>

클라이언트는 서버에게 Preflight라는 사전 요청을 보낸다. HTTP 메소드는 OPTIONS를 사용하여, HOST에 클라이언트 주소를, Origin에 서버주소를 넣어 보낸다. 이를 통해 권한이 있는지 확인할 수 있다. 서버는 Preflight 요청을 핸들링하여 CORS를 설정해주면 된다.
<ul>
  <li><a href="https://engkimbs.tistory.com/781">Spring Boot CORS</a></li>
</ul>

## 레퍼런스
https://www.popit.kr/cors-preflight-%EC%9D%B8%EC%A6%9D-%EC%B2%98%EB%A6%AC-%EA%B4%80%EB%A0%A8-%EC%82%BD%EC%A7%88/<br>
https://zamezzz.tistory.com/137
https://brunch.co.kr/@adrenalinee31/1
