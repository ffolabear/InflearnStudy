## 스프링 핵심 원리 - 기본편
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br

## 빈 생명주기 콜백

<br>

DB 커넥션 풀이나 Socket 등등 어플리케이션 시작 시점에 필요한 연결을 하고 어플리케이션 종료시점에 연결을 끊는것 처럼 초기화와 종료 작업이 필요한 경우가 있다.

> 스프링빈은 객체생성 후 의존관계 주입 하는 라이프 사이클을 가진다. 그렇기 때문에 객체를 생성만한 시점에서 사용하려면 간혹 `null` 값 또는 NPE 에러가 나올수도 
> 있다. 스프링은 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 초기화 시점을 알려주는 다양한 기능을 제공한다. 또한 스프링은 스프링 컨테이너가 
> 종료되기 직전에 소멸 콜백을 주기 때문에 안전하게 객체를 사용할 수 있다.


**스프링 빈의 라이프 사이클**
> 스프링컨테이너생성 → 스프링빈생성 → 의존관계주입 → 초기화콜백 → 사용 → 소멸전콜백 → 스프링 종료



<br>

### 1. `InitializingBean`, `DisposableBean` 

<br>

`InitializingBean`, `DisposableBean` 두 인터페이스를 상속 받아서 그 안의 메서드를 재정의해서 사용할 수 있다. 
두 메서드 `afterPropertiesSet()`, `destroy()` 는 각각 초기화화 종료를 지원한다. 


하지만 이 메서드들은 몇가지 단점이 있다.

1. 이 인터페이스는 스프링 전용 인터페이스다. 해당 코드가 스프링 전용 인터페이스에 의존한다.
2. 초기화, 소멸 메서드의 이름을 변경할 수 없다.
3. 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없다.

메서드 자체도 2003년도에 나온것이기 때문에 비추천하는 방법이다.

<br>

### 2. 빈 설정정보 변경

<br>

빈을 등록하는 시점에 초기화와 종료 메서드를 같이 넣어줄 수도 있다.

```java

    public class NetworkClient {

        public void init() { 
            System.out.println("NetworkClient.init");
        }
        public void close() {
            System.out.println("NetworkClient.close");
        }
    }

```

```java

    @Configuration
    static class LifeCycleConfig {
        @Bean(initMethod = "init", destroyMethod = "close")
        public NetworkClient networkClient() {
            NetworkClient networkClient = new NetworkClient();
            networkClient.setUrl("http://hello-spring.dev");
            return networkClient;
    } 
}

```

1번과는 다르게 내가 원하는 메서드를 자유롭게 작성, 지정할 수 있으며 스프링에 의존하지 않고 외부에서도 자유롭게 사용할 수 있다.

어노테이션에 작성했을떄만 해당하는 기능이있는데 `destroyMethod` 메서드는 기본값이 `"(inferred)"` 이다. 보통 대부분의 라이브러리는 `close`, 
`shutdown` 이라는 이름으로 종료메서드를 사용하므로 이 이름들을 기본값으로 호출한다. 추론 기능을 사용하기 싫으면 `""` 으로 남겨준다.

<br>

### 3. `@PostConstruct`, `@PreDestroy`

<br>

스프링이 권고하는 방법. 초기화에 실행하고 싶은 메서드에 `@PostConstruct` 를, 종료에 사용하고 싶은 메서드에 `@PreDestroy` 를 붙혀주면 된다.

이 어노테이션들은 스프링이 아니라 자바 인터페이스에서 온것이므로 스프링이 아닌 다른 컨테이너에서도 동작한다. 하지만 외부라이브러리에서 사용하지 못한다는 단점이 
있가 때문에 그럴경우 2번의 방법을 사용하자.

#### 한줄 요약
`@PostConstruct`, `@PreDestroy` 를 사용하고 외부 라이브러리에서 사용할때는 `@Bean(initMethod = "...", destroyMethod = "...")` 을 
사용하자


<br><br>