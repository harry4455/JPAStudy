# 자바 ORM 표준 JPA 프로그래밍
## 1주차 (2022.01.22)

## 1장. JPA 소개
## 1.1 SQL을 직접 다룰때 발생하는 문제점

JPA (Java Persistence API)  

* 자바 진영의 ORM(Object Relational Mapping) 표준 기술

* 객체와 관계형 데이터베이스의 차이를 중간에서 해결

* 실행시점에 SQL을 만들어서 실행  
-> SQL을 직접 작성하는 것이 아니라 어떤 SQL이 실행될지 고려하면 된다  
-> 매서드로 데이터를 조작할 수 있다  

<p align="center">
<img src="https://images.idgesg.net/images/article/2019/04/jw_java_persistence_series_1200x1600_diagram-100792564-large.jpg?auto=webp&quality=85,70" style="width:300px; height:400px;" />
</p>

JPA 장점
* CRUD SQL을 작성할 필요가 없음  
* 조회된 결과가 자동으로 객체에 맵핑  
 -> 데이터 저장 계층에 작성 해야할 코드가 감소  
* 객체 중심으로 개발하여 생산성과 유지보수가 좋아지는 효과  
* 개발 과정에서 데이터베이스 변경 건이 있어도 큰 지장 없음  
* Spring Framework, 전자 표준 Framework 등 많은 곳에서 지원

SQL을 직접 다룰 때 발생하는 문제점  
* DB 관리를 위해 SQL 사용  
* 자바 애플리케이션은 JDBC API를 사용해서 SQL을 DB에 전달  
  <b>JDBC (Java DataBase Connectivity)</b>

# 1.1.1 반복, 반복 그리고 반복

* CRUD 기능을 개발하기 위해 반복해서 진행되는 순서 (DAO 개발)
1. SQL 작성
ex. SELECT MEMBER_ID, NAME FROM MEMBER M WHERE MEMBER_ID=?

2. JDBC API를 사용하여 SQL 실행
ex. ResultSet rs = stmt.executeQuery(sql);

3. 결과를 객체로 맵핑 (ex. Member)
ex. String memberId = rs.getString(“MEMBER_ID”);
String name = rs.getString(“NAME”);  
Member member = new Member();  
Member.setMemberId(memberId);  
…

* 회원 객체를 자바 컬렉션에 보관한다면?  
ex. list.add(member);

* 데이터베이스는 객체 구조와 다른 데이터 중심 구조를 가짐으로 객체를 직접 저장하거나 조회 불가

* 매번 비슷한 기능을 구현하기 위해서 반복적인 작업이 필요  
  -> 비효율적

# 1.1.2 SQL에 의존적인 개발

* 개발 완료된 기능에 요구사항이 추가되는 경우  
<i> ex. 회원의 연락처를 함께 저장하는 추가요구</i>  
  <b>[SQL과 JDBC API 수정]</b>

  1. 회원 객체에 연락처 필드 추가  
  2. SQL 수정
  3. 회원 객체의 연락처 값을 꺼내 등록 SQL에 전달  
  
  <b>[연쇄적으로 발생하는 수정 작업]  </b>

  4. 회원 조회 SQL 수정 및 객체에 연락처 추가 맵핑  
  5. 회원 수정 SQL 수정 및 객체에 연락처 추가 맵핑  
     …

* 연관된 객체가 추가되는 경우  
객체의 사용여부가 전적으로 사용하는 SQL에 달려 있음  
데이터 접근 계층을 통해 SQL을 숨겨도 DAO를 열어 어떤 SQL이 실행되는지 확인 필요  
-> 물리적으로는 계층 분할이 되었으나, 논리적으로 엔티티와 강한 의존관계를 지님

    <b>1. 진정한 의미의 계층 분할이 아니다.</b>  
    <b>2. 엔티티를 신뢰할 수 없다.</b>  
    <b>3. SQL에 의존적인 개발을 피하기 어렵다.</b>

# 1.1.3 JPA와 문제 해결
* 제공하는 API를 통해 SQL 작성 필요 없이 DB를 저장하고 관리  

    <b>ex. JPA가 제공하는 CRUD API</b>  
    jpa.persist(member);  // 저장  
    Member member = jpa.find(Member.class, memberId);   // 조회  
    jpa.setName();  // 수정  
    Team team = member.getTeam();  // 연관된 객체 조회





