데이터를 INSERT하니 등록일시가 -9시간되는 현상이 발생하여 3가지의 해결방법을 찾아보았다.<br><br>
우리나라의 표준시는 **UTC의 +9:00인 KST**이므로 서버의 시스템 시간 값이 UTC로 설정되어있다면 서버의 Timezone을 변경하거나 데이터베이스의 Timezone을 변경해야한다.

<h3>1. JDBC URL 옵션 추가</h3>
<pre>
jdbc:mysql://localhost:3306/dbName?allowPublicKeyRetrieval=true&useSSL=false&serverTimezone=Asia/Seoul
</pre>

아래와 같은 오류가 발생하는 경우에도 추가한다.

```java
Cause: org.springframework.jdbc.CannotGetJdbcConnectionException:
Failed to obtain JDBC Connection; nested exception is java.sql.SQLException:
Cannot create PoolableConnectionFactory (The server time zone value xxxxx is unrecognized or represents more than one time zone.
You must configure either the server or JDBC driver (via the serverTimezone configuration property)
to use a more specifc time zone value if you want to utilize time zone support.)
```

<h3>2. default-time-zone 설정</h3>

my.cnf에 추가 후 MySQL 재시작
<pre>
[mysqld]
default-time-zone='+9:00'
</pre>
변경된 Timezone 확인
```mysql
SELECT @@global.time_zone, @@session.time_zone;
```

<h3>3. 시스템 타임존 변경</h3>

변경 가능한 Timezone 확인<br>
<pre>
ls /usr/share/zoneinfo
</pre>
Timezone 변경
<pre>
sudo ln -sf /usr/share/zoneinfo/Asia/Seoul /etc/localtime
</pre>
이후 <code>date</code>를 입력하여 변경되었는지 확인한다.
