<a href="https://github.com/KimJongHyeok2/collect">개념글 저장소</a>라는 사이트를 운영하면서 테이블의 Row가 점점 증가할수록 극심한 응답 지연이 발생하는 현상을 겪었다. 원인을 찾아보니 인덱스를 지정하지 않았던 것이 문제였고 인덱스 관련 지식이 많이 부족하다고 생각되어 이번 일을 계기로 인덱스의 기초부터 공부하게되었다.

## 인덱스 기초
데이터베이스 서버의 입장에서 인덱스는 쿼리(Query)의 결과를 신속하게 전달해 줄 수 있는 중요한 기능이다. 인덱스가 없다면 데이터베이스는 모든 행을 검사해야한다. <br><br>
MySQL은 '인덱싱 데이터'를 별도의 유지 관리하고 있으며, 데이터가 변경될 때마다 이 데이터를 갱신해야한다. 때문에 테이블의 모든 컬럼에 인덱스를 쓰면 쓰기 속도가 저하되며, 물리적 저장소(Physical Storgage)와 메모리 사용량이 급증하게 된다. 즉, CRUD의 4개 요소에서 'R'이외의 다른 요소들은 극단적으로 성능이 저하되며 사용량 또한 증가한다.

<h3>부분 인덱스(Part Index)</h3>
성능을 향상 시키고 싶지만, 용량 소비율을 최소화 할 때 주로 사용되는 방법으로, Field 전체보다 일부의 데이터만 인덱싱 하는 방법이다. 적잖은 사례에서 VARCHAR(255)를 사용하는 경우를 어렵지 않게 찾을 수 있는데, 실제 이 모든 크기를 사용하는 경우는 확실히 드물기 때문에 보편적으로 접두사 부분 문자열에서 높은 엔트로피를 갖는다는 가정으로 특정 크기만큼의 인덱스를 지정하는 방법이다.<br><br>

```mysql
ALTER TABLE study ADD INDEX (data_no (5));
```

위와 같이 크기(5)를 지정하는 경우 이 크기만큼만 색인하라는 의미이며, 이경우 데이터 사용량을 크게 줄일 수 있다.

<h3>멀티 컬럼 인덱스(Multi Column Index)</h3>
복합 인덱스 또는 합성 인덱스라 부르기도 한다. 하나의 데이터 저장소에 2개 이상의 Field 색인을 저장한다. MySQL은 단일 쿼리를 실행할 때 하나의 테이블 당 하나의 인덱스만 사용할 수 있다. 즉, data_no와 category를 별도의 인덱스로 선언하는 경우 MySQL은 둘 중 인덱싱된 데이터 내 행이 적은 것을 먼저 참고하게 된다.<br><br>

따라서, 멀티 컬럼 인덱스를 사용하면 쿼리 속도를 향상시킬 수 있다. 제한되어 있는 인덱스 키 크기를 벗어 날 수 없기 때문에 빈번한 조합이 일어나는 Query 내 Field를 지정하는 방법을 권장한다.

```mysql
ALTER TABLE study ADD INDEX idx_data_list (data_no, category);
```
<h3>고유 인덱스(Unique Index)</h3>
레코드를 삽입하거나 갱신할 때 모든 값을 확인해 동일 값이 이미 존재하는지 않는가를 확인한다. 유일한 값이므로 Primary Key와 함께 사용되는 경우가 잦고, 조건절에서 명확히 언급하는 경우 가장 빠른 성능을 보인다. 엄연히 말해 고유 인덱스는 인덱스의 종류라기 보다는 '제약 조건' 중 하나라고 생각하는 것이 옳다.<br><br>

```mysql
ALTER TABLE study ADD UNIQUE(data_no);
```
<h3>적절한 인덱스 사용 여부 확인</h3>
MySQL에는 EXPLAIN을 사용하면 SELECT 시 어떤 테이블에 인덱스를 추가해야 더 빠르게 레코드를 검색할 수 있는가 정보를 얻을 수 있다. EXPLAIN은 쿼리에 있는 테이블 하나당 한 행씩 출력되며, 결합된(JOIN/UNION 등) SQL에서는 두개의 행에 대한 결과를 얻을 수 있다.<br><br>
https://dev.mysql.com/doc/refman/5.7/en/explain.html<br><br>

```mysql
EXPLAIN SELECT data_no, category_name FROM study;
```

<table>
  <thead>
    <tr>
      <td>인덱스가 동작하는 경우</td><td>인덱스가 동작하지 않는 경우</td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1. Field 값을 상수와 비교할 경우<br>(WHERE user_id = 'none')</td><td>1. LIKE인 동시에 와일드카드로 시작하는 경우</td>
    </tr>
    <tr>
      <td>2. Field 값 전체를 JOIN하는 경우<br>(a.user_id = b.user_id)</td><td>2. MySQL이 DB 전체를 읽는 것이 빠르다고 판단하는 경우</td>
    </tr>
    <tr>
      <td>3. Field 값을 구하는 경우</td><td>3. 일반적인 ORDER BY</td>
    </tr>
    <tr>
      <td>4. LIKE이나 고정된 문자로 시작되는 경우</td><td>4. 조건절 및 ORDER BY Fid가 서로 다를 경우</td>
    </tr>
    <tr>
      <td>5. MIN/MAX</td><td rowspan=3></td>
    </tr>
    <tr>
      <td>6. 문자열의 접두사를 바탕으로 한 ORDER BY, GROUP BY</td>
    </tr>
    <tr>
      <td>7. 조건절의 모든 Field가 인덱스의 일부인 경우</td>
    </tr>
  </tbody>
</table>

## 인덱스 구조
MySQL의 인덱스는 <a href="https://dev.mysql.com/doc/refman/5.6/en/innodb-physical-structure.html">MySQL 5.7</a>, <a href="https://dev.mysql.com/doc/refman/5.7/en/mysql-indexes.html">Reference Manual</a>, <a href="https://dev.mysql.com/doc/refman/5.7/en/innodb-physical-structure.html">The Physical Structure of an InnoDB Index</a>에 기재돼어 있듯이 B-Tree 구조로 되어있다.

<h3>B-Tree</h3>
가장 일반적으로 사용되는 인덱스 알고리즘으로서, 모든 경우에 유연하게 대처할 수 있어 전체적인 성능 균형이 잡혀있다. 칼럼의 값을 변형하지 않고 인덱스 구조체 내에서는 항상 정렬된 상태로 유지하고 있다. 전문 검색과 같은 특수한 요건이 아닌 경우, 대부분 인덱스는 거의 B-Tree를 사용할 정도로 일반적인 용도에 적합한 알고리즘이다.

## 레퍼런스
https://engineering.linecorp.com/ko/blog/mysql-workbench-visual-explain-index/#visualExplainIndex<br>
https://grip.news/archives/1428<br>
https://12bme.tistory.com/138
