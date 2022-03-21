# 스프링 핵심 원리 - 기본편
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>

## 빈 스코프

<br>

스코프는 빈이 존재할수 있는 범위를 뜻한다. 

#### 스프링이 지원하는 스코프

- 싱글톤 : 지금까지 봤던 스프링 컨테이너가 시작될때 생성되고 종료될때까지 유지되는 스프링빈의 스코프 
- 프로토타입 : 스프링 컨테이너는 프로토타입 빈의 생성과 의존관계 주입까지만 관여하고 더는 관리하지 않는 매우 짧은 범위의 스코프
- 웹 관련 스코프
    - request: 웹 요청이 들어오고 나갈때 까지 유지되는 스코프
    - session: 웹 세션이 생성되고 종료될 때 까지 유지되는 스코프
    - application: 웹의 서블릿 컨텍스트와 같은 범위로 유지되는 스코프

<br>

#### 빈 스코프 지정
- 컴포넌트 스캔 자동 등록
  ```
  @Scope("prototype")
  @Component
  ```
    
  <br>    

- 수동 등록
  ```
  @Scope("prototype")
  @Bean
  ```

<br>

### 프로토타입 스코프

<br>

스프링 컨테이너가 모든 객체를 싱글톤 객체로 만들어 주는것은 매번 객체를 생성하지 않고 항상 같은 객체를 반환하기 위함이었다. 
하지만 프로토타입은 그와는 다르게 **프로토타입 스코프를 스프링 컨테이너에 조회하면 스프링 컨테이너는 항상 새로운 인스턴스를 생성해서 반환한다.**

클라이언트가 프로토타입 빈을 요청하면, 

1. 스프링 컨테이너는 이 시점에 프로토타입 빈을 생성하고, 필요한 의존관계를 주입한다.
2. 스프링 컨테이너는 생성한 프로토타입 빈을 클라이언트에 반환한다.
3. 이후에 스프링 컨테이너에 같은 요청이 오면 항상 새로운 프로토타입 빈을 생성해서 반환한다.

프로토 타입 빈에서 기억해야할 점은 스프링 컨테이너가 프로토타입 빈을 생성하고, 의존관계 주입, 초기화까지만 처리하고
그 이후에는 관리하지 않는다는 것이다. 그래서 `@PreDestroy` 같은 종료 메서드가 호출되지 않는다.

#### 테스트코드

<br>

```java

    public class PrototypeTest {

        @Test
        void prototypeBeanFind() {
            AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(PrototypeBean.class);

            System.out.println("find prototypeBean1");
            PrototypeBean prototypeBean1 = ac.getBean(PrototypeBean.class);
            System.out.println("find prototypeBean2");
            PrototypeBean prototypeBean2 = ac.getBean(PrototypeBean.class);

            System.out.println("prototypeBean1 = " + prototypeBean1);
            System.out.println("prototypeBean2 = " + prototypeBean2);
            //assertThat(prototypeBean1).isNotSameAs(prototypeBean2);
            assertThat(prototypeBean1).isSameAs(prototypeBean2);

            ac.close();
        }


        //@Scope("prototype")
        @Scope("singleton")
        static class PrototypeBean {

            @PostConstruct
            public void init() {
                System.out.println("PrototypeBean.init");
            }

            @PreDestroy
            static void destroy() {
                System.out.println("PrototypeBean.destroy");
            }
        }
    
    
    }
```

해당 코드는 싱글톤일때 코드이며 프로토타입을 테스트하고 싶으면 주석부분으로 바꿔주면 된다. 

#### 결과
**Prototype**
```
find prototypeBean1
PrototypeBean.init
find prototypeBean2
PrototypeBean.init
prototypeBean1 = hello.core.scope.PrototypeTest$PrototypeBean@265adfad
prototypeBean2 = hello.core.scope.PrototypeTest$PrototypeBean@a3d9978
[main] ~ AnnotationConfigApplicationContext - Closing

```

<br>

**Singleton**
```
PrototypeBean.init
find prototypeBean1
find prototypeBean2
prototypeBean1 = hello.core.scope.PrototypeTest$PrototypeBean@265adfad
prototypeBean2 = hello.core.scope.PrototypeTest$PrototypeBean@265adfad
[main] ~ AnnotationConfigApplicationContext - Closing
PrototypeBean.destroy
```

<br>

- 싱글톤 빈은 스프링 컨테이너 생성 시점에 초기화 메서드가 실행 되지만
- 프로토타입 스코프의 빈은 스프링 컨테이너에서 빈을 조회할 때 생성되고, 초기화 메서드도 실행된다.
- 각 빈의 주소가 다르므로 싱글톤은 같은 빈을, 프로토타입은 새로운 빈을 생성했음을 알 수 있다.
- 싱글톤 빈은 스프링 컨테이너가 관리하기 때문에 스프링 컨테이너가 종료될 때 빈의 종료 메서드가 실행되지만
- 프로토타입 빈은 생성, 의존관계 주입까지만 스프링컨테이너가 관여하기 때문에 생성 이후에 호출되어야하는 `@PreDestroy` 는 호출되지 않는다.

<br>


<br>

오직 하나만 존재하는 싱글톤과 매번 새롭게 생성하는 프로토타입을 같이 사용할때 발생하는 문제점이 있다.  
만약 여러 클라이언트들이 싱글톤빈에 요청해서 각각의 프로토타입 빈을받고, 프로토타입빈 내부의 값을 변경한는경우, 
**각각 다른값을 가지는 것이 아니라 값을 공유하는것을 볼 수 있다.**

