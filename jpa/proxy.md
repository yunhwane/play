## 프록시

- 엔티티를 조회할 때, 연관된 엔티티가 항상 사용되는 것이 아니다.

### member 
```java
@Entity
@Getter
public class Member{

    private String username;

    @ManyToOne
    private Team team;
}
```
### team
```java
@Entity
@Getter
public class Team{
    private String teamName;
}
```
## 회원과 팀의 대한 정보를 출력하는 코드 예시 
```java
public void selectMemberAndTeam(String memberId){
    Member member = em.find(Member.class, memberId);
    Team team = member.getTeam();
}
```

## 회원만 출력하는 코드 예시
```java
public void selectMember(String memberId){
    Member member = em.find(Member.class, memberId);
}
```
### 지연 로딩
- 회원만 출력하는 코드를 보게 되면 em.find()로 회원 엔티티 조회할때 연관된 팀 엔티티도 함께 조회한다. 하지만 팀 엔티티는 쓰일 일이 없기에 효율적이지 않기 때문에 JPA는 데이터베이스를 조회하는 것을 지연하는 방법을 지원하는데 이것을 지연로딩이라고 한다.

### 프록시 
- 지연로딩에서 실제 엔티티 객체를 조회하는 것 대신에 가짜 객체를 조회하여 지연시킬 수 있는 것을 프록시 객체라고 한다.

# PROXY
```java
Member member = em.find(Member.class, "member1");
```
- 영속성 컨텍스트에서 member1 에 대한 엔티티가 없으면 실제 데이터베이스 조회
```java
Member member = em.getReference(Member.class, "member1");
```
- 엔티티를 실제 사용하는 시점까지 데이터베이스 조회를 미루고 싶으면 EntityManager.getReference() 가짜 엔티티 객체 조회


## 특징
![스크린샷 2023-12-03 오후 9 31 46](https://github.com/yunhwane/jpa-querydsl/assets/147581818/2be313e9-3d21-46d5-82ea-2dba0128ea44)
- 프록시 클래시는 실제 클래스를 상속받아서 만들어지므로 겉 모양은 같다.
- 사용하는 관점에서 이것이 프록시인지 진짜 인지 구분하지 않고 사용한다.
![스크린샷 2023-12-03 오후 9 33 05](https://github.com/yunhwane/jpa-querydsl/assets/147581818/caea48f6-9060-4ecb-84bf-97f381c4d2a4)

- 프록시 객체는 실제 객체에 대한 Target(참조)를 보관하여 실제 메서드를 호출하게끔 이루어져 있다.

## 초기화 방식은 ?
![스크린샷 2023-12-03 오후 9 34 08](https://github.com/yunhwane/jpa-querydsl/assets/147581818/cda89c70-2774-4037-bf71-fc2310eceb67)

1. 처음 프록시 객체 참조는 null
2. JPA가 영속성 컨텍스트에 초기화 요청
3. DB 조회하여 실제 객체 생성
4. 기존 프록시 객체와 실제 객체 참조 연결

## 중요한 특징
- 처음 사용할 때 한번만 초기화 한다.
- 프록시 객체를 초기화 할 때, 프록시 객체가 실제 엔티티로 바뀌는 것이 아닌 target을 통하여 접근하는 것
- 프록시 객체는 실제 엔티티를 상속받는 것이며, 타입 체크 시 == 사용이 아니라, instance of를 사용한다.
- 영속성 컨텍스트에 찾는 엔티티가 있으면 em.getReference()를 호출해도 실제 객체를 반환
```java
Member refMember = em.getReference(Member.class, "member1.getId()");
Member findMember = em.find(Member.class, "member1.getId()");
System.out.println("refMember == findMember :" + (refMember == findMember));
```
- 코드 결과는 true ref와 find 둘다 프록시 객체이다.
- 한번 프록시로 조회하면 실제 객체로 조회하는 건 맞으나, 프록시 객체로 반환한다.
- ref == find true 보장
```java
Member refMember = em.getReference(Member.class, member1.getId());
em.detach(refMember); // 영속성 컨테스트가 관리 X -> 준영속
refMember.getusername(): // Could not initialize proxy 오류 발생
```
- 준영속 상태 객체는 영속성 컨텍스트의 도움을 못받기 때문에 프록시 초기화가 불가능함
