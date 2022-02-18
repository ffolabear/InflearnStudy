## 웹어플리케이션설정, 서블릿
<br>
<br>
백엔드 웹 기술을 학습하기 어려운 이유는 3가지
<br><br>


- HTTP에 대한 기반 지식 부족
- 20년의 역사.. 왜 이렇게 되는지
- 스프링 MVC의 방대함

* * * 

1. 웹 서버 / 웹 어플리케이션 서버
<br>
모두 HTTP 기반으로 통신한다.<br>
<br>
웹서버 - HTTP 기반으로 동작, 정적 리소스 제공, 기타 부가 기능 (아파치, NGINX)<br>
웹 애플리케이션 서버 - HTTP 기반으로 동작, 웹 서버 기능 포함 + 프로그램 코드를 실행하여 어플리케이션 로직 수행<br>
(동적 HTMLL, HTTP API(JSON), 서블릿, JSP, 스프링 MVC)<br>
<br>
** was는 프로그래밍 코드를 만드는데 더 특화되어 있다.<br>
<br>
> Q. 그럼 왜 WAS만 쓰지 웹 서버를 왜 두지?<br>

그러나 WAS만 쓰면 너무 많은 역할을 담당하여 서버 과부하가 우려.<br>

정적 리소스는 파일에 있는 것을 내려주기만 하기에... 비싼 애플리케이션 로직이 정적 리소스 때문에 수행이 어려워지면 안된다.<br>

WAS는 장애가 생길 수 있기에 (개발자의 로직이 들어가므로) 장애시 오류 화면도 노출 불가능해짐.<br>
<img width="618" alt="스크린샷_2022-02-17_오후_3 45 53" src="https://user-images.githubusercontent.com/48312457/154682044-9b73fdf0-dd86-4b5c-b230-231737a650ab.png">

이렇게 구조화하면 역할을 분담 가능.<br>
<br>
효율적인 리소스 관리 (정적 리소스가 많이 사용되면 웹 서버 증설.. 애플리케이션 리소스가 많이 필요하면 WAS 증설..)<br>
<br><br>
* * *
<br><br>
2. 서블릿<br>
<br>
서블릿이 없다면 HTTP 요청 메시지를 받아 응답할 때 (서버 TCP/IP 대기, 소켓 연결, HTTP 요청 메시지 읽기, POST 방식, /save URL 확인, Content-Type 확인, HTTP 메시지 바디 내용 파싱, 저장 프로세스, **비즈니스 모델** , HTTP 응답 메시지 생성, TCPIP 응답 전달, 소켓 종료) 를 온전히 해야한다.<br>
<br>
서블릿은 비즈니스 모델 부분을 제외하고 모든 부분을 자동화한다.<br>
<br>
** 서블릿은 비즈니스 모델을 제외한 HTTP 요청과 HTTP 응답을 자동화해주는 도구이다.<br>
<br>
서블릿 컨테이너 :  WAS 안에 존재하여 서블릿들을 생성/호출/관리... 즉, 생명주기를 관리해준다.<br>

서블릿을 지원하는 WAS를 서블릿 컨테이너라고 한다.<br>

서블릿 객체를 ‘싱글톤'으로 관리한다. (객체를 1개만 사용하여 그 객체를 공유하여 사용함)<br>

(request, response는 항상 실행될 때마다 생성되어야 하지만... 서블릿은 그럴 필요가 없다.)<br>

그래서 공유 변수 사용을 주의해야한다.<br>

동시 요청을 위한 멀티 쓰레드 처리를 지원한다.<br>
<br><br>
* * *
<br><br>
3. 동시 요청 - 멀티쓰레드<br>
<br>
서블릿을 누가 호출할까 ? → 쓰레드<br>

프로세스 : 프로그램을 실행하는 것,  쓰레드 : 어플리케이션 코드를 하나 하나 내부에서 순차적으로 실행하는 것<br>

쓰레드는 한번에 하나의 코드 라인만 수행한다.<br>

