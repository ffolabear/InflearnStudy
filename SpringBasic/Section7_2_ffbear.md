## 스프링 핵심 원리 - 기본편
_강의를 듣고 강의용을 기반으로 작성하였습니다._


## 스프링 의존관계 자동 주입- 2

<br>

의존관계를  자동 주입- 조회 빈이 여러개라면 `NoUniqueBeanDefinitionException` 이 발생한다. 대처할 방법은 3개가 있다.


### 1. 필드 명을 빈 이름으로 변경


`@Autowired` 는 타입 매칭을 시도하고, 이때 여러 빈이 있으면 필드 이름, 파라미터 이름으로 빈 이름을 추가
매칭한다. 이점을 이용해서 필드 이름을 바꿔주면 정상 주입된다.

<br>

### 2. `@Qualifier` 사용


추가 구분자를 붙여주는 방법이다. 

```java

    @Component
    @Qualifier("mainDiscountPolicy")
    public class RateDiscountPolicy implements DiscountPolicy {}

```

<br>

```java
@Component
//@RequiredArgsConstructor //final 이 붙은 필드를 모아서 생성자를 자동으로 생성해줌
public class OrderServiceImpl implements OrderService {

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, 
                     @Qualifier("mainDiscountPolicy") DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

}
```

만약 `@Qualifier` 에 있는 이름은 찾지 못한다면 해당 이름의 빈을 추가로 찾는과정을 거친다.

<br>

`@Qualifier("mainDiscountPolicy")` 를 사용하면 컴파일시 타입 체크가 안되므로 컴파일오류로 발견할 수 없다, 이럴때는 어노테이션을 만들어서 문제를 
해결할 수 있다.

```java

    @Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.TYPE, 
             ElementType.ANNOTATION_TYPE})
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Qualifier("mainDiscountPolicy")
    public @interface MainDiscountPolicy { }

```

<br>

```java
    @Component
    @MainDiscountPolicy
    public class RateDiscountPolicy implements DiscountPolicy {}

```

<br>

```java
    @Component
    public class OrderServiceImpl implements OrderService {
    
    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, @MainDiscountPolicy DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }

}
```


<br>

### 3. `@Primary` 사용

`@Primary` 는 우선순위를 정하는 방법이다. @Autowired 시에 여러 빈이 매칭되면 @Primary 가 우선권을 가진다.

<br>

`@Primary` 는 기본값 처럼 동작하는 반면, `@Qualifier` 는  상세하게 동작한다. 이런 경우 자동보다는 수동이, 넒은 범위의 선택권 보다는 좁은 범위의 
선택권이 우선 순위가 높으므로 `@Qualifier` 가 우선권을 가지게 된다.