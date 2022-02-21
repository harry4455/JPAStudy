# Chapter 4. 엔티티 매핑

JPA 사용에 있어 가장 중요한 일은 엔티티와 테이블을 정확히 매핑하는 것.
따라서 매핑 어노테이션 숙지가 필요

- 객체와 테이블 매핑 : @Entity, @Table
- 기본 키 매핑 : @Id
- 필드와 컬럼 매핑 : @ Column
- 연관관계 매핑 : @ManyToOne, @JoinColumn

## 4.1 @Entity

JPA를 사용해서 테이블과 매핑할 클래서는 @Entity 어노테이션을 필수로 붙어야 한다.
어노테이션이 붙으면 해당 클래스는 JPA가 관리한다는 의미
 
|속성|기능|기본값|
|:---|:---|:---|
|name|PA에서 사용할 엔티티의 이름을 지정. 기본값인 클래스의 이름을 사용. 다른 클래스와 이름이 겹치지 않도록 주의 필요.|설정하지 않으면 클래스 명대로 따라감|

#주의사항
기본 생성자는 필수
final 클래스, enum, interface, inner 클래스에는 사용 불가
저장할 필드에 final 사용하면 안됨

## 4.2 @Table

엔티티와 매핑할 테이블을 지정
생략하면 매핑한 엔티티 이름을 테이블 명으로 사용한다

|속성|기능|기본값|
|:---|:---|:---|
|name|매핑할 테이블 이름|엔티티 이름을 사용|
|catalog|catalog 기능이 있는 DB에서 catalog 매핑||
|schema|schema 기능이 있는 DB에서 schema 매핑||
|uniqueConstraints(DDL)|DDL 생성 시에 유니크 제약조건을 만든다. 2개 이상의 복합 유니크 제약조건도 만들 수 있음. DDL을 만들때만 사용||

## 4.3 다양한 매핑 사용

## 4.4 데이터베이스 스키마 자동 생성

JPA는 매핑정보와 데이터베이스 방언을 사용해서 데이터베이스 스키마를 생성함
스키마 자동 생성 기능 (hibernate.hbm2ddl.auto)
애플리케이션 실행 시점에 데이터베이스 테이블을 자동으로 생성

그러나 운영환경에서 사용할 만큼 DDL이 완벽하지 않으므로 개발 환경에서 사용하거나 매핑을 할때 참고정도로 보는 것이 좋음.

|옵션|설명|
|:---|:---|
|create|기존 테이블을 삭제하고 새로 생성 (DROP + CREATE)|
|create-drop|create 속성에 추가로 애플리케이션 종료 시 생성상 DDL을 제거|
|update|DB 테이블과 에티티 매핑 정보를 비교해서 변경 사항만 수정|
|validate|DB 텡블과 엔티티 매핑정보를 비교해서 차이가 있으면 경고를 남기고 애플리케이션을 실행하지 않음. DDL 수정 안함|
|none|자동 생성 기능을 사용하지 않으려면 속성 자체를 삭제하거나 유효하지 않은 옵션 값을 주면 됨|
|update|DB 테이블과 에티티 매핑 정보를 비교해서 변경 사항만 수정|

## 4.5 DDL 생성 기능

스키마 자동 생성하기를 통해 만들어지는 DDL에 제약조건이 추가되는 경우

ex) not null 조건, varchar 크기 조건

-> 이러한 기능들은 DDL 생성할 때만 사용되고 JPA의 실행 로직에는 영향을 주지 않음

## 4.6 기본 키 매핑

기본 키를 애플리케이션에서 직접 할당하는 대신에 데이터베이스가 생성해주는 값을 사용하려면 어떻게 매핑해야 하는가?
ex) Oracle - sequence object, MySQL - AUTO_INCREMENT

JPA가 제공하는 DB 기본 키 생성 전략
* 직접 할당 : 기본 키를 애플리케이션에 직접 할당
* 자동 생성 : 대리키 사용 방식
 - IDENTITY : 기본 키 생성을 DB에 위임
 - SEQUENCE : DB 시퀀스를 사용해서 기본 키를 할당
 - TABLE : 키 생성 테이블을 사용
 
 데이터베이스 벤더마다 지원 하는 방식이 다르기에 자동 생성 전략이 다양함
 
### 4.6.1 기본 키 직접 할당 전략
 
 기본 키를 직접 할당하라면 @Id로 맵핑하면 된다.
 할당 전략은 em.persist() 로 엔티티를 저장하기 전에 애플리케이션에서 기본 키를 직접 할당하는 방법이다.
 
### 4.6.2 IDENTITY 전략
 
 IDENTITY는 기본 키 생성을 DB에 위임하는 전략 (주로 MySQL, PostgreSQL, SQL server, DB2에 사용)
 MySQL의 AUTO_INCREMENT 처럼 DB에 값을 저장하고기본 키 값을 구할 수 있을 때 사용.
 
 개발자가 엔티티에 직접 식별자를 할당하는 경우에는 @Id 어노테이션만 있으면 되지만, 지금 같이 식별자가 자동 생성되는 경우에는 @GeneratedValue 어노테이션을 사용한 전략이 필요
 IDENTITY 전략을 사용하려면 @GeneratedValue에 속성값으로 GeneratedType.IDENTITY를 지정
 이를 통해 JPA는 기본 키 값을 가져오기 위해 DB를 추가로 조회
 