동시 처리가 필요하다면 쓰레드를 추가로 생성해야 한다.<br>
<br>
1) 요청마다 쓰레드 생성 : <br>
<br>
장점<br>

- 동시 요청을 처리할 수 있다.<br>
- 리소스(CPU, 메모리)가 허용할 때 까지 처리 가능<br>
- 하나의 쓰레드가 지연 되어도 낭머지 쓰레드는 정상 동작한다.<br>
<br>
단점

- 쓰레드는 생성 비용이 비싸다 (응답 속도가 늦어진다.)<br>
- 쓰레드는 컨텍스트 스위칭 비용이 발생한다.<br>
- 쓰레드 생성에 제한이 없다.<br>

→ 고객 요청이 너무 많이 오면, CPU나 메모리가 임계점을 넘어서 서버가 죽을 수 있다.<br>
<br>
예를 들면 cpu 코어 1개고 쓰레드 2개면... 하나 하나 전환하면서 동작하게 되는 것이고 그 속도가 매우 빨라서 두 개를 동시에 하는 것처럼 보이는 것이다.<br>
<br>

2) 쓰레드 풀 이용: (WAS는 이렇게 이용한다.)<br>
<br>

<img width="664" alt="스크린샷_2022-02-17_오후_4 33 20" src="https://user-images.githubusercontent.com/48312457/154682154-0cde5807-bb5a-472b-bc9c-5f19c6cec1ef.png">



필요한 쓰레드를 쓰레드 풀에 보관하고 관리한다.<br>

풀 안에 미리 쓰레드를 만들어놓고 (예를 들면 200개), 놀고 있는 쓰레드에 할당을 해준다.<br>

사용이 다 끝나면 죽이고 다시 생성하는 것이 아닌, 쓰레드 풀에 다시 넣어놓는 것<br>

최대 쓰레드가 모두 사용중이라면 ? → 기다리는 요청을 거절하거나 특정 숫자만큼만 대기하도록 설정 가능<br>
<br>
장점
<br>
- 쓰레드가 미리 생성되어 있으므로 쓰레드 생성하고 종료하는 비용(CPU)가 절양되고 응답 시간이 빠르다.<br>
- 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리할 수 있다.<br>
<br>
쓰레드 풀 - 실무 팁:
<br>
- WAS의 주요 튜닝 포인트는 쓰레드 풀의 쓰레드 수이다.<br>
- 값을 너무 낮게 설정하면? - 동시 요청 많을 때 서버 리소스는 여유롭지만, 클라이언트는 금방 응답 지연<br>

(cpu 사용률이 50 안되면 개발자의 수치)
<br>
- 이 값을 너무 높게 설정하면? - 동시 요청이 많으면 CPU, 메모리 리소스 임계점 초과로 서버 다운<br>

서버 죽으면 다시 복구하기 쉽지 않다.<br>
<br>

- 장애가 발생한다면?

클라운드라면? 일단 서버를 늘리고, 나중에 튜닝. 클라우드가 아니라면? 매번 잘 튜닝<br>
<br><br>
쓰레드 풀의 적정 숫자는?<br>
<br>
애플리케이션 로직의 복잡도, CPU, 메모리, IO 리소스 상황에 따라 모두 다름...<br>
<br>
** 성능 테스트를 해야한다 ! (아파치 ab, 제이미터, nGrinder(네이버 오픈 소스))<br>

서버가 트래픽을 얼마나 받는지 가늠하기. 생각보다 못받으면 병목현상을 찾아 튜닝<br>
<br>
결론 : 멀티 쓰레드에 대한 부분은 WAS가 처리.<br>

개발자가 멀티 쓰레드 관련 코드를 신경쓰지 않아도 됨<br>

개발자는 마치 싱글 쓰레드 프로그래밍을 하듯이 편리하게 소스 코드를 개발<br>

멀티 쓰레드 환경이므로 싱글톤 객체 (서블릿, 스프링 빈)는 주의해서 사용.<br>
<br><br>
* * *
<br><br>
4. HTML, HTTP API, CSR, SSR<br>

HTML : 정적 리소스 or HTML 페이지(동적)(뷰 템플릿.. jsp, 타임리프)<br>

