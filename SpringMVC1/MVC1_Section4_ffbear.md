## 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>

### JSP

- 기존의 서블릿에서 `Printerwriter` 객체로 HTML을 작성하는게 번거로워서 나온 템플릿이다.
    - 요새는 `ThymeLeaf` 가 주류
    

- HTML 안에 자바 코드를 `<% %>` 로 넣을 수 있는게 특징이다  
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
<br>
  