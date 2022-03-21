# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>



### JSP

- 기존의 서블릿에서 `Printerwriter` 객체로 HTML을 작성하는게 번거로워서 나온 템플릿이다.
    - 요새는 `ThymeLeaf` 가 주류
    
- `request`, `response`, `out` 는 기본적으로 사용할 수 있는 예약어이다.
  

- 서블릿과 비슷하지만 HTML 을 출력하는데 더 초점을 두고 있다.

- `<% %>` 이 부분에 자바 코드를 입력할 수 있다.
- `<%= %>` 이 부분에 자바 코드를 출력할 수 있다.  
  
    ex) 
    ```html
    <%@ page import="hello.servlet.domain.member.Member" %>
    <%@ page import="java.util.List" %>
    <%@ page import="hello.servlet.domain.member.MemberRepository" %>
    <%@ page contentType="text/html;charset=UTF-8" language="java" %>
    <%
        MemberRepository memberRepository = MemberRepository.getInstance();
        List<Member> members = memberRepository.findAll();
    %>
    <html>
    <head>
        <meta charset="UTF-8">
        <title>Title</title>
    </head>
    <body>
        <a href="/index.html"></a>
    </body>

    ```
  
<br>

JSP의 등장으로 서블릿에서 HTML 렌더링에 대한 부분은 좀 나아졌지만 그래도 비즈니스 로직과 클라이언트가 보는 뷰의 영역의 분리가 되어 있지 않으므로
유지보수에 나쁜영향을 미치는 한계가 있다. 그래서 한계를 극복하기 위한 MVC 패턴이 등장하게 되었다.

<br>
<hr>

### MVC 
역할의 분리와 유지보수를 더 좋게 하기위한 디자인 패턴


#### 컨트롤러
HTTP 요청을 받아서 파라미터를 검증하고, 비즈니스 로직을 실행한다. 그리고 뷰에 전달할 결과 데이터를 조회해서 모델에 담는다.


#### 모델
뷰에 출력할 데이터를 담아둔다. 뷰가 필요한 데이터를 모두 모델에 담아서 전달해주는 덕분에 뷰는 비즈니스 로직이나 데이터 접근을 몰라도 되고, 화면을 렌더링 하는 일에 집중할 수 있다.
서블릿의 `request` 객체를 모델로 사용한다. 


#### 뷰
모델에 담겨있는 데이터를 사용해서 화면을 그리는 일에 집중한다. 여기서는 HTML을 생성하는 부분을 말한다.

<br>

<br>
<br>
  