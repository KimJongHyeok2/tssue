Apache2와 Tomcat 연동 환경에서 Apache에 SSL을 적용하였는데 WebSocket 통신 시 code 400의 오류가 발생하였다. 열심히 해결방법을 찾은 결과 mod_rewrite 모듈을 사용하는 방법이 있었다.

## mod_rewrite
URL을 동적으로 전환(redirecting) 및 재작성(rewriting) 할 수 있는 아파치 확장 모듈이다. 사용방법 및 용도가 다양하며 필요에 따라 사용할 경우 매우 유용하다.

<h3>1. 모듈 활성화</h3>
<pre>
a2enmod rewrite
</pre>

<h3>2. 000-default.conf</h3>
<pre>
RewriteEngine on
RewriteCond %{HTTP:UPGRADE} ^websocket$ [NC]
RewriteCond %{HTTP:CONNECTION} Upgrade [NC]
RewriteRule .* ws://localhost:8080%{REQUEST_URI} [P]
</pre>

<h3>3. Apache2 재시작</h3>
<pre>
service apache2 restart
</pre>

## 레퍼런스
https://stackoverflow.com/questions/22002744/how-to-set-up-an-apache-proxy-for-meteor-sockjs-and-websocket
https://soul0.tistory.com/139
