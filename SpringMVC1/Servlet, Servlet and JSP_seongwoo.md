</br>

## MVC study 2 ) Servlet 정리, Servlet/JSP 차이

</br>

** 일단 Servlet 관련 너무 잘 정리된 글이 있어 공유합니다. **

[https://hochoon-dev.tistory.com/entry/JAVA-Servlet%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%9D%B4%EB%9E%80]

</br></br>

Servlet : 클라이언트의 요청을 처리하고 그 결과를 다시 클라이언트에게 전송하는 Servlet 클래스의 구현 규칙을 지킨 기술

-> 자바를 사용하여 웹을 만들기 위해 필요한 기술

</br>

----------

</br></br>

Servlet 특징 :

- 클라이언트의 Request에 대해 동적으로 작동하는 웹 어플리케이션 컴포넌트

- HTML로 응답한다.

- JAVA의 쓰레드를 이용하여 동작

- HTML 변경 시 Servlet을 재컴파일해야 하는 단점

</br></br>

 * * *
 
 </br></br>

Servlet 동작 방식 :

1.  클라이언트가 URL을 치고 엔터치면 HTTP Request를 Servlet Container로 전송
2.  Servlet Contatiner는 HttpServletRequest, HttpServletResponse 두 객체 생성
3.  web.xml은 사용자가 요청한 URL을 분석하여 어느 서블릿에 대해 요청을 한 것인지 찾는다.
4.  해당 서블릿에서 service 메소드를 호출한 후 클라이언트의 POST, GET 여부에 따라 doGet() 또는 doPost()를 호출한다.
5.  doGet(), doPost()는 동적 페이지를 생성한 후 HttpServletResponse 객체에 응답을 보낸다.
6.  응답이 끝나면 HttpServletRequest, HttpServletResponse 두 객체를 소멸

</br></br>

Servlet 생명 주기 :

1.  클라이언트의 요청이 들어오면 컨테이너는 해당 서블릿이 메모리에 있는지 확인
2.  없을 경우 init() 메소드를 호출하여 메모리에 적재
3.  init()은 처음 한번만 실행된다. 서블렛의 쓰레드에서 공통적으로 사용해야 하는 것이 있다면 init()을 오버라이딩 하여 구현하면 된다.
4.  실행 중 서블렛이 변경될 경우, 기존 서블렛을 destroy()하고 init()을 통해 새로운 내용을 다시 메모리에 적재한다.
5.  init()이 호출된 후 클라이언트의 요청에 따라서 service() 메소드를 통해 요청에 대한 응답이 doGet()과 doPost()로 분기
6.  이 때 서블렛 컨테이너가 클라이언트의 요청이 오면 가장 먼저 처리하는 과정으로 생성된 HttpServletRequest, HttpServleResponse에 의해 request와 response 객체가 제공된다.
7.  컨테이너가 서블렛에 종료 요청을 하면 destroy() 메소드가 호출되는데 마찬가지로 한번만 실행되며, 종료시에 처리해야 하는 작업들은 destroy() 메소드를 오버라이딩하여 구현하면 된다.

</br></br>

----------

</br></br>

Servlet Container : 서블릿을 관리해주는 컨테이너

</br>

**Servlet Container 역할 :**

</br>

1. 웹서버와의 통신 지원

**개발자가 서블릿에 구현해야 할 비지니스 로직에 대해서만 초점을 두게끔 도와준다.**

일반적으로 소켓을 만들고 listen, accpet 등  _(서버 TCP/IP 대기, 소켓 연결, HTTP 요청 메시지 읽기, POST 방식, /save URL 확인, Content-Type 확인, HTTP 메시지 바디 내용 파싱, 저장 프로세스, **비즈니스 모델** , HTTP 응답 메시지 생성, TCP IP 응답 전달, 소켓 종료)_ 을 해야하지만 서블릿 컨테이너는 이러한 기능을 API로 제공하여 복잡한 과정을 생략(비즈니스 모델 구현에만 집중하게끔)하도록 한다.

</br>

2. 서블릿 생명주기 관리

서블릿 컨테이너는 서블릿 클래스를 로딩하여 인스턴스화하고, 초기화 메소드를 호출하고, 요청이 들어오면 적절한 서블릿 메서드를 호출한다. 또한 수명이 다 된 서블릿들을 대상으로 가비지 컬렉션(JAVA에서 할당된 메모리를 free() 방식)을 호출해 필요없는 자원 낭비를 막는다.

</br>

3. 멀티쓰레드 지원 및 관리 :

**서버가 다중 쓰레드를 생성 및 운영해주기 때문에 쓰레드의 안정성에 대해서 걱정하지 않아도 된다.**  서블릿 컨테이너는 Request가 올 떄마다 새로운 자바 쓰레드를 하나 생성하는데 (쓰레드 풀 속의 하나에 매칭시켜줌), HTTP 메소드를 실행하고 나면 쓰레드는 자동으로 죽게 된다.

</br>

4. 선언적인 보안 관리

보안관리를 XML 배포 서술자에 기록함으로써 재컴파일 하지 않아도 보안 관리가 가능하다. (보안에 관련된 내용을 자바 클래스나 서블릿에 구현하지 않아도 된다.)

</br></br>

----------

</br></br>

Servlet과 JSP의 차이

**Servlet :**

Java 코드 안에 HTML 코드 (하나의 클래스)

data processing(doGet()이나 doPost(). MVC 중 Controller)에 좋다.

DB와의 통신, 비즈니스 로직 호출, 데이터 읽고 확인하는 작업 등에 유용하다.

.java를 컴파일 한 .class파일만 있어도 실행되기에 보안에 유리하다.

Servlet 코드가 수정되면 Java 코드를 컴파일 후 동적 페이지를 처리하기 때문에 전체 코드를 업데이트하고 다시 컴파일 한 후 배포해야한다. (개발 생상성 저하)

HTML을 모두 JAVA 문자열 ""내에 작성하여야 하므로 View 작성에 불리하다.

</br></br>

**JSP :**

HTML 코드 안에 자바 코드

View 작성에 유리하다.

JSP가 수정된 경우 재배포할 필요 없이 WAS가 알아서 처리한다. (쉬운 배포)

코드가 보여지기에 보안에 좋지 않다.

** 기존의 웹 어플리케이션 개발 패턴은 모델1과 모델2로 나뉘어지는데

모델 2에서는 Controller로 서블릿을, View로 JSP를, Model은 Java bean으로 구현했다.

그러나 현재 JSP는 성능적으로 다른 뷰 템플릿에 밀려 사용되지 않음.

</br></br>

* * *

</br></br>

참고 사이트 :

[https://hochoon-dev.tistory.com/entry/JAVA-Servlet%EC%84%9C%EB%B8%94%EB%A6%BF-%EC%9D%B4%EB%9E%80]


[https://swk3169.tistory.com/276]