HTTP API : HTML이 아니라 데이터를 전달 (주로 JSON 형식 사용)<br>

데이터만 주고 받는다. UI 화면이 필요하면, 클라이언트가 별도 처리<br>
<br>
** HTTP 주고 받을 때 백엔드 개발자가 고려해야 할 것은 정적 리소스, HTML페이지(동적 페이지), HTTP API(데이터)<br>
<br>
SSR : 서버 사이드 렌더링<br>

서버에서 최종 HTML을 생성하여 클라이언트에 전달<br>
<br>
CSR : 클라이언트 사이드 렌더링<br>

HTML 결과를 자바스크립트를 사용해 웹 브라우저에서 동적으로 생성하여 적용<br>

ex) 구글 지도, Gmail 등.. 줌 해도 다시 페이지 새로고침하지 않는 기술들<br>
<br>
백엔드 개발자의 입장에서 UI 기술은 얼만큼 알아야 할까?<br>
<br>
백엔드 - 서버 사이드 렌더링 기술<br>
<br>

- JSP, 타임리프
- 화면이 정적이고, 복잡하지 않을 때 사용
- 백엔드 개발자는 서버 사이드 렌더링 기술 학습 필수


<br>
웬 프론트엔드 - 클라이언트 사이드 렌더링 기술<br>
<br>

- React, Vue.js
- 복잡하고 동적인 UI 사용
- 웬 프론트엔드 개발자의 전문 분야


<br>
선택과 집중<br>
<br>


- 백엔드 개발자의 웹 프론트엔드 기술 학습은 옵션
- 백엔드 개발자는 서버, DB, 인프라 등등 수 많은 백엔드 기술을 공부해야 한다.
- 웹 프론트엔드도 깊이있게 잘 하려면 숙련에 오랜 시간이 필요하다.


<br><br>
* * *
<br><br>
자바 웹 기술 역사<br>
<br>


1. 서블릿 - HTML 생성이 어려움. (java code로 써야하고...) 
2. JSP - HTML 생성은 편리하지만, 비즈니스 로직까지 너무 많은 역할 담당 (한 .jsp가 몇천줄)
3. 서블릿, JSP 조합 MVC 패턴 사용 (많은 개발자들이 프레임워크를 만들어볼까)
4. MVC 프레임워크 춘추 전국 시대 - 수 많은 MVC 프로임워크가 나옴


- MVC 패턴 자동화, 복잡한 웹 기술을 편리하게 사용할 수 있는 다양한 기능 지원
- 스트럿츠, 웹워크, 스프링 MVC(과거 버전)


<br>


5. 현재 기술<br>


- 애노테이션 기반의 스프링 MVC 등장

—> @Controller<br>

—> MVC 프레임워크의 춘추 전국 시대 마무리<br>

- 스프링 부트 등장

—> 스프링 부트는 서버를 내장<br>

—> 과거에는 서버에 WAS를 직접 설치하고, 소스는 War 파일을 만들어서 설치한 WAS에 배포<br>

—> 스프링 부트는 빌드 결과(Jar)에 WAS 서버 포함 → 빌드 배포 단순화<br>
<br>
스프링 웹 기술 2가지 분화<br>
<br>

1. Web sevlet - Spring MVC
2. Web Reactive - Spring WebFlux (스프링 웹 플럭스)
- 비동기 넌 블러킹 처리, 최소 쓰레드로 최대 성능, 함수형 스타일 → 그러나 기술적 난이도 매우 높고 일반 MVC도 충분히 빠르다. 성능이 아주 중요할 때만 사용

* * *
<br>
자바 뷰 템플릿 역사<br>
<br>

1. JSP - 속도 느림, 기능 부족
2. 프리마커(Freemarker), 벨로시티(Velocity) - 속도 문제 해결
3. 타임리프 


- 내츄럴 템플릿 : HTML의 모양을 유지하면서 뷰 템플릿 적용 가능
- 스프링 MVC와 강력한 기능 통합
- 최선의 선택. 단, 성능은 프리마커와 벨로시티가 더 빠름.


