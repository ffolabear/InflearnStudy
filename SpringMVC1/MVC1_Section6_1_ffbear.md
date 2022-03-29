# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>


## HTTP 요청 매핑

- `@RestController` 가 있으면 문자열을 그대로 Http 메세지 바디에 넣어줌 
  - view 를 찾는 것이 아님!!

<br>

### `@RequestMapping`

- url 을 매핑해서 메서드를 호출할 수 있게한다.
  

- url 끝에 / 를 붙인것과 안붙인것 구분없이 스프링이 처리해준다.
  

- 만약 `@RequestMapping` 에 Http 메서드 속성을 지정해주지 않으면 메서드에 무관하게 호출된다.
  - `@RequestMapping(value = "", method = RequestMethod.GET)` 은  
    `@GetMapping` 으로 줄일 수 있다.
   
<br>
 
### PathVariable 사용

<br>

```java
public class MappingController {

    @GetMapping("/mapping/{userId}")
    public String mappingPath(@PathVariable("userId") String data) {
      return "ok";
    }
}

```

- url 자체에 값이 들어가 있는것
- url 에 값을 넣고 꺼낼 수 있음
- **정말 많이 사용함**
- 만약 변수이름이 매핑된 곳의 이름과 같다면 이름 지정해주는 부분 생략 가능

<br>

### 특정 파라미터 조건 매핑
- `@GetMapping(value = "/mapping-param", params = "mode=debug")`

- 특정 파라미터 정보를 설정할때 호출

<br>

### 특정 헤더 조건 매핑
- `@GetMapping(value = "/mapping-header", headers = "mode=debug")`
  
- 말 그대로 헤더에 특정 조건이 있어야 응답

<br>

### 미디어 타입 조건 매핑 - HTTP 요청 Content-Type, consume
- `@PostMapping(value = "/mapping-consume", consumes = MediaType.APPLICATION_JSON_VALUE)`

- 헤더의 컨텐트 타입이 맞아야 응답

<br>

### 미디어 타입 조건 매핑 - HTTP 요청 Accept, produce
- `@PostMapping(value = "/mapping-produce", produces = "text/html")`

- 생산해내는 컨텐트 타입이 Accept 와 맞아야 응답

<br>

### 스프링 MVC - HTTP 요청 매핑 - API 예시

- 회원 목록 조회: `GET`
- 회원 등록: `POST`
- 회원 조회: `GET`
- 회원수정: `PATCH `
  회원 삭제: `DELETE`
  
약간은 다를 수 있겠지만 RESTApi 를 따르는 요즘 크게 벗어나지 않는다.

<br>

## HTTP 요청 - 기본, 헤더 조회

- `HttpMethod` : HTTP 메서드를 조회한다.
  

- `Locale` : Locale 정보를 조회한다. 
  

- `@RequestHeader MultiValueMap<String, String> headerMap` : 모든 HTTP 헤더를 MultiValueMap 형식으로 조회한다.
  - 하나의 키에 여러 값을 받을 수 있다.
  - HTTP header, HTTP 쿼리 파라미터와 같이 하나의 키에 여러 값을 받을 때 사용한다.
  

- `@RequestHeader("host") String host` : 특정 HTTP 헤더를 조회한다.
  

- `@CookieValue(value = "myCookie", required = false) String cookie` : 특정 쿠키를 조회한다.


<br>

## HTTP 요청 파라미터 - 쿼리 파라미터, HTML Form

3가지.
- GET - 쿼리 파라미터 
  - `/url?username=hello&age=20`
  - 메시지 바디 없이, URL의 쿼리 파라미터에 데이터를 포함해서 전달
  - 검색, 필터, 페이징등에서 많이 사용하는 방식
  

- POST - HTML Form
  - `content-type: application/x-www-form-urlencoded`  
  - 메시지 바디에 쿼리 파리미터 형식으로 전달 `username=hello&age=20`
  - 회원 가입, 상품 주문, HTML Form 사용
  

- HTTP message body에 데이터를 직접 담아서 요청
  - HTTP API에서 주로 사용, JSON, XML, TEXT
  - 데이터 형식은 주로 JSON 사용
  - POST, PUT, PATCH
  
  