그 이유는 프로토타입빈은 스프링 컨테이너에 요청해서 프로토타입 빈이 새로 생성이 된 것이지, 사용 할 때마다 새로 생성되는 것이 아니다. 
즉, 처음 싱글톤 빈을 생성할때 프로토타입빈도 같이 요청했으므로 이미 주입이 끝난 생태이기 때문에 클라이언트들이 같은 프로토타입 객체를 사용하는 것이다.



이럴경우 매번 새로운 빈을 생성해서 반환하는 프로토타입을 사용하는 의미가 없으므로 추가적인 작업이 필요하다.


### 1. `ObjectFactory`, `ObjectProvider`

<br>

스프링 내부에서 지원해주는 DL(Dependency Lookup) 을 사용하는 `ObjectFactory`, `ObjectProvider`를 사용할 수 있다.
> `ObjectFactory` 가 더 확장된것이 `ObjectProvider` 이다.

<br>

```java
public class SingletonWithPrototypeTest1 {

    @Scope("singleton")
    static class ClientBean {

        @Autowired
        private ObjectProvider<PrototypeBean> prototypeBeanProvider;
  
        public int logic() {
            PrototypeBean prototypeBean = prototypeBeanProvider.getObject();
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
  
            return count;
        }
    }
}
```

- getObject 를 호출하면 그 스프링컨테이너에서 prototypeBean 을 찾아서 리턴한다.
- 스프링컨테이너에서 DL로 원하는 빈을 찾아주는 과정을 간단히 해주는 역할이다.
- 스프링에 의존한다.

<br>

### 2. JSR-330 Provider

<br>

자바 표준으로 제공하는 라이브러리를 사용하는 방법이다.   
`build.gradle` 에 `implementation 'javax.inject:javax.inject:1'` 를 추가해줘야한다.

<br>

```java
public class SingletonWithPrototypeTest1 {

    @Scope("singleton")
    static class ClientBean {

        @Autowired
        private Provider<PrototypeBean> provider;
  
        public int logic() {
            PrototypeBean prototypeBean = prototypeBeanProvider.get();
            prototypeBean.addCount();
            int count = prototypeBean.getCount();
  
            return count;
        }
    }
}
```

<br>

- 일반적으로 싱글톤 빈으로 대부분의 문제를 해결할 수 있기 때문에 프로토타입 빈을 직접적으로 사용하는 일은 매우 드물다.
- `ObjectProvider` , `JSR330 Provider` 등은 프로토타입 뿐만 아니라 DL이 필요한 경우는 언제든지 사용할 수 있다.
- 만약 스프링이 아닌 다른 컨테이너에서도 사용할 수 있어야 한다면 JSR-330 Provider 를 사용해야 한다.

<br>

<hr>

<br>

## 웹 스코프

<br>

웹 스코프는 웹에서만 동작하고 스프링이 해당 스코프의 종료시점까지 관리한다

### 종류

- request: HTTP 요청 하나가 들어오고 나갈 때 까지 유지되는 스코프, 각각의 HTTP 요청마다 별도의 빈 인스턴스가 생성되고, 관리된다.
- session: HTTP Session과 동일한 생명주기를 가지는 스코프
- application: 서블릿 컨텍스트( `ServletContext` )와 동일한 생명주기를 가지는 스코프
- websocket: 웹 소켓과 동일한 생명주기를 가지는 스코프


#### 예제

- `MyLogger`
  -  로거 출력용 클래스
  - `@Scope(value = "request")` 를 사용해서 요청 당 하나씩 생성되고, HTTP 요청이 끝나는 시점에 소멸된다.
  - 이 빈이 생성되는 시점에는 URL을 알 수 없으므로, 외부에서 `setter`로 입력 받는다.
  - 빈 생성시 `UUID` 를 생성하고 빈의 주소를, 
  - 종료시 생성한 `UUID` 빈 주소를 출력한다. 
    

- `LogDemoController`
  - 로거가 잘 작동하는지 확인하는 테스트용 컨트롤러다.
  - 현재 예제에서는 view 화면 없이 문자만 반환하므로 `@RequestBody` 로 설정한다.
  - HttpServletRequest를 통해서 요청 URL을 받고 그 값을 `myLogger` 의 `setter`로 저장한다.


- `LogDemoService`
  -  서비스 계층에서 로그를 출력하는 클래스

<br>

위의 예제만으로 기대하는 결과값은 클라이언트들의 요청이 들어올때마다 `Logger` 빈이 생성되어서 정상적으로 로그가 찍히는 것이지만, 어플리케이션의 실행할때는 
클라이언트의 요청이 없으므로 Request 스코프 빈을 생성할 수 없다.

<br>

두가지 해결책을 사용한다.

<br>

#### `ObjectProvider`
- 그냥 Logger 를 가져오지말고 DL을 통해서 찾아서 가져온다.
  - `private final ObjectProvider<MyLogger> myLoggerProvider`


<br>

#### 프록시 방식
- MyLogger 클래스의 어노테이션을 바꿔준다.
  - ```
    @Component
    @Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
    ```
    

- MyLogger의 가짜 프록시 클래스를 만들어두고 HTTP request와 상관 없이 가짜 프록시 클래스를 다른 빈에 미리 주입해 둘 수 있다.  
- 로그를 찍어보면 내가 만든 클래스가 아닌 `CGLIB` 라이브러리로 만든 가짜 객체를 만들어서 주입한것을 볼 수 있다.
- 마치 싱글톤을 사용하는 것 같지만 다르게 동작하기 때문에 결국 주의해서 사용해야 한다.
- 이런 특별한 scope는 꼭 필요한 곳에만 최소화해서 사용하자, 무분별하게 사용하면 유지보수하기 어려워진다.


<br><br>