<br>
** 스프링부트에서 JSP를 권장하지 않는다.<br>
<br><br>
* * *
<br><br>
HTTP 요청 받는 방법 : GET, POST, HTTP message body(JSON)(POST, PUT, PATCH)<br>
<br>
1)GET
<br>
파라미터 이름이 하나일 때는 request,getParameter(), 중복일 때는 request.getParameterValues()<br>
<br>
2)POST<br>
<br>
content-type이 존재한다.<br>
<br>
request,getParameter(), request.getParameterValues() 쿼리 파라미터 메소드 동일하게 사용가능.<br>
<br>
cf.content-type은 HTTP 메시지 바디의 **데이터 형식**을 지정한다. (ex. 폼으로 보내준다, JSON으로 보내준다)<br>
<br>
3) HTTP 요청 데이터 - API 방식<br>
<br><br>
3-1 단순 텍스트 : 

```
ServletInputStream inputStream = request.getInputStream(); // byte코드로 담아옴
String s = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);//문자로 바꿀 때는 인코딩 정보를 줘야함

System.out.println("s = " + s);
```

3-2 JSON 객체 :

 

```
@WebServlet(name = "requestBodyJsonServlet", urlPatterns = "/request-body-json")
public class RequestBodyJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        ServletInputStream inputStream = request.getInputStream();
        String s = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);

        System.out.println("message :" + s);

        HelloData helloData = objectMapper.readValue(s, HelloData.class); //objectMapper를 통해 JSON 객체를 파싱하여 자바 객체로 변환

        System.out.println("helloData.getUsername() = " + helloData.getUsername());
        System.out.println("helloData.getAge() = " + helloData.getAge());
    }
}

```

* * *

** HTTP 응답 메시지 생성<br>
<br>
HTTP 응답코드 지정, 헤더 생성, 바디 생성, Content-Type, 쿠키, Redirect<br>

content, 쿠키, redirect 편의 메서드들<br>

```
private void content(HttpServletResponse response) {
    //Content-Type: text/plain;charset=utf-8
    //Content-Length: 2
    //response.setHeader("Content-Type", "text/plain;charset=utf-8");
    response.setContentType("text/plain");
    response.setCharacterEncoding("utf-8");
    //response.setContentLength(2); //(생략시 자동 생성)
}

private void cookie(HttpServletResponse response) {
    //Set-Cookie: myCookie=good; Max-Age=600; //
    //response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");
    Cookie cookie = new Cookie("myCookie", "good");
    cookie.setMaxAge(600); //600초 동안 유효하다
    response.addCookie(cookie);
}

private void redirect(HttpServletResponse response) throws IOException {
    //Status Code 302
    //Location: /basic/hello-form.html
    //response.setStatus(HttpServletResponse.SC_FOUND); //302
    //response.setHeader("Location", "/basic/hello-form.html");
    response.sendRedirect("/basic/hello-form.html");
}
```

응답의 세 종류 : 단순 텍스트 응답, HTML 응답, HTTP API (JSON) 응답<br>

HTML 응답<br>

```

@WebServlet(name = "responseHtmlServlet", urlPatterns = "/response-html")
public class ResponseHtmlServlet extends HttpServlet {

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
       //Content-Type : text/html;charset=utf-8
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        PrintWriter writer = response.getWriter();
        writer.println("<html>");
        writer.println("<body>");
        writer.println("    <div>안녕</div>");
        writer.println("</body>");
        writer.println("</html>");

    }
}

```

JSON 응답

```
@WebServlet(name = "responseJsonServlet", urlPatterns = "/response-json")
public class ResponseJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");

        HelloData helloData = new HelloData();
        helloData.setUsername("kim");
        helloData.setAge(20);

        //json으로 바꾸기
        String s = objectMapper.writeValueAsString(helloData);
        response.getWriter().write(s);
    }
}
```

질문 <br><br>
 
HTTP와 HTML의 차이?

멀티 쓰레드, 멀티 프로세스 (fork 는 어디 속할까?)

캐시 무효화?
