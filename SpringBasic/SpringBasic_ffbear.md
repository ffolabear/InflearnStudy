# 스프링 핵심 원리 - 기본편
_강의를 듣고 강의용을 기반으로 작성하였습니다._

<br>

### IOC - Inversion Of Control 제어의 역전

<br>

객체 지향 설계 원칙 - SOLID 에서 봤을때 기존의 `MemberServiceImpl` 은 `DIP` 를 위반한다. `MemberServiceImpl` 은 `MemberService` 
인터페이스의 구현체이지만 `new` 연산자를 통해서 직접 `MemoryMemberRepository` 객체를 생성해서 구체화 하고 있으므로 `DIP` 를 위반한다. 이렇게 
설계했을때 결합도가 높아져서 유연함이 떨어지기 때문에 유지보수가 어려워지는 문제가 있다.   

그래서 이것을 방지하기 위해 `AppConfig` 클래스를 만들어서 `MemberService` 를 호출하면 `MemberServiceImpl` 에 `MemoryMemberRepository`
객체를 넣어서 리턴해주고 `MemberServiceImpl` 에서는 빈 `MemberRepository` 객체를 생성자에 넣어준다.

````java

public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository);
    }
}

````

<br>

```java

public class MemberServiceImpl implements MemberService {

    private final MemberRepository memberRepository;

    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}

```

이렇게 설계하면 `MemberServiceImpl` 는 `AppConfig` 를 통해 주입받으므로 변경에 닫혀 있으므로 `OCP` 도 만족하면서 `DIP` 도 만족한다.

- **DIP** 의존관계 역전 원칙 (Dependency inversion principle) : 추상화에 의존해야지, 구체화에 의존하면 안된다.
- **OCP** 개방-폐쇄 원칙 (Open/closed principle) : 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.




<br>

### DI - Dependency Injection 의존성 주입


<br>