### 4.6.3 SEQUENCE 전략
 
 DB 시퀀스는 유일한 값을 순서대로 생성하는 특별한 DB 오브젝트
 이를 활용하여 기본 키를 생성 (Oracle, PostgreSQL, DB2, H2에서 사용)
 
 @SequenceGenerator를 통해 시퀀스 생성기를 등록하고, 속성 이름을 시퀀스와 맵핑
 이후 GenerationType.SEQUENCE로 키 생성 전략을 설정하여 generator로 시퀀스 생성기를 등록
 
 사용코드는 IDENTITY 전략과 같지만 내부 동작은 다름
 1. em.persist()를 호출 할 때 DB 시퀀스를 사용해 식별자를 조회
 2. 조회한 식별자를 엔티티에 할당한 후에 엔티티를 영속성 컨텍스트에 저장
 3. 트랜잭션을 커밋하여 플러시가 일어나면서 엔티티를 DB에 저장
 (** IDENTITY 전략은 먼저 엔티티를 DB에 저장한 후에 식별자를 조회해서 엔티티의 식별자에 할당)        
 
 [@SequenceGenerator]
 |속성|기능|기본값|
 |:---|:---|:---|
 |name|식별자 생성기 이름|필수|
 |sequenceName|DB에 등록되어 있는 시퀀스 이름|hibernate_sequence|
 |initialValue|DDL 생성시에만 사용됨. 시퀀스 DDL을 생성할 때 처음 시작하는 수를 지정|1|
 |allocationSize|시퀀스 한 번 호출에 증가하는 수 (성능 최적화에 사용)|50|
 |catalog, schema|DB catalog, schema 이름||
 
### 4.6.4 TABLE 전략
 
 키 생성 전용 테이블을 하나 만들고 여기에 이름과 값으로 사용할 칼럼을 만들어 DB 시퀀스를 흉내내는 전략
 모든 데이터베이스에 적용 가능
 
 @TableGenerator를 사용해서 테이블 키 생성기를 등록
 GeneratioType.TABLE를 선택하여 TABLE 전략을 사용
 @GeneratedValue.generator에 만든 테이블 키 생성기를 지정
 
 
 [@TableGenerator]
 |속성|기능|기본값|
 |:---|:---|:---|
 |name|식별자 생성기 이름|필수|
 |table|키생성 테이블명|hibernate_sequences|
 |pkColumnName|시퀀스 컬럼명|sequence_name|
 |valueColumnName|시퀀스 값 컬럼명|next_val|
 |pkColumnValue|키로 사용할 값 이름|엔티티 이름|
 |initivalValue|초기 값. 마지막으로 생성된 값이 기준|0|
 |allocationSize|시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨)|50|
 |catalog, schema|DB catalog, schema 이름||
 |uniqueConstraints(DDL)|유니크 제약 조건을 지정할 수 있음||

 [매핑할 DDL, 테이블 명]
 |{pkColumnValue}|{valueColumnName}|
 |:---:|:---:|
 |{pkColumnValue}|{initialValue}|


### 4.6.5 AUTO 전략

GenerationType.AUTO는 선택한 DB 방언에 따라 IDENTITY, SEQUENCE, TABLE 중 하나를 자동으로 선택

@GeneratedValue.strategy의 기본 값은 AUTO

DB를 변경해도 코드를 수정할 필요가 없다는 장점


### 4.6.6 기본 키 매핑 정리

영속성 컨텍스트는 엔티티를 식별자 값으로 구분하기에 엔티티를 영속 상태로 만들려면 식별자 값이 반드시 필요
아래는 em.persist()를 호출한 직후 발생한 일을 식별자 할당 전략별로 정리한 내용
 
 |전략|발생되는 일|
 |:---|:---|
 |직접 할당|em.persist()를 호출하기 전에 애플리케이션에서 직접 식별자 값을 할당해야 함. 식별자 값이 없으면 예외 발생|
 |SEQUENCE|DB 시퀀스에서 식별자 값을 획득한 후 영속성 컨텍스트에 저장|
 |TABLE|DB 시퀀스 생성용 테이블에서 식별자 값을 획득한 후 영속성 컨텍스트에 저장|
 |IDENTITY|DB에 엔티티를 저장해서 식별자 값을 획득한 후 영속성 컨텍스트에 저장(IDENTITY 전략은 테이블에 데이터를 저장해야 식별자 값을 획득할 수 있음)| 
 
 [참고 - 권장하는 식별자 전략 선택]
 
 - 자연 키 보다는 대리 키를 권장
 - 비즈니스 환경은 언젠가 변함
 - JPA는 모든 엔티티에 일관된 방식으로 대리 키 사용을 권장
 
  
## 4.7 필드와 컬럼 매핑 : 레퍼런스

