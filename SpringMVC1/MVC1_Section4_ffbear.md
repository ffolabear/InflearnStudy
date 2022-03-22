# 스프링 MVC 1편 - 백엔드 웹 개발 핵심 기술
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>

## 스프링 MVC - 구조 이해

스프링 MVC 의 주요 클래스들을 직접 구현해보면서 따라간다.

<br>

### 프론트 컨트롤러 패턴 - `DispatcherServlet`
여러 기능을 도입하면서 그 로직에 맞는 컨트롤러가 많아지는데 그럴수록 유지보수가 안좋아지고 공통 
처리가 어려워진다. 그래서 모든 요청을 받아서 알맞은 컨트롤로 요청을 넘기는 프론트 컨트롤러가 등장한다.


<br>

#### FrontControllerServletV1

- 모든 컨트롤러를 담은 `Map<String, ControllerV1> controllerMap` 을 생성하고 각 url 와 컨트롤러를 저장한다.
- `HttpServletRequest` 객체에서 `getRequestURI()`를 통해 URI 정보를 얻어서 해당하는 컨트롤러를 리턴한다. 


- Spring MVC 에서는  `DispatcherServlet` 으로 구현되어있디.

<br>

### 프론트 컨트롤러 패턴 - `View`
요청에 맞는 컨트롤러를 리턴하는데는 성공했지만 여전히 요청에 맞는 `view` 를 응답하는 부분은 
중복되는 코드가 많다.

```java
public class MemberFormControllerV1 implements ControllerV1 {

        @Override
        public void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
            String viewPath = "...물리 경로...";
            RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
            dispatcher.forward(request, response);
        }
}
```

<br>

요청에 맞는 `view` 를 생성하는 부분은 각 컨트롤러마다 중복되는 부분이므로 알맞는 `view` 를 생성해주는 클래스를 만든다.

#### MyView - `View`

- 프런트 컨트롤러에서 URI 를 넘겨주면 `render` 메서드에서 요청에 맞는 `view` 를 `RequestDispatcher` 가 포워드 해준다.

<br>

### Model
이제는 각 컨트롤레에서 하던 `HttpServletRequest`, `HttpServletResponse` 객체에서 정보를 얻는 일을 다른 클래스에서 해주기 때문에 
더이상 파라미터로 받지않아도된다. 그래서 그 부분을 제거해주고 서블릿이 하던일을 대신 해주는 `Model` 을 생성한다.

#### 뷰 이름 중복 제거
접두사 역할을 하는 복잡한 경로를 변수로 선언해서 마지막에 URI 를 리턴할때 붙여준다.

<br>

### ModelView - `ModelAndView`
- `Model` 은 서블릿의 종속성을 없애기 위한 클래스이므로 `HttpServletRequest`, `HttpServletResponse` 을 더이상 사용할 수 없다.


-  `String viewName` 와 `Map<String, Object> model` 객체를 인자로 받는데, 컨트롤러에서 `view` 를 렌더링할 데이터를 넣어준다.


- 프런트 컨트롤러에서는 로직에 필요한 데이터를 `Map`( `ParamMap` ) 에 담아 각 로직별 컨트롤러로 넘겨주고 로직별 컨트롤러는 받은 데이터로 로직을 실행하고 
알맞은 뷰의 정보를 담은 객체를( `ModelView` ) 다시 프런트 컨트롤러로 넘겨준다. 


- `ModelView` 객체에서 받은 데이터로 `viewResolver` 메서드로 알맞은 URI를 생성해서 `View` 의 `render` 메서드로 알맞은 화면을 보여준다.

<br>

### 어댑터 - `HandlerAdatpter`
만약 지금 사용하고 있는 컨트롤러가 아닌 다른 컨트롤러를 사용할때 사용하는 클래스이다. `support` 메서드로 현재 찾고 있는 컨트롤러가 특정 인터페이스의 
인스턴스인지 비교해보고 맞다면 요청에 맞는 컨트롤러로 요청을 넘긴다.


<br><br>