- 쿼리 파라미터 방식과 Form 방식은 `HttpServletRequest` 의 `request.getParameter()` 를 사용하면 다음 두가지 요청 파라미터를 조회할 수 있다.
- 둘다 요청 파라미터(request parameter) 조회라 한다. 



<br>

### `request.getParameter()`
- 서블릿과 동일한 방식.

<br>

### `@RequestParam`
- `@RequestParam("username") String memberName`
  

- `@Controller` 일때 String 을 리턴하면 리턴값에 해당하는 view 를 찾으므로 이를 방지하기 위해서
  `@ResponseBody` 를 붙이는 것도 좋은 생각
  - Http 응답 메세지에 넣어서 반환 - `@Restcontroller` 와 같은 효과
  

-  HTTP 파라미터 이름이 변수 이름과 같으면 이름지정은 안해줘도 됨


- String , int , Integer 등의 단순 타입이면 `@RequestParam` 도 생략 가능

- `required = true` 를 설정해서 필수여부를 정할 수 있음
  - 만약 `defaultValue` 를 준다면 필요없음  
    `@RequestParam(required = true, defaultValue = "guest") String username,`


- 파라미터를 Map, MultiValueMap 으로 조회할 수 있다.


<br>

### `@ModelAttribute`

실제 개발을 하면 요청 파라미터를 받아서 필요한 객체를 만들고 그 객체에 값을 넣어주어야 하는데 이 과정을
`@ModelAttribute` 가 대신해준다.

- `@Getter` , `@Setter` 나 혹은 `@Data` 로 바인딩할 객체를 만들고 그 클래스를 파라미터로 넘겨주면 된다.
  

- 스프링MVC 는 `@ModelAttribute` 가 있으면
  1. HelloData 객체를 생성한다.
  2. 요청 파라미터의 이름으로 HelloData 객체의 프로퍼티를 찾아서 해당 프로퍼티의 setter를 호출해서 파라미터의 값을 입력(바인딩) 한다.
  3. 만약 파라미터 타입이 맞지 않으면 예외 발생
  

- 스프링은 단순타입은 `@RequestParam` 으로 
- 나머지는 `@ModelAttribute` (argument resolver 로 지정해둔 타입 외) 로 받기 때문에 생략 가능

<br>

## HTTP 요청 메시지 - 단순 텍스트

요청 파라미터와 다르게  HTTP 메시지 바디를 통해 데이터가 직접 데이터가 넘어오는 경우는 `@RequestParam` , `@ModelAttribute` 를 사용할 수 없다.

<br>

#### V1 ~ V2
- 메세지 바디의 데이터를  `ServletInputStream` 로 읽고
  

- `StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8);` 로 꺼낼 수 있다.


- 서블릿을 통해서 받아도 되지만 나머지 코드는 필요없으므로 파라미터로 `InputStream` 과 `OutputStream` 만 넘겨줘도 된다.

<br>

#### V3
- `HttpEntity` 를 넘겨주면 스프링이 문자를 인식하고 알아서 바꿔주는 `HttpMessageConverter` 가 동작


- 리턴도 새로운 `HttpEntity` 를 만들어서 문자를 넘겨주 바디 메세지로 넘길 수 있다. 


- view 조회는 아님‼️


- 혹은 `HttpEntity` 를 상속받은 `RequestEntity`, `ResponseEntity` 도 사용할 수 있음


- 요청 파라미터는 조회하는 것 GET + 쿼리 파라미터 혹은 POST html 폼 방식이고 
이때만 `@RequestParam`, `@ModelAttribute` 를 사용한다‼️

<br>

#### V4 - `@RequestBody` 만이 사용함‼️
- `HttpEntity` 가 번거롭기 때문에 이것을 대체하는 `@RequestBody` 어노테이션을 사용한다.
  

- 마찬가지로 응답 결과를 HTTP 메시지 바디에 직접 담아서 전달할 수 있다. 물론 이 경우에도 view를 사용하지 않는다.


- 응답은 `@ResponseBody` 로 하는데, 리턴값을 Http 응답 코드에 넣어서 반환

> 요청 파라미터를 조회하는 기능: `@RequestParam` , `@ModelAttribute`
> HTTP 메시지 바디를 직접 조회하는 기능: `@RequestBody`

<br>

## HTTP 요청 메시지 - JSON




<br><br>