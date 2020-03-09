커뮤니티 사이트를 운영하고 싶었던 작은 소망이 있었기에 <a href="https://github.com/KimJongHyeok2/aps">APS</a> 라는 커뮤니티 사이트를 개발하여 잠시나마 운영해보았다. 역시.... 아무도 이용해주지 않았지만 새로운 사실을 알게되었다.<br>
MySQL은 커넥션을 일정 시간 미 사용 시 닫아버린다는 점이다.
<code>show variable</code> 명령어를 실행했을 때 wait_timeout 값에 해당하는 시간이 지나면 커넥션이 닫힌다고 한다.
<pre>
 com.mysql.jdbc.exceptions.jdbc4.MySQLNonTransientConnectionException: No operations allowed after connection closed.
</pre>
MySQL을 재 시작하면 해결되긴 하지만 매우 번거롭기 때문에 다른 해결방법을 찾아본 결과, DBCP의 validationQuery 옵션을 사용하여 해결하였다.
```xml
<beans:bean name="dataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close">
  <beans:property name="testWhileIdle" value="true"/>
  <beans:property name="validationQuery" value="SELECT 1"/>
  <beans:property name="minEvictableIdleTimeMillis" value="120000"/>
</beans:bean>
```
<ul>
  <li>testWhileIdle : 유효 커넥션을 제거할 때 해당 커넥션의 유효성 여부 테스트, 기본 값은 false이며 true로 설정할 경우 반드시 validationQuery 설정이 되어야 한다.</li>
  <li>
    validationQuery : 커넥션 유효성 검사 시 사용할 SQL문
    <ul>
      <li>데이터베이스 서버에 부하를 줄 수 있으니 가장 간단한 SQL문을 적용한다.</li>
    </ul>
  </li>
  <li>minEvictableIdleTimeMillis : evictor thread 작업 시 설정된 시간만큼 사용되지 않은 커넥션을 제거한다. testWhileIdle 옵션이 true일 경우 설정된 시간을 초과하지 않은 커넥션에 대해서 validationQuery를 수행하여 유효하지 않은 커넥션을 제거한다.</li>
</ul>

## 레퍼런스
http://blog.naver.com/PostView.nhn?blogId=moonv11&logNo=220407847746&redirect=Dlog&widgetTypeCall=true
