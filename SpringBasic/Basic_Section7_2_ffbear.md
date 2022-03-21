# 스프링 핵심 원리 - 기본편
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>

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

`@Qualifier("mainDiscountPolicy")` 를 사용하면 컴파일시 타입 체크가 안되므로 컴파일오류로 발견할 수 없는데, 이것은 어노테이션을 만들어서 문제를 
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

<br><br>

`@Primary` 는 기본값 처럼 동작하는 반면, `@Qualifier` 는  상세하게 동작한다. 이런 경우 자동보다는 수동이, 넒은 범위의 선택권 보다는 좁은 범위의 
선택권이 우선 순위가 높으므로 `@Qualifier` 가 우선권을 가지게 된다.

<hr>

<br>

만약 어떤 로직에서 스프링빈을 특정 자료구조를 받아서 원하는 것을 찾아서 사용하는 경우가 있다면 이럴때는 빈들을 Map 이나 List 에 담을 수도 있다.

```java
    public class AllBeanTest {

        static class DiscountService {
    
            private final Map<String, DiscountPolicy> policyMap;
            private final List<DiscountPolicy> policies;
    
            @Autowired
            public DiscountService(Map<String, DiscountPolicy> policyMap, List<DiscountPolicy> policies) {
                this.policyMap = policyMap;
                this.policies = policies;
            }
    
            public int discount(Member member, int price, String discountCode) {
                DiscountPolicy discountPolicy = policyMap.get(discountCode);
    
                return discountPolicy.discount(member, price);
            }
        }
    }
```

<br>

`policyMap`, `policies` 를 출력하면 다음과 같이 출력된다.
```
policyMap = {fixDiscountPolicy=hello.core.discount.FixDiscountPolicy@1951b871, 
            rateDiscountPolicy=hello.core.discount.RateDiscountPolicy@5c18016b}
            
policies = [hello.core.discount.FixDiscountPolicy@1951b871, 
            hello.core.discount.RateDiscountPolicy@5c18016b]

```

`DiscountService` 에서 `policyMap`, `policies` 은 생성자 주입을 통해서  
`policyMap` 에는 `key` - `DiscountPolicy` 으로 조회한 빈의 이름과, `value` - `DiscountPolicy` 의 빈을 받고  
`DiscountPolicy` 에는 `DiscountPolicy` 으로 조회한 빈을 담아준다.    

이 모든것이 스프링 컨테이너를 통해 빈들이 관리되고 있기 때문에 편리하게 사용할 수 있는 것이다.



<br><br>


