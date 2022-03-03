# 스프링 핵심 원리 - 연습문제
_스프링 핵심 원리 - 기본편 을 듣고 진행하는 연습문제입니다._

> 목표 : 주문 - 회원 -  할인로직 까지 구현하기

### 기본 설정

- [스프링 부트 스타터 사이트](https://start.spring.io/)로 이동해서 스프링 프로젝트 생성  
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
        - Long ID
        - String name
        - Enum `Grade` - BASIC, VIP
    
<br>

- #### 회원 저장소
    - ##### `MemberRepository` (인터페이스)
        - `save(Member member)`
        - `findById(Long memberId)`

    - ##### `MemoryMemberRepository` - 인터페이스 구현체  (스프링컨테이너 관리 대상)
        -  회원정보 담은 Map
        - `save(Member member)`
        - `findById(Long memberId)`

<br>

- #### 회원 서비스
    - ##### `MemberService` (인터페이스)
        - `join(Member member)`
        - `findMember(Long memberId)`
    
    - ##### `MemberServiceImpl` - 인터페이스 구현체  (스프링컨테이너 관리 대상)
        - DI (생성자주입)
        - `join()`, `findMember()` 둘다 `MemberRepository` 에서 가져온 객체 사용
    

<br>

- #### 스프링 설정정보 클래스 
    - `AutoAppConfig`

<br>

- #### 회원 가입 Main
    - 스프링 컨테이너에 저장된 빈 사용
        - 특정 빈을 가져와야 그 메서드를 사용할 수 있다. 
    - 회원 가입 ( `join` )
    - 특정 회원 조회( `findMember` )
    - 회원 가입 테스트코드 작성

<br>

<hr>

<br>

## 주문 로직













<br><br>    


