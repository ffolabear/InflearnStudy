# 스프링 핵심 원리 - 연습문제
_스프링 핵심 원리 - 기본편 을 듣고 진행하는 연습문제입니다._

> 목표 : 주문 - 회원 -  할인로직 까지 구현하기

<br>

<div align="center">
    <img src="OrderDomain.png" width="70%">
</div>

<br>

### 기본 설정

- [스프링 부트 스타터 사이트](https://start.spring.io) 로 이동해서 스프링 프로젝트 생성  
    - 프로젝트 선택
        - Project: Gradle Project 
        - Spring Boot: 2.3.x 
        - Language: Java 
        - Packaging: Jar
        - Java: 11
            
    - Project Metadata
        - groupId: hello 
        -  artifactId: core
    -  Dependencies: 선택하지 않는다.

<br>

<hr>

<br>
    
## 회원 로직

<div align="center">
    <img src="MemberClassDiagram.png" width="70%">
</div>

- #### 회원 엔티티
    
    - ##### `Member`
        
        ```java
      
            public class Member {
                
                private Long id;
                private String name;
                private Grade grade;
            
                //기본생성자
                
                //Getter & Setter
          
            }
        ```
      
    - ##### `Grade`
            
        ```java
         public enum Grade {
                    BASIC,
                    VIP 
                }
        ```
                        
    
    
<br>

- #### 회원 저장소
    - ##### `MemberRepository` (인터페이스)

        ```java
        public interface MemberRepository {
            
            void 저장(회원);
            Member ID로회원찾기(회원 ID);
            
        }        
        ```

    - ##### `MemoryMemberRepository` - 인터페이스 구현체  (스프링컨테이너 관리 대상)

        ```java
        //컴포넌트 스캔 대상
        public class MemoryMemberRepository implements MemberRepository {
            //회원저장하는 Map
            //Key : ID, Value : Member 
               
            @Override
            public void 저장(회원) {
                //Map 에 회원 추가                      
            }
            
            @Override
            public Member ID로회원찾기(회원 ID) {
                //memberId에 해당하는 회원 리턴
            }
      

        }
        ```

<br>

- #### 회원 서비스
    - ##### `MemberService` (인터페이스)
        
        ```java
        public interface MemberService {
      
            void 회원가입(회원);
            Member 회원찾기(회원ID);
      
        }
        ``` 
    
    - ##### `MemberServiceImpl` - 인터페이스 구현체  (스프링컨테이너 관리 대상)
        ```java
        //컴포넌트 스캔 대상
        public class MemberServiceImpl implements MemberService {
        
            private final 멤버저장소;
            
            public MemberServiceImpl 생성자(멤버 저장소){
                //의존관계 생성자 주입
            }
            
            @Override
            public void 회원가입(회원){
                //회원저장소에 회원 저장
            }
      
            @Override
            public Member 회원찾기(회원ID){
                return 회원저장소에서 ID로 찾은 회원;
            }
        
        }
        ```


<br>

- #### 회원 가입 Main   
    - `MemberApp`
        ```java
            public class MemberApp {
                public static void main(String[] args) {
                
                    //스프링 컨테이너 객체 생성
                    //컨테이너에서 memberService 란 이름의 빈을 꺼내기
                    회원A = new 회원(아이디, 이름, 등급);
                    memberService.회원가입(회원A);
                    
                    회원B = 멤버서비스로 찾은 회원;
      
            }
        }
        ``` 
  
<br>

<!--
- #### 회원 가입 테스트 `MemberServiceTest`
    ```java
    public class MemberServiceTest {
        멤버서비스 전역변수;
        
        @BeforeEach
        public void 모든테스트전에 실행() {
            //스프링 설정정보 클래스 생서
        }
        
    }
    ```
-->



<br>

<hr>

<br>

## 주문 로직

<br>

<div align="center">
    <img src="OrderClassDiagram.png" width="70%">
</div>

- #### 할인 정책 
    - ##### `DiscountPolicy` (인터페이스)
        - `int discount(Member member, int price)`
        
        ```java
        public interface DiscountPolicy {
            int 할인(멤버, 가격);
        }
        ``` 

    - ##### ` FixDiscountPolicy` (인터페이스 구현체) - 정액 할인 정책(고정 금액)
    - ##### `RateDiscountPolicy` (인터페이스 구현체) - 정률 할인 정책(주문 금액에 따라 % 할인)
        
        ```java
        public class 할인정책 implements DiscountPolicy{
            
            @Override
            public int 할인(할인, 가격) {
                if(회원의 등급이 VIP){
                    //정액할인
                    return 1000;
      
                    //정률할인
                    return 가격 * 0.1;
                } else {
                    return 0;
                }
      
            }
            
        }
        ```

<br>

- #### 주문 엔티티
    - ##### `Order`
        ```java
        public class Order {
    
            private Long memberId;
            private String itemName;
            private int itemPrice;
            private int discountPrice;
      
            //기본생성자
            
            //계산 로직
            public int calculatePrice() {
                return itemPrice - discountPrice;
            }
      
            //Getter & Setter
    
        }
        ```


<br>

- #### 주문 서비스
    - ##### `OrderService` (인터페이스)
        ```java
        public interface OrderService {
            
            Order 주문생성(회원ID, 상품명, 상품가격);
        }
        ```

    - ##### `OrderServiceImpl` 인터페이스 구현체  (스프링컨테이너 관리 대상)
        ```java
        //컴포넌트 스캔 대상
        public class OrderServiceImpl implements OrderService {
      
            private final 회원저장소;
            private final 할인정책;
                
            public OrderServiceImpl 생성자(회원저장소, 할인정책){
                //의존관계 생성자 주입
            } 
            
            @Override
            Order 주문생성(회원ID, 상품명, 상품가격){
                회원A = 회원저장소(회원ID);
                할인 가격 = 할인정책(회원, 상품가격);
            }
        }
        ```


<br>

- #### 주문 Main
    - `OrderApp`
        ```java
        public class OrderApp {
            public static void main(String[] args) {
                            
                //스프링 컨테이너 객체 생성
                //컨테이너에서 memberService 란 이름의 빈을 꺼내기
                //컨테이너에서 orderService 란 이름의 빈을 꺼내기
                회원A = new 회원(아이디, 이름, 등급);
                memberService.회원가입(회원A);
                주문A = orderService(회원A정보);
            }
        }
        ```

<br>

<hr>

<br>    

## 스프링 설정정보 클래스

- `AutoAppConfig` (컴포넌트 스캔 대상)
    - 역할과 구현을 분리하고 DIP 와 OCP 를 지키기 위한 클래스
    ```java
    @Configuration
    @ComponentScan(
        basePackages = "hello.core.member",
        basePackageClasses = AutoAppConfig.class,
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)
    )    
    public class AutoAppConfig {
    
    }   
    ```

- `AppConfig` (컴포넌트스캔 사용하기 전 설정정보 클래스)
    ```java
        @Configuration
        public class AppConfig {
  
            @Bean
            public 회원서비스인터페이스 memberService() {
                return new 회원서비스구현체(회원저장소구현체());
            }
        
            @Bean
            public 회원저장소구현체 memberRepository() {
                return new 회원저장소구현체();
            }
        
            @Bean
            public 주문서비스 orderService() {
                return new 주문서비스구현체(회원저장소구현체, 할인정책구현체);
            }
        
            //할인 정책 변경시 이부분만 바꿔주면됨
            @Bean
            public 할인정책인터페이스 discountPolicy() {
                return new 정액할인;
                //return new 정률할인;
            }
        
    }

    ```


<br>
<br>
