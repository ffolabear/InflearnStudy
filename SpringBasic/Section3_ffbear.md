## 스프링 핵심 원리 - 기본편
_강의를 듣고 강의용을 기반으로 작성하였습니다._

<br>

## 스프링 컨테이너와 스프링 빈
기존의 순수 자바 코드를 기반으로 만들었을떄는 `DI` 와 `IOC` 가 `AppConfig` 에서 이루어졌다. 이 순수 자바 코드를 스프링으로 바꾸는 것은 `AppConfig`
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

```java

class ApplicationContextInfoTest {
    
    //스프링 컨테이서 빈 사용
    AnnotationConfigApplicationContext ac = new
            
            AnnotationConfigApplicationContext(AppConfig.class);

    void findAllBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            Object bean = ac.getBean(beanDefinitionName);
        }
    }
    
}

```

빈들이 들어있는 배열에서 값을 하나씩 읽으며 가져온다.

<br>

### 애플리케이션 빈 출력하기
<br>
스프링 컨테이너에는 스프링이 내주적으로 자동으로 생성한 빈도 있지만 사용자 애플리케이션의 개발을 위해 등록한 빈이있다.

```java

public class ApplicationContextInfoTest {

    AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);

    @Test
    @DisplayName("애플리케이션 빈 출력하기")
    void findApplicationBean() {
        String[] beanDefinitionNames = ac.getBeanDefinitionNames();
        for (String beanDefinitionName : beanDefinitionNames) {
            BeanDefinition beanDefinition = ac.getBeanDefinition(beanDefinitionName);

            //스프링이 내부에서 등록한 빈이 아닌 내가 직접 등록한 빈들이라면
            //Role ROLE_APPLICATION : 직접 등록한 애플리케이션 빈
            //Role ROLE_INFRASTRUCTURE : 스프링이 내부에서 사용하는 빈
            if (beanDefinition.getRole() == BeanDefinition.ROLE_INFRASTRUCTURE) {
                Object bean = ac.getBean(beanDefinitionName);
                System.out.println("beanDefinitionName = " + beanDefinitionName + " object = " + bean);

            }
        }
    }

}



```

만약 스프링이 내부에서 사용하는 빈을 출력하려면 `BeanDefinition.ROLE_INFRASTRUCTURE` 으로 바꿔주면 된다.

<br>



<br>
<br>