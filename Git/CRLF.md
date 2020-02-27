터미널에 git 명령어를 입력하였는데 아래와 같은 에러가 발생하였다.
<pre>
warning: CRLF will be replaced by LF in some/file.file.
The file will have its original line endings in your working directory.
</pre>

이는 맥 또는 리눅스를 사용하는 개발자와 윈도우를 사용하는 개발자가 Git으로 협업할 때 발생하는 **Whitespace** 에러라고 한다. 유닉스 시스템에서는 한 줄의 끝이 **LF(Lien Feed)** 로 이루어지는 반면, 윈도우에서는 줄하나가 **CR(Carriage Return)**, **LF(Line Feed)** 즉 **CRLF**로 이루어지기 때문이다. 따라서 어느 한 쪽을 선택할지 Git에게 혼란이 온 것이라고 한다.<br><br>
유닉스 OS를 사용하고 있다면 ``CRLF will be replaced by LF in`` 에러 메시지가, 윈도우를 사용하고 있다면 ``LF will be replaced by CRLF in`` 에러 메시지가 나타난다고 한다.

<h3>해결방법</h3>

``core.autocrlf`` 를 켜는 것이다. 이 기능은 개발자가 git에 코드를 추가했을 때에는 CRLF를 LF로 변환해주고, git의 코드를 개발자가 조회할 때에는 LF를 CRLF로 변환해준다.

<ul>
  <li>
    윈도우 사용자의 경우에 이러한 변환이 항상 실행되도록 하고싶을 때 명
<pre>
git config --global core.autocrlf true
</pre>
  </li>
  <li>
    리눅스나 맥을 사용하고 있는 경우, 조회할 때 LF를 CRLF를 변환하는 것은 원하지 않을 것이다. 따라서 input 이라는 명령어를 추가해줌으로써 단방향으로만 변환이 이루어지도록 설정한다.
<pre>
git config --global core.autocrlf true input
</pre>
  </li>
  <li>
    이러한 변환 기능을 원하지 않고, 에러 메시지를 끄고 알아서 작업하고 싶은 경우 아래 명령어로 core.safecrlf를 꺼준다.
<pre>
git config --global core.safecrlf false
</pre>
  </li>
</ul>

## 레퍼런스
https://blog.jaeyoon.io/2018/01/git-crlf.html
