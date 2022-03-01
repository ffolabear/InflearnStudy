## 스프링 핵심 원리 - 기본편
_강의를 듣고 강의용을 기반으로 작성하였습니다._

<br>

`@Configuration` 과 `@Bean` 으로 설정정보에 있는 빈들을 등록할 수 있지만 항상 반복적인 코드를 작성해야한다는 불편함이 있다. 
그래서 등장한 것이 `@ComponentScan` 과 `@Autowired` 이다. 

`@ComponentScan` 은 `@Component` 어노테이션이 붙은 클래스를 찾아서 자동으로 스프링빈으로 등록해준다. 강의에서는 기존의 `AppConfig` 와 비교해보기 위해
기존의 `@Configuration` 으로 설정정보를 주었던 클래스들을 제외하기 위한 옵션을 추가로 주었다. (`@Configuration` 에도 `@Component` 이 붙어있기 
때문이다.)


<br>

```
    excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
```

<br>

`@ComponentScan` 을 붙이면서 기존의 `AppConfig` 안에 작성했던 메서드들이 필요없어졌고 대신에 각 클래스에 `@Component` 어노테이션을 붙여줬다.

- `FixDiscountPolicy`
- `RateDiscountPolicy`
- `MemberServiceImpl`
- `MemoryMemberRepository`
- `OrderServiceImpl`

이떄 등록되는 빈들의 이름은 메서드의 첫번째 글자를 소문자로 바꾼 이름으로 등록된다. 만약 직접 지정해주고 싶다면 `@Component` 옆에 괄호에 작성해서 넣어주면된다.


<br>

어노테이션을 붙이면서 삭제했던 코드에 있던 의존관계를 주입해주는 부분은? 자동의존관계 주입으로 대체한다. (자동으로 등록하기 때문에 수동으로 넣어주는 것이 
불가능하다.)   
자동의존관계 주입은 의존관계 주입이 필요한 클래스의 생성자에 `@Autowired` 를 붙이면 생성자에 필요한 타입에 맞는 객체를 찾아서 넣어준다. 

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

`@ComponentScan` 으로 인한 빈등록과 `@Autowired` 로 주입받은 의존과계는 로그를 통해 확인할 수 있다 .

```
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/MemberServiceImpl.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/MemoryMemberRepository.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/discount/FixDiscountPolicy.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/RateDiscountPolicy.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/MemberServiceImpl.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/MemoryMemberRepository.class]
~.ClassPathBeanDefinitionScanner - Identified candidate component class: file [~/OrderServiceImpl.class]
~.DefaultListableBeanFactory - Autowiring by type from bean name 'memberServiceImpl' via constructor to bean named 'memoryMemberRepository'
```