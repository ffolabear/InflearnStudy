# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

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

#### V4 - `@RequestBody` 많이 사용함‼️
- `HttpEntity` 가 번거롭기 때문에 이것을 대체하는 `@RequestBody` 어노테이션을 사용한다.
  

- 마찬가지로 응답 결과를 HTTP 메시지 바디에 직접 담아서 전달할 수 있다. 물론 이 경우에도 view를 사용하지 않는다.


- 응답은 `@ResponseBody` 로 하는데, 리턴값을 Http 응답 코드에 넣어서 반환

> 요청 파라미터를 조회하는 기능: `@RequestParam` , `@ModelAttribute`
> HTTP 메시지 바디를 직접 조회하는 기능: `@RequestBody`

<br>

## HTTP 요청 메시지 - JSON


#### V1

- `ServletInputStream` 으로 받고 `StreamUtils.copyToString` 으로 변환해준다.
  

- 그리고 서블릿과 동일하게 구현 클래스 객체에 `objectMapper.readValue` 를 넣어준다.

<br>

#### V2

- `@ResponseBody` 와 `@RequestBody` 를 사용해서 문자바꾸는 기능을 그대로 사용한다.


<br>

#### V3
- `@RequestBody` 로 문자열이 아닌 구현 클래스를 직접 넘겨준다.


- 만약 생략하면 `@ModelAttribute` 가 되기 떄문에 HTTP 메세지 바디가 아닌 요청 파라미터를 처리하게되지만 
  실제 요청 파라미터가 있는것이 아니기 때문에 default 값이 들어감

<br>

#### V4
- `HttpEntity` 를 사용할 수도 있다. 대신 `@RequestBody` 는 필요 없음


- `HttpEntity` 로 받은 값을 구현 클래스 객체에 넣어주면 된다. 

<br>

#### V5
- 응답을 문자열이 아닌 JSON 으로 할 수도 있다. 


- 리턴 타입을 구현클래스로 해준다. 


<br><br>