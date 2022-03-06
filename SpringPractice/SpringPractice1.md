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

- #### 회원 가입 Main `MemberApp`   
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

    - ##### ` FixDiscountPolicy` (인터페이스 구현체) - 정액 할인 정책(고정 금액)
    - ##### `RateDiscountPolicy` (인터페이스 구현체) - 정률 할인 정책(주문 금액에 따라 % 할인)
        - `int discount(Member member, int price)`
        - 회원의 등급에(VIP, BASIC) 따라 할인금액을 책정 

<br>

- #### 주문 엔티티
    - ##### `Order`
        - `Long` memberId
        - `String` itemName
        - `int` itemPrice
        - `int` discountPrice
        - `Getter`, `Setter`, 
        - `calculatePrice`

<br>

- #### 주문 서비스
    - ##### `OrderService` (인터페이스)
        - `createOrder(Long memberId, String itemName, int itemPrice)`
            - `Order` 객체 리턴

    - ##### `OrderServiceImpl` 인터페이스 구현체  (스프링컨테이너 관리 대상)
        - DI 생성자 주입
        - `createOrder` 는 의존관계 주입된 객체 사용 


<br>

- #### 주문 Main `OrderApp`
    - 스프링컨테이너에서 가져온 빈에 새로운 회원을 저장
    - 등록한 회원의 정보로 새오운 주문 생성

<br>

<hr>

<br>    

## 스프링 설정정보 클래스

- `AutoAppConfig` (컴포넌트 스캔 대상)
    - 역할과 구현을 분리하고 DIP 와 OCP 를 지키기 위한 클래스



<br>
<br>
