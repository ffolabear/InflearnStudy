<h2> 컴포넌트 스캔 </h2>

<br>

> 컴포넌트 스캔과 의존관계 자동 주입 시작하기.

스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 **컴포넌트 스캔**이라는 기능을 제공한다.

또, 의존관계도 자동으로 주입하는 @Autowired를 지원한다.

<br>

cf. `AnnotationConfigApplicationContext

<img width="549" alt="스크린샷_2022-02-24_오후_7 59 34" src="https://user-images.githubusercontent.com/48312457/155759682-e0369426-8e06-432b-aaa8-819323dc6d4d.png">

<img width="825" alt="스크린샷_2022-02-24_오후_7 59 43" src="https://user-images.githubusercontent.com/48312457/155759700-3a584ee0-ce9a-468f-a913-c805ab09c4de.png">

<br>

Spring에서 Bean의 생성과 Bean들의 관계설정 같은 제어를 담당하는 IoC 오브젝트를 BeanFactory라고 한다.

보통 BeanFactory를 확장한 ApplicationContext를 사용하는데 ApplicationContext는 IoC방식을 따라 만들어진 일종의 BeanFactory라고 생각하면 된다.

BeanFactory의 종류 중 하나가 AnnotationConfigApplicationContext.

@Configure Annotation을 이용한 Java Code를 설정정보로 사용하려면 AnnotationConfigApplicationContext 를 이용한다.

<br>


> @ComponentScan은 @Component가 붙은 모든 클래스를 스프링 빈으로 등록함


이때 스프링 빈의 기본 이름은 클래스 명을 사용하되, 맨 앞글자만 소문자를 사용한다. (임의로 지정할 수도 있다. @Component(”이름"))
<br><br>


> @Autowired 의존관계 자동 주입

스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입한다.

같은 타입을 찾아서 주입한다. (우선적으로)

컴포넌스 스캔의 탐색 위치와 기본 스캔 대상

basePackages = “hello.core.member“ : 범위를 설정해줄 수 있음. 이 패키지의 하위 패키지를 모두 탐색

basePakageClasses : 해당 클래스의 ‘패키지’부터 찾음.

지정하지 않는다면 ? Default는 ?? → 지정하지 않으면 @ComponentScan이 붙은 설정 정보 클래스의 ‘패키지’부터 하위 디렉토리를 모두 뒤져서 스프링빈을 등록한다.

<br>

“권장하는 방법”

패키지 위치를 지정하지 않고, 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것

스프링부트에는 @SpringBootApplication에 컴포넌트 스캔이 있어서 다 알아서 스프링빈 등록을 한다.

@Component 뿐만 아니라 @Controller, @Service, @Repository, @Configuration .. 도 추가됨!

그러나 사실 애노테이션(@)은 상속관계라는 것이 없다. 애노테이션이 특정 애노테이션을 들고 있는 것을 인식할 수 있는 것은 자바 언어가 지원하는 기능이 아니고, 스프링이 지원하는 기능.

<br>

> 필터:

includeFilter : 스캔 대상 추가

excludeFilter : 스캔 대상 제외
<img width="912" alt="스크린샷_2022-02-24_오후_8 55 52" src="https://user-images.githubusercontent.com/48312457/155759736-c551942f-ad5f-4efb-8227-d0e0d96a88da.png">

<br>


> 중복 등록과  충돌

1. 자동 빈 등록 vs 자동 빈 등록
2. 수동 빈 등록 vs 자동 빈 등록
<br>
1번은 오류를 발생시킴.

2번은 수동 빈이 우선권을 가진다. 자동 빈을 오버라이딩 한다.

그러나 스프링 부트에서는 2번 경우에서, 충돌이 나며 오류가 나도록 한다.

//개발할 때, 코드가 줄어들더라도 덜 명확해진다면 ? 코드를 동일한 걸 쓰더라도 명확해진다면? 후자가 좋다. 누가 봐도 명확하게.. 혼자만 쓰는 것이 아니므로.

<br>

**의존관계 자동 주입**

1. 생성자 주입 : 이름 그대로 생성자에서 한 번 주입받는 것. (불변, 필수(값이 무조건 있어야 한다.))
- 생성자가 1개만 있으면 Autowired를 생략할 수 있다.
2. 수정자 주입 : (setter 주입) : 선택, 변경 가능성이 있는 의존관계에 사용. 생성자보다 늦게 의존관계가 등록된다.
3. 필드 주입 : 필드에 (변수에) 바로 주입하는 방법
- 코드가 간결해서 많은 개발자들을 유혹하지만 외부에서 변경이 불가능해서 테스트하기 힘들다는 단점이 있다. DI 프레임워크가 없으면 아무것도 할 수 없다. (테스트코드(SpringBootTest)나 스프링 설정 목적의 @Configuration에서만 사용)
4. 일반 메서드 주입
- (일반적으로 생성자, 수정자로 해결)
