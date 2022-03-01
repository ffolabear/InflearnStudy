## 스프링 핵심 원리 - 기본편
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>

## 스프링 컨테이너와 스프링 빈
기존의 순수 자바 코드를 기반으로 만들었을때는 `DI` 와 `IOC` 가 `AppConfig` 에서 이루어졌다. 이 순수 자바 코드를 스프링으로 바꾸는 것은 `AppConfig`
클래스에 `@Configuration` 어노테이션을 붙이는 것에서 시작한다. `@Configuration` 을 붙이므로써 스프링에게 이 자바파일이 설정정보라는 것을 알려줄수있다.

그리고 기존에 만들었던 메서드들에는 `@Bean` 을 붙혀준다.  스프링 컨테이너 생성시 `@Configuration` 클래스를 읽으면서 `@Bean` 붙인 메서드들을 
**메서드 이름** 을 Key 로, **리턴값을** Value 로 스프링 컨테이너에 등록한다. 이것을 스프링이 관리하는 자바 객체인 빈(Bean)이라고 한다.
> Bean 의 이름은 메서드이름을 사용하며 반드시 겹치지않는 이름으로 생성해야한다.

스프링 컨테이너의 생성 과정을 요약하면, 
1. 스프링 컨테이너 생성
2. 스프링 빈 등록
3. 스프링 빈 의존관계 설정 - 준비
4. 스프링 빈 의존관계 설정 - 완료

<br>

그리고 기존에 `AppConfig` 객체를 만들어서 사용했던 `OrderApp` 은 다음과 같이 바꿔준다.

```java
public class OrderApp {

    public static void main(String[] args) {
        
        ApplicationContext applicationContext = new AnnotationConfigApplicationContext(AppConfig.class);
        MemberService memberService = applicationContext.getBean("memberService", MemberService.class);
        OrderService orderService = applicationContext.getBean("orderService", OrderService.class);

        /*
               ...
        */
    }
}

```

`ApplicationContext` 를 스프링 컨테이너라고 하며 `@Bean` 을 붙혀준 메서드들은 스프링 컨테이너를 통해서 불러와야 한다.
> 더 정확히는 `BeanFactory` , `ApplicationContext` 로 구분해서 이야기한다.

<br>
<br>
<hr>

## 스프링 빈의 조회

<br>

### 모든 빈 출력하기

<br>

스프링 컨테이너를 통해서 빈들을 가져올 수 있다. 
```
AnnotationConfigApplicationContext.getBeanDefinitionNames();
```

`~Names` 로 가져올 경우 빈들의 정보를 담은 배열을 리턴하므로 배열을 다루는 방법으로 읽을 수 있다. 스프링 컨테이너에서 가져온 빈의 타입을 `Object` 로 지정한 것은 타입을 알 수 없기 때문이다.

<br>

### 애플리케이션 빈 출력하기
<br>
스프링 컨테이너에는 스프링이 내주적으로 자동으로 생성한 빈도 있지만 사용자 애플리케이션의 개발을 위해 등록한 빈은 구분되어있는데, 빈마다 부여된 역할로 구분된다.  

- `ROLE_APPLICATION` : 직접 등록한 애플리케이션 빈
- `ROLE_INFRASTRUCTURE` : 스프링이 내부에서 사용하는 빈

`getBeanDefinitionNames()` 메서드를 통해서 배열의 요소들의 역할은 `BeanDefinition.getRole()` 으로 얻을 수 있으며 조건문을 통해서 원하는 역할을 
가진 빈들을 얻을 수 있다.

<br>

이렇게 여러개의 빈을 한꺼번에 조회하는 방법도 있지만 특정 조건에 해당하는 빈들만 조회하는 방법도 있다.

- 빈 이름으로 조회

   -  `AnnotationConfigApplicationContext.getBean("빈 이름", 타입)`
    
- 이름 없이 타입만으로 조회
    -  `AnnotationConfigApplicationContext.getBean(클래스.class)`

<br>

기본적으로 빈의 이름은 메서드의 이름이기때문에 메서드의 이름이 같아서 충돌이 생길 수도 있다. 이렇게 같은 타입의 메서드 메서드의 여러개일 경우 빈의 조회시 오류가 
발생하기 때문에 이때는 빈의 이름을 지정하고 해당 이름으로 조회하면 된다. 

<br>

스프링의 빈은 부모 타입을 조회시 자식 타입은 모두 따라온다는 특징이 있다. 그렇기 때문에 자식이 여러개인 부모타입을 조회할때도 이름으로 조회해야 한다. 만약 부모 
타입으로 모두 조회할때는 `Object` 타입으로 조회하면 된다. 


<br>
<br>