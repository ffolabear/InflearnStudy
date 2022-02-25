## 스프링 핵심 원리 - 기본편
_강의를 듣고 강의용을 기반으로 작성하였습니다._

## 스프링 의존관계 주입방법

### 1. 생성자 주입
스프링이 컴포넌트 스캔을 하면서 메서드를 스프링빈으로 등록할때 생성자를 호출하는데 이때 생성자에 `@Autowired` 가 있으면 생성자에 필요한 스프링빈들을 
스프링 컨테이너에서 꺼내와서 생성자에 주입해준다. 

- 생성자를 호출하는 딱 1번의 시점에서 호출되고 변하지 않는다.
  - 클래스의 호출과 동시에 연관관계 생성
- **불편**, **필수** 의존관계에서 사용

<br>

```java

@Component
public class OrderServiceImpl implements OrderService {

    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}

```

`final` 로 선언된 부분은 무조건 값이 있어야한다는 것을 의미한다. 만약 생성자에서 값을 넣어주는 부분이 없다면 초기화를 안해줬기 때문에 
오류가 발생한다. **생성자가 딱 1개만 있으면 `@Autowired` 를 생략해도 자동 주입 된다.**


<br>

### 2. Setter(수정자) 주입
자바빈 프로퍼티 규약에 있는 수정자 메서드를 통해서 의존관계를 주입하는 방법이다. 생성자 주입과는 다르게 선택, 변경 가능성이 있는 의존관계에 사용한다.
스프링빈은 크게 두가지 라이프 사이클을 도는데,   

1. 빈을 등록한다. 
2. 연관관계를 자동으로 주입한다. 

사실 생성자 주입은 객체를 생성함과 동시에 이루어지기 때문에 1번과 2번 단계가 동시에 일어나지만 수정자 주입은 그렇지 않기 때문에 
빈을 등록한 다음 의존관계를 주입한다. **수정자를 통해서 의존관계를 주입하므로 생성자가 필요없다.** 

<br>

```java

@Component
public class OrderServiceImpl implements OrderService {

    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;

    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) { 
        this.memberRepository = memberRepository;
    }

    @Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) { 
          this.discountPolicy = discountPolicy;
    }
}

```

만약 필수적으로 의존관계를 넣어야하는 대상이 아니거나 주입할 대상이 없어도 동작하게 하려면 `@Autowired(required = false)` 옵션을 주면 된다. 

<br>

### 3. 필드 주입
이름대로 필드에 주입하는 방법이다. 생성자에서 값을 넣거나 수정자로 넣는것이 아닌 필드에서 주입받기 때문에 외부에서 접근할 수 있는방법이 없기 때문에 결국 수정자를
만들어야 한다. 

<br>

```java

@Component
public class OrderServiceImpl implements OrderService {

    @Autowired
    private MemberRepository memberRepository;
    
    @Autowired
    private DiscountPolicy discountPolicy;
      
}
```
필드 주입 또한 수정자 주입처럼 생성자가 없기 때문에 외부에서 사용하려면 객체를 직접 생성해줘야하는데, 이렇게 생성한 객체는 스프링 컨테이너의 관리를 받을 수 없다.
이 말은 즉 `@Autowired` 를 받을수 없다는 뜻이므로 결국 에러가 발생하게 된다. 아래의 두 경우에서는 예외적으로 사용할수는 있다. 

- 애플리케이션의 실제 코드와 관계 없는 테스트 코드
- 스프링 설정을 목적으로 하는 `@Configuration` 같은 곳에서 특별한 용도로 사용


<br>

### 4. 일반 메서드 주입
일반 메서드를 통해서 주입 받을 수 있다. 생성자 주입과 수정자 주입안에서 대부분의 경우 해결하므로 사용할 일은 거의 없다. 
항상 주의할 점은 의존관계 자동 주입은 **스프링 컨테이너가 관리하는 스프링 빈이어야 동작한다는 것이다.**

<br>

```java

@Component
public class OrderServiceImpl implements OrderService {
    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;
    
    @Autowired
    public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy; 
    }
    
  }
```




<br><br>