<table>
 <tr>
  <td>분류</td>
  <td>매핑 어노테이션</td>
  <td>설명</td>
 </tr>
 <tr>
  <td rowspan="5">필드와 컬럼 매핑</td>
  <td>@Column</td>
  <td>컬럼을 매핑한다</td> 
 </tr>
 <tr>
  <td>@Enumerated</td>
  <td>자바의 enum 타입을 매핑한다.</td> 
 </tr>
 <tr>
  <td>@Temporal</td>
  <td>날짜 타입을 매핑한다.</td> 
 </tr>
 <tr>
  <td>@Lob</td>
  <td>BLOB, CLOB 타입을 매핑한다.</td> 
 </tr>
 <tr>
  <td>@Transient</td>
  <td>특정 필드를 DB에 매핑하지 않는다.</td> 
 </tr>
 <tr>
   <td>기타</td>
  <td>@Access</td>
  <td>JPA가 엔티티에 접근하는 방식을 지정한다.</td> 
 </tr>
</table> 

### 4.7.1 @Column
@Column은 객체 필드를 테이블 컬럼에 매핑

|속성|기능|기본값|
 |:---|:---|:---|
 |name|필드와 매핑할 테이블의 컬럼 이름|객체의 필드 이름|
 |insertable(거의 사용하지 않음)|엔티티 저장 시 이 필드도 같이 저장. false로 설정하면 이 필드는 DB에 저장하지 않는다. false 옵션은 읽기 전용일 때 사용|true|
  |updatable(거의 사용하지 않음)|엔티티 수정 시 이 필드도 같이 수정. false로 설정하면 DB에 수정하지 않음. false 옵션은 읽기 전용일 때 사용|true|
 |table(거의 사용하지 않음)|하나의 엔티티를 두 개 이상의 테이블에 매핑할 때 사용. 지정한 필드를 다른 테이블에 매핑할 수 있음|현재 클래스가 매핑된 테이블|
 |nullable(DDL)|null 값의 허용 여부를 설정. false로 설정하면 DDL 생성 시에 not null 제약조건이 붙음|true|
 |unique(DDL)|@Table의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약조건을 걸 때 사용. 만약 두 컬럼 이상을 사용해서 유니크 제약조건을 사용하려면 클래스 레벨에서 @Table.uniqueConstraints를 사용해야 함||
 |columnDeifinition(DDL)|DB 컬럼 정보를 직접 줄 수 있음|필드의 자바 타입과 방언 정보를 사용해서 적절한 컬럼 타입을 생성|
 |length(DDL)|문자 길이 제약조건, String 타입에만 사용|255|
 |precision, scale(DDL)|BigDecimal 타입에서 사용(BigInteger도 사용할 수 있음). precision은 소수점을 포함한 전체 자릿수를, scale은 소수의 자릿수. 참고로 double, float 타입에는 적용이 안됨. 아주 큰 숫자나 정밀한 소수를 다루어야 할 때만 사용|precision=19, scale=2|
 
### 4.7.2 @Enumerated

자바의 enum 타입을 매핑할 때 사용

 |속성|기능|기본값|
 |:---|:---|:---|
 |value|* EnumType.ORDINAL : enum 순서를 데이터베이스에 저장<br> * EnumType.STRING : enum 이름을 데이터베이스에 저장|EnumType.ORDINAL|


ORDINAL
- 장점 : DB에 저장되는 데이트 크기가 작음
- 단점 : 이미 저장된 enum의 순서를 변경할 수 없음

STRING
- 장점 : 저장된 enum의 순서가 바뀌거나 enum이 추가되어도 안전
- 단점 : DB에 저장되는 데이터의 크기가 ORDINAL에 비해 큼


### 4.7.3 @Temporal

날짜 타입(java.util.Date, java.util.Calendar)을 매핑할 때 사용

 |속성|기능|기본값|
 |:---|:---|:---|
 |value|* TemporalType.DATE : 날짜, 데이터베이스 date 타입꽈 매핑<br> * TemporalType.TIME : 시간, 데이터베이스 time 타입과 매핑 <br> * TemporalType.TIMESTAMP : 날짜와 시간, 데이터베이스 timestamp 타입과 매핑|TemporalType은 필수로 지정해야 함|

### 4.7.4 @Lob

BLOB, CLOB 타입과 매핑

지정할 수 있는 속성은 없음. 대신 매핑하는 필드 타입이 문자면 CLOB, 나머지는 BLOB 매핑

* CLOB : String, char[], java.sql.CLOB
* BLOB : byte[], java.sql.BLOB  

### 4.7.5 @Transient

따로 매핑하지 않음. 객체에 임시로 어떤 값을 보관하고 싶을 때 사용

### 4.7.6 @Access

JPA가 엔티티 데이터에 접근하는 방식을 지정

* 필드 접근 : AccessType.FIELD로 지정. 필드에 직접 접근. 필드 접근 권한이 private여도 접근 가능
* 프로퍼티 접근 : AcessType.PROPERTY로 지정. 접근자(Getter)를 사용.   
