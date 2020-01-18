MySQL에 연결된 클라이언트의 수가 일정수치를 넘어설 경우 나타나는 문제이다.

<h3>MySQL에 연결할 수 있는 Connection 개수 확인</h3>

```mysql
show variables like '%max_connect%';
```

<h3>MySQL 현재 상태 확인</h3>

```mysql
show status like '%CONNECT%';
```

<h3>해결 방안</h3>
<ul>
  <li>
    MySQL 설정파일 변경 후 MySQL 재시작<br>
    [mysqld]<br>
    max_connections=214 // 설정파일에서는 최대 214까지만 설정가능
  </li>
  <li>
    MySQL 명령어 설정<br>
    set global max_connections=500
  </li>
</ul>
