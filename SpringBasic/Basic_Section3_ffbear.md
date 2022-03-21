# 스프링 핵심 원리 - 기본편
_강의를 듣고 강의 내용을 기반으로 작성하였습니다._

<br>

### IOC - Inversion Of Control 제어의 역전

<br>

객체 지향 설계 원칙 - SOLID 에서 봤을때 기존의 `MemberServiceImpl` 은 `DIP` 를 위반한다. `MemberServiceImpl` 은 `MemberService` 
인터페이스의 구현체이지만 `new` 연산자를 통해서 직접 `MemoryMemberRepository` 객체를 생성해서 구체화 하고 있으므로 `DIP` 를 위반한다. 이렇게 
설계했을때 결합도가 높아져서 유연함이 떨어지기 때문에 유지보수가 어려워지는 문제가 있다.   

그래서 이것을 방지하기 위해 `AppConfig` 클래스를 만들어서 `MemberService` 를 호출하면 `MemberServiceImpl` 에 `MemoryMemberRepository`
객체를 넣어서 리턴해주고 `MemberServiceImpl` 에서는 빈 `MemberRepository` 객체를 생성자에 넣어준다.

<br>

````java

public class AppConfig {

    public MemberService memberService() {
        return new MemberServiceImpl(new MemoryMemberRepository());
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

이렇게 설계하면 `MemberServiceImpl` 는 `AppConfig` 를 통해 주입받으므로 변경에 닫혀 있으므로 `OCP` 도 만족하면서 `DIP` 도 만족한다. 결국
`MemberServiceImpl` 를 외부인 `AppConfig` 에서 제어하기 때문에 `제어의 역전` 이라고 부른다.
> 생성자를 통해서 외부에서 객체를 받으므로 **생성자주입** 이라고도 한다.

- **DIP** 의존관계 역전 원칙 (Dependency inversion principle) : 추상화에 의존해야지, 구체화에 의존하면 안된다.
- **OCP** 개방-폐쇄 원칙 (Open/closed principle) : 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 한다.

<br>

`AppConfig` 처럼 객체를 생성하고 관리하면서 의존관계를 연결해주는 것을 `IOC 컨테이너` 또는 `DI 컨테이너` (더 많이 사용함) 라고 부른다.

<br>
<hr>


### DI - Dependency Injection 의존성 주입

<br>

`AppConfig` 클래스가 생긴 이후로 `MemberServiceImpl` 입장에서는 어떤 객체가 들어와서 실행되는지는 알 수 없다. 애플리케이션의 실행 시점에서야 
비로소 `AppConfig` 에서 `MemoryMemberRepository` 객체를 넣어주는것을 알게된다. `MemberServiceImpl` 의 관점에서 외부( `AppConfig` )
에서 의존관계를 넣어주기 때문에 이것을 `의존관계 주입` 이라고 한다.  

의존관계 주입으로 인해 결합도나 낮아지고 유연해지기 때문에 클라이언트 코드를(`MemberServiceImpl`)  변경하지 않고 클라이언트에서 사용하는 인스턴스를 
변경할 수 있다. 






<br><br>
