## 스프링 핵심 원리 - 기본편
_강의를 듣고 강의용을 기반으로 작성하였습니다._

<br>

`@Configuration` 과 `@Bean` 으로 설정정보에 있는 빈들을 등록할 수 있지만 항상 반복적인 코드를 작성해야한다는 불편함이 있다.   
그래서 등장한 것이 `@ComponentScan` 과 `@Autowired` 이다. 

`@ComponentScan` 은 `@Component` 어노테이션이 붙은 클래스를 찾아서 자동으로 스프링빈으로 등록해준다. 강의에서는 기존의 `AppConfig` 와 비교해보기 위해
기존의 `@Configuration` 으로 설정정보를 주었던 클래스들을 제외하기 위한 옵션을 추가로 주었다.

```
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
```

<br><br>

`@Configuration` 에도 `@Component` 가 포함되어 있기 때문에 스캔대상이다. 이 외에도 `@Controller`,`@Service`, `@Repository`, `@Configuration` 도 스캔 대상이다.  

<br>

`@ComponentScan` 은 별다른 설정이 없으면 설정정보가 있는 패키지부터 모든 자바코드를 탐색하기 때문에 탐색 시작점을 설정하는 것이 좋다.
```
    basePackages = {"hello.core", "hello.service"}
```
최근에는 기본 설정값으로 설정정보가 있는 패키지부터 모든 자바코드를 탐색하는 점을 이용해서 시작점을 설정하지 않고 **프로젝트 메인 설정 정보를 프로젝트 최상단에 
두는 것을** 관례로 하고있다. 이렇게 했을때 이렇게 하면 하위 패키지들은 모두 스캔 대상으로 할 수 있다.


<br><br>


`@ComponentScan` 을 붙이면서 기존의 `AppConfig` 안에 작성했던 메서드들이 필요없어졌고 대신에 각 클래스에 `@Component` 어노테이션을 붙여줬다.

- `FixDiscountPolicy`
- `RateDiscountPolicy`
- `MemberServiceImpl`
- `MemoryMemberRepository`
- `OrderServiceImpl`

이떄 등록되는 빈들의 이름은 메서드의 첫번째 글자를 소문자로 바꾼 이름으로 등록된다. 만약 직접 지정해주고 싶다면 `@Component` 옆에 괄호에 작성해서 넣어주면된다.


<br>

어노테이션을 붙이면서 삭제했던 코드에 있던 의존관계를 주입해주는 부분은 자동으로 빈을 등록하기 때문에 수동으로 넣어주는 것이 불가능하므로 
자동의존관계 주입으로 대체한다.    
 

자동의존관계 주입은 의존관계 주입이 필요한 클래스의 생성자에 `@Autowired` 를 붙이면 생성자에 필요한 객체를 **타입으로 조회해서** 찾아서 넣어준다. 

```java
@Component
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, @MainDiscountPolicy DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}

```

<br>

```java

@Component
public class MemberServiceImpl implements MemberService {


    private final MemberRepository memberRepository;

    @Autowired
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}

```

`@ComponentScan` 으로 인한 빈등록과 `@Autowired` 로 주입받은 의존관계는 로그를 통해 확인할 수 있다 .

```
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/MemberServiceImpl.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/MemoryMemberRepository.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/discount/FixDiscountPolicy.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/RateDiscountPolicy.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/MemberServiceImpl.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/MemoryMemberRepository.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/OrderServiceImpl.class]
~.DefaultListableBeanFactory - Autowiring by type from bean name 'memberServiceImpl' via constructor to 
bean named 'memoryMemberRepository'
```

<br>

만약 빈의 자동등록시 같은 이름의 빈이 중복등록된다면 `ConflictingBeanDefinitionException` 에러를 발생시키지만, 자동등록과 수동등록시 같은 이름의 빈이 
등록된다면 **수동빈이 자동빈을 Override** 한다. 
```
Overriding bean definition for bean 'memoryMemberRepository' with a different definition: replacing
```

<br>

하지만 최근 스프링부트에서는 아예 오류를 발생시켜버린다. 

```
Consider renaming one of the beans or enabling overriding by setting spring.main.allow-bean-definition-overriding=true
```

만약 해당옵션을 해제하고 싶으면 `application.properties` 에서 `spring.main.allow-bean-definition-overriding=true` 를 넣어주면 된다. 

<br><br>