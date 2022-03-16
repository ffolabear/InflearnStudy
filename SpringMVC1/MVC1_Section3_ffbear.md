## 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>

## 서블릿

서블릿 코드를 만들때 해야하는 것
- `@WebServlet(name = "서블릿이름", urlPatterns = "/url 주소")`
- `HttpServlet` 을 상속받고 `service` 메서드 오버라이드
- 요청에 대한 여러가지 정보들은 `HttpServletRequest`의 메서드들로 조회가능하다.


<br>

### HTTP 요청 데이터

<br>

#### 1.GET - 쿼리 파라미터
- 검색, 필터, 페이징등에서 많이 사용하는 방식
- 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
- 쿼리 파라미터는 URL에 다음과 같이 `?`를시작으로보낼수있다.추가파라미터는 `&`로구분하면된다.
  - //localhost:8080/request-param `?` username=hello `&` age=20

- 전체 파라미터 조회하기
    - `HttpServlet` 클래스를 상속받고 `service` 클래스에서 조회
        ```
        request.getParameterNames().asIterator().forEachRemaining(
            paramName -> System.out.println(paramName +" = " + request.getParameter(paramName)));
        ```


- 단일 파라미터 조회하기
    - `request.getParameter(파라미터이름)`
    - String 변수에 결과를 담을 수 있음
    - get 방식으로 보낸데이터와 form 태그로 보낸 데이터 모두 조회가

- 이름이 같은 복수 파라미터 조회
    - `request.getParameterValues(파라미터이름)`
    - 배열에 결과를 담을수 있음

같이 파라미터 이름은 하나인데, 값이 중복이면 `request.getParameterValues()` 를 사용해야 한다. 만약 `request.getParameter()` 를 사용하면
`request.getParameterValues()` 의 첫번째 값을 반환한다. 

<br>

#### 2.POST - HTML Form
- 주로 회원 가입, 상품 주문 등에서 사용하는 방식이다.
- HTML의 Form을 사용해서 클라이언트에서 서버로 데이터를 전송한다. 
- content-type: `application/x-www-form-urlencoded`
- 메시지 바디에 쿼리 파리미터 형식으로 데이터를 전달한다.

<br>

#### 3. API 메시지 바디 - 단순 텍스트
- JSON 도 결국 텍스트지만, 파싱작업을 해줘야한다.
- 요청받을 JSON 의 형식을 알고 있다면 `ObjectMapper` 클래스의 `readValue` 메서드에 읽은 JSON 과 JSON 형식으로 만든 클래스 타입을 넣어줘서 
  편리하게 사용할 수 있다.
- JSON 파싱 클래스 
    ```java
    public class RequestBodyJasonServlet extends HttpServlet {
    private ObjectMapper objectMapper = new ObjectMapper();
    
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
         //데이터 읽기
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);
  
        //읽은 JSON을 클래스로
        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class);
    
    
    }
  }
  ```
  
  <br>
  
  JSON을 변환할 클래스 
  ```java
    @Getter @Setter
    public class HelloData {

    private String username;
    private int age;
  
  }
  ```
  
<br>

<hr>

### HTTP 응답

<br>

#### HttpServletResponse - 기본 사용법 - HTTP 응답 메시지 생성

- 응답코드 세팅 : `response.setStatus(HttpServletResponse.SC_XX);`
  

- response 헤더 
  - `HttpServletResponse`의 `setHeader` 메서드로 여러 가지를 설정 가능  
    ex) `response.setHeader("Content-Type", "text/plain;charset=utf-8");`


- content 편의 메서드
  - `HttpServletResponse`의 `setContentType`, `setCharacterEncoding` 로 설정 가능  
    ex) `response.setContentType("text/plain");`
    

- cookie 편의 메서드
  - `javax.servlet.http.Cookie` 를 활용해서 나만의 쿠키를 만들 수 있다.  
    ex)   
    ```java
    public class ResponseHeaderServlet extends HttpServlet {
        private void cookie(HttpServletResponse response) {
            Cookie cookie = new Cookie("myCookie", "good");
            cookie.setMaxAge(600); //600초
            response.addCookie(cookie);
        }
    }
    ```
    

- redirect 편의 메서드
  - `HttpServletResponse`의 `sendRedirect` 메서드로 설정 가능  
    ex) `response.sendRedirect("/basic/hello-form.html");`

<br>

### HTTP 응답 데이터

  <br>

#### 단순 텍스트 응답
- 앞에서 계속 사용했던 `PrintWriter` 의 `println` 메서드에 원하는 문자열을 넣으면 텍스트를 응답

<br>

#### HTML 응답
- `HttpServletResponse` 클래스의 `setContentType`, `setCharacterEncoding` 으로 컨텐트 타입을 잡아줘야한다.
- `PrintWriter` 의 `println` 메서드에 HTML 태그를 넣으면 그대로 렌더링한다.

<br>

#### HTTP API - MessageBody JSON  응답
- `setContentType` 을 application/json 으로 지정해줘야한다.
- JSON 형식으로 만든 클래스 객체를 만들어서 값을 넣어주고 문자열로 변환해서 `PrintWriter` 의 `println` 를 사용하면된다.
  ```java
  public class ResponseJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");

        HelloData helloData = new HelloData();
        helloData.setUsername("kim");
        helloData.setAge(20);

        String result = objectMapper.writeValueAsString(helloData);
        response.getWriter().write(result);

    }
  }
  ```

<br>



<br><br>