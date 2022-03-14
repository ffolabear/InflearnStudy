## 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>

## 서블릿


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


<br><br>