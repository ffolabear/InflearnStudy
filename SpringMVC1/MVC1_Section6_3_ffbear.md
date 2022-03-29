# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>


## 스프링 MVC - HTTP 응답

<br>

### 1. 정적 리소스
> 웹 브라우저에 정적인 HTML, css, js을 제공할 때는, 정적 리소스를 사용한다.

- 스프링 부트는 클래스패스의 다음 디렉토리에 있는 정적 리소스를 제공한다.
    - `/static`, `/public`, `/resources`, `/META-INF/resources`
    - 보통은 `/src/main/resources/static` 이 될것


- 정적 리소스는 외부에 공개되므로 자유롭게 접근 할 수 있다.


- `ModelAndView` 에 뷰 이름과 `attribute` 를 넣어주고 그것을 반환하면 정적 리소스가 나온다.
  - 뷰 리졸버가 실행되어서 뷰를 찾고, 렌더링 한다.


- `Model` 에 값을 넣고 String을 반환하면 view의 논리적인 이름이 된다.
  - 만약 @ResponseBody 가 붙어있으면 view 를 찾는 것이 아니라 메세지 바디에 문자열을 넣는다. 


- 권장하지 않지만 리턴값이 없고 RequestMapping url이 view의 물리적인 주소와 같다면 해당경로의 view를 보여준다. 


> `ResponseBody`, `HttpEntity` 를 사용하면, 뷰 템플릿을 사용하는 것이 아니라, HTTP 메시지 바디에 직접 응답 데이터를 출력할 수 있다.

<br>

### 2. 뷰 템플릿 사용
> 웹 브라우저에 동적인 HTML을 제공할 때는 뷰 템플릿을 사용한다.

- 이전에 사용했던 JSP 는 사장되고 대신 ThymeLeaf 를 사용한다. 
 
- HTML 파일에 다음을 추가해준다.
  - `<html xmlns:th="http://www.thymeleaf.org">`
  - build.gradle 에 dependencies 에 추가해준다.
    `implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'`

<br>

### 3. HTTP 메시지 사용
>HTTP API를 제공하는 경우에는 HTML이 아니라 데이터를 전달해야 하므로, HTTP 메시지 바디에 JSON 같은 형식으로 데이터를 실어 보낸다.

<br>











