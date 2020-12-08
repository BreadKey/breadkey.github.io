---
title: 스프링 입문 5. 비즈니스 로직 층의 설계와 구현
author: breadkey
categories: study
tags: spring
layout: post
---
도메인 모델인지, 트랜잭션 스크립트인지에 관계 없이 업무 처리(비즈니스 로직)을 하는 것이 비즈니스 로직 층의 주요 역할이다. 오목형 레이어와 DI를 활용하면 데이터 액세스나 웹을 경유하는 기술 처리를 비즈니스 로직 층 이외의 층에 맡길 수 있다. 하지만 비즈니스 로직과 밀접한 관계가 있는 트랜잭션 처리는 비즈니스 로직 층에서 잘 대응해야 한다.

## 트랜잭션이란?
관련된 여러 처리를 하나의 큰 처리로 취급할 때 쓰이는 단위이다. 원자성을 만족하기 위해 트랜잭션 내의 모든 처리가 성공했을 때 트랜잭션이 확정되고, 어딘가 실패하면 트랜잭션이 실패하기 전 상태로 돌아가야 한다.  
　  
웹 애플리케이션에서의 트랜잭션은 여러 다누이가 존재하고, 스프링이 서포트할 수 있는 단위는 한정적이다.  
　  
첫 번째 단위는 여러 업무에 걸친 트랜잭션이다. 고객이 주문한 상품을 발주하고 물건이 도착하면 고객에게 출하하기까지, 즉 수주 -> 공장 발주 -> 집하 -> 출하 이 과정이 하나의 트랜잭션이다. 여기서 원자성을 지키기 위해선 중간에 고객의 변심으로 주문을 취소한 경우 각 작업에서 확정한 내용들을 모두 없었던 일로 해야 한다.  
　  
두 번째 단위는 하나의 작업에서 사용자로부터의 여러 요청에 걸쳐서 발생하는 트랜잭션이다. 상품의 재고를 화면에 표시하고, 출하할 상품 수를 입력한 후 확정 버튼을 누르기까지의 트랜잭션 단위이다. 여러 사용자가 동시에 같은 상품의 재고 수를 갱신할 수 없도록 비관적 또는 낙관적 오프라인 락 구조가 필요하다.  
　  
세 번째 단위는 하나의 요청 안에서 데이터 소스(보통 데이터베이스)의 트랜잭션이다. 주문 확정의 요청을 받아 발주 테이블과 고객 테이블, 재고 테이블을 갱신하기까지의 단위이다. 재고 테이블에서 갱신에 실패한 경우 고객 테이블, 발주 테이블의 갱신도 취소해야 한다. 하나의 데이터 소스에 대한 트랜잭션을 로컬 트랜잭션이라고 부른다. 다수의 데이터 소스에 대한 트랜잭션을 글로벌 트랜잭션이라고 한다.

{% include figure.html src="/assets/img/transaction2.png" figcaption="세 단위의 트랜잭션" %}
스프링의 서포트 대상은 세 번째, 하나의 요청 안에서 데이터 소스의 트랜잭션이다.
### 트랜잭션의 경계
트랜잭션의 경계는 프레젠테이션 층과 비즈니스 로직 층 사이에 그어지는 것이 전통적인 방법이다. 구체적으로 프레젠테이션 층에 공개된 서비스 클래스의 메서드가 트랜잭션의 시작과 종료이다. 컨트롤러에서 서비스 클래스의 메서드가 호출되면 트랜잭션의 시작, 서비스 클래스의 메서드를 마치고 컨트롤러로 되돌아갈 때가 트랜잭션의 종료이다.

### 트랜잭션 처리를 구현하는 장소 문제
서비스 메서드의 시작과 종료에 맞춰 트랜잭션을 시작하고 종료한다는 것은 메서드의 시작과 끝부분에 트랜잭션의 시작과 종료를 기술한다는 의미가 아니다.  
　  
메서드의 시작과 끝부분에 트랜잭션의 시작과 종료를 기술하면 다음과 같은 문제가 발생한다. 에를들어 JDBC를 이용한다고 생각한다면 커넥션의 취득, 커밋, 롤백 등을 비즈니스 로직 층 안에서 호출해야 한다. 이렇게 되면 은닉되어야 할 JDBC의 API에 비즈니스 로직 층이 의존하게 된다. 또한 커넥션은 DAO에도 필요하므로 취득한 커넥션을 DAO에 인수등으로 넘겨줘야 하는 상황도 발생한다.

### AOP를 이용한 트랜잭션 처리
AOP를 사용하면 서비스 내부를 수정하지 않고 트랜잭션 처리를 구현할 수 있다. 트랜잭션 처리를 위한 어드바이스를 개발자가 직접 구현할 필요 없이 스프링이 제공하는 트랜잭션 매니저와 트랜잭션 어드바이스를 이용할 수 있다.

* * *
## 트랜잭션 매니저
트랜잭션의 시작과 종료, 롤백 처리를 비롯해 트랜잭션의 정의 정보(롤백의 조건이나 독립성 레벨 등)을 세밀하게 설정할 수 있다. 또한, 데이터 액세스 기술(JDBC, 하이버네이트, MyBatis)을 은닉해주므로 데이터 액세스 기술이 바뀌어도 같은 방법으로 트랜잭션 매니저를 이용할 수 있다.
### 트랜잭션 정의 정보

#### 전파(propagation) 속성
{% include figure.html src="/assets/img/propagation.png" figcaption="트랜잭션의 전파" %}
1처럼 직접 호출하는 경우에 트랜잭션의 서비스 1의 메서드가 호출됐을 때 트랜잭션이 시작된다. 2의 경우에는 서비스 2 메서드가 호출됐을 때 트랜잭션이 시작되고, 그 트랜잭션 안에서 서비스 1이 호출된다. 이 때 동시에 트랜잭션을 새로 시작할지 아니면 원래 트랜잭션을 그대로 이어갈지 선택해야 한다. 이렇게 전파 방법을 선택하는 것이 전파 속성이다.  
　  
전파 속성은 7종류가 있는데 다음과 같이 설정할 수 있다.

<table style="width: 100%">
	<tr>
		<th rowspan="2">전파 속성</th>
		<th colspan="2">서비스 1에 대해서 설정했을 때의 동작</th>
	</tr>	
	<tr>
		<th>1의 경우</th>
		<th>2의 경우</th>
	</tr>
	<tr>
		<td>PROPAGATION_REQUIRED (기본 값)</td>
		<td>트랜잭션을 시작</td>
		<td>서비스 2의 트랜잭션에 참가</td>
	</tr>
	<tr>
		<td>PROPAGATION_REQUIRES_NEW</td>
		<td>트랜잭션을 시작</td>
		<td>새로운 트랜잭션을 시작</td>
	</tr>
	<tr>
		<td>PROPAGATION_SUPPORTS</td>
		<td>트랜잭션을 하지 않음</td>
		<td>서비스 2의 트랜잭션에 참가</td>
	</tr>
	<tr>
		<td>PROPAGATION_MANDATORY</td>
		<td>예외 발생</td>
		<td>서비스 2의 트랜잭션에 참가</td>
	</tr>
	<tr>
		<td>PROPAGATION_NESTED</td>
		<td>트랜잭션을 시작</td>
		<td>부분적인 트랜잭션을 시작</td>
	</tr>
	<tr>
		<td>PROPAGATION_NEVER</td>
		<td>트랜잭션을 하지 않음</td>
		<td>예외 발생</td>
	</tr>
	<tr>
		<td>PROPAGATION_NOT_SUPPORTED</td>
		<td>트랜잭션을 하지 않음</td>
		<td>트랜잭션을 하지 않음</td>
	</tr>
</table>

각각의 경우는 다음과 같은 경우에 설정할 수 있다.
* 항상 최초로 호출되는 발주 서비스 오브젝트의 메서드는 모두 기본 값으로 설정
* 단독으로 트랜잭션이 완결되고 다른 많은 트랜잭션이 병행해서 이용하는 번호 할당 서비스 오브젝트의 발주 번호 부여 메서드는 PROPAGATION_REQUIRES_NEW로 설정
* 트랜잭션에 포함되지 않고 이용되기도 하는 고객 서비스 오브젝트의 검색 메서드는 PROPAGATION_SUPPORTS로 설정
* 다른 서비스에서 이용되는 것이 전제고 트랜잭션이 시작되지 않으면 이용할 수 없는 재고 서비스 오브젝트의 재고 수량 감소 메서드는 PROPAGATION_MANDATORY로 설정
{% include figure.html src="/assets/img/propagation_example.png" figcaption="전파 속성 설정 예제" %}

#### 독립성 수준
트랜잭션 처리가 병행해서 실행될 때 각 트랜잭션의 독립성을 결정하는 것이다.  
　  
트랜잭션 1과 트랜잭션 2가 나란히 실행되고 있을 때, 트랜잭션 1에서 레코드를 갱신했다고 한다. 물론 아직 트랜잭션이 끝나지 않았으므로 커밋하지 않았다. 이어서 트랜잭션 2는 트랜잭션 1에서 갱신한 데이터를 읽어오려고 할 때, 읽어오려는 데이터는 커밋되지 않은 불확정한 상태이다. 이 때 트랜잭션 2가 갱신된 데이터를 읽어와도 되는가에 대해 문제가 생긴다. 모순되지 않게 처리하려면 트랜잭션 1이 커밋된 후 데이터를 읽어와야 한다. 이렇게 처리하는 속성이 독립성이다.  
　  
스프링에서 지정할 수 있는 독립성 수준은 5가지가 있다.
<table style="width: 100%">
	<tr>
		<th>독립성 수준</th>
		<th>의미</th>
	</tr>
	<tr>
		<td>ISOLATION_READ_COMMITTED</td>
		<td>다른 트랜잭션이 변경했지만 아직 커밋하지 않은 데이터는 읽어낼 수 없음</td>
	</tr>
	<tr>
		<td>ISOLATION_READ_UNCOMMITTED</td>
		<td>다른 트랜잭션이 변경하고 아직 커밋하지 않은 데이터를 읽어낼 수 있음</td>
	</tr>
	<tr>
		<td>ISOLATION_REPETABLE_READ</td>
		<td>트랜잭션 내에서 여러 번 데이터를 읽어올 때, 다른 트랜잭션이 도중에 데이터를 갱신해도 이전과 같은 값을 읽어냄</td>
	</tr>
	<tr>
		<td>ISOLATION_SERIALIZABLE</td>
		<td>트랜잭션을 하나씩 순서대로 처리해 독립시킴</td>
	</tr>
	<tr>
		<td>ISOLATION_DEFAULT</td>
		<td>데이터베이스가 제공하는 기본 독립성 수준을 이용</td>
	</tr>
</table>

병행 실행되는 트랜잭션을 생각했을 때, 모순되는 대표적인 상태는 Dirty Read, Unrepetable Read, Phantom Read라고 하는 세 가지 상태이다.  
　  
* **Dirty Read**
: 다른 트랜잭션이 변경했지만, 아직 커밋하지 않은 데이터를 읽어내는 것이다.
* **Unrepeatable Read**
: 트랜잭션 내에서 같은 데이터를 여러 번 읽을 때, 다른 트랜잭션이 해당 데이터를 변경해 이전의 데이터와 다른 데이터를 읽어내는 것이다.
* **Phantom Read**
: 트랜잭션 내에서 같은 데이터를 여러 번 읽을 때, 다른 트랜잭션이 새 레코드를 추가하면 새로이 추가된 레코드를 읽어내는 것이다.

이 세 모순된 상태를 어디까지 허용할지 나타낸 것이 독립성의 수준이다

<table style="width: 100%">
	<tr>
		<th>독립성 수준</th>
		<th>Dirty Read</th>
		<th>Unrepeatable Read</th>
		<th>Phantom Read</th>
	</tr>
	<tr>
		<td>ISOLATION_READ_UNCOMMITTED</td>
		<td>O</td>
		<td>O</td>
		<td>O</td>
	</tr>
	<tr>
		<td>ISOLATION_READ_COMMITTED</td>
		<td>X</td>
		<td>O</td>
		<td>O</td>
	</tr>
	<tr>
		<td>ISOLATION_REPEATABLE_READ</td>
		<td>X</td>
		<td>X</td>
		<td>O</td>
	</tr>
	<tr>
		<td>ISOLATION_SERIALIZABLE</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>
</table>

아래로 내려올수록 모순된 상태를 허용하지 않으며 독립성 수준이 높다. 독립성이 가장 높은 SERIALIZABLE을 사용하면 된다고 생각할 수 있지만, 독립성이 높을수록 성능이 나빠진다. 데이터 베이스의 독립성 수준을 구현하는 수단은 대부분 처리 대상 레코드를 완전히 잠그고 다른 쪽 트랜잭션 처리를 기다리게 함으로써 독립성을 확보하기 때문이다.  
　  
대부분 COMMITTED의 독립성 수준으로 설정해도 큰 문제가 없을 것이다.
#### 그 밖의 트랜잭션의 정보
* **만료 시간**
: 트랜잭션이 만료되는 시간을 초 단위로 설정
* **읽기 전용 상태**
: 트랜잭션 내의 처리가 읽기 전용으로 설정된다. 이 설정으로 DB나 ORM 프레임워크 쪽에서 최적화가 이루어진다.
* **롤백 대상 예외**
: 어느 예외가 던져졌을 때 롤백할지 설정할 수 있다. 기본적으로 RuntimeException 및 그 서브 클래스 예외에서 롤백이 이루어지고 검사 예외는 던져져도 롤백되지 않으니 주의해야 한다.
* **커밋 대상 예외**
: 어느 예외가 던져졌을 때 커밋할지 설정할 수 있다. 기본적으로 검사 예외가 던져졌을 때 커밋이 이루어진다.

### 트랜잭션 매니저의 구현 클래스
트랜잭션 매니저의 이용 방법은 데이터 액세스 기술에 의존하지 않는데, 이는 데이터 액세스 기술별로 제공되는 트랜잭션 매니저의 구현 클래스가 공통 인터페이스를 구현함으로써 실현된다. 공통 인터페이스는 PlatformTransactionManager로 구현 클래스는 DataSourceTransactionManager, HibernateTransactionManager, JpaTransactionManager 등이 있다.  

<table>
	<tr>
		<th>구현 클래스</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>DataSourceTransactionManager</td>
		<td>하나의 DataSource에 대해 트랜잭션 제어를 하는 트랜잭션 매니저</td>
	</tr>
	<tr>
		<td>HibernateTransactionManager</td>
		<td>하이버네이트의 Session에 대해 트랜잭션 제어를 하는 트랜잭션 매니저</td>
	</tr>
	<tr>
		<td>JpaTransactionManager</td>
		<td>JPA의 EntityManager에 대해 트랜잭션 제어를 하는 트랜잭션 매니저</td>
	</tr>
	<tr>
		<td>JdoTransactionManager</td>
		<td>JDO의 PersistenceManager에 대해 트랜잭션 제어를 하는 트랜잭션 매니저</td>
	</tr>
	<tr>
		<td>JtaTransactionManager</td>
		<td>JTA를 사용하는 트랜잭션 매니저</td>
	</tr>
</table>
사용할 데이터 액세스 기술에 맞게 적절한 구현 클래스를 선택하고 Bean에 등록한다

DataSourceTransactionManager 등록
```xml
<bean id="transactionManager"
	class = "org.springframework.jdbc.datasource.DataSourceTransactionManager">
	<property name="dataSource" ref="dataSource" />
</bean>
```

* * *
## 트랜잭션 기능의 사용법
* 선언적 트랜잭션
: Bean 정의 파일 또는 어노테이션으로 지정하는 방법. 지정할 때 트랜잭션 정의 정보도함께 설정. 나머지는 스프링이 자동으로 프록시를 생성해 트랜잭션 처리. 소스코드에 트랜잭션 처리를 기술할 필요가 없다.
* 명시적 트랜잭션
: API를 직접 호출해서 트랜잭션 처리를 하는 방법

### 선언적 트랜잭션 설정
설정할 수 있는 트랜잭션 정의 정보와 기본값은 다음과 같다.
<table>
	<tr>
		<th>설정명</th>
		<th>기본값</th>
		<th>기본값의 비고</th>
	</tr>
	<tr>
		<td>전파 속성</td>
		<td>PROPAGATION_REQUIRED</td>
		<td></td>
	</tr>
	<tr>
		<td>독립성 수준</td>
		<td>ISOLATION_DEFAULT</td>
		<td>데이터베이스의 기본 독립성 수준을 적용</td>
	</tr>
	<tr>
		<td>만료 시간</td>
		<td>-1</td>
		<td>만료 시간 없음</td>
	</tr>
	<tr>
		<td>읽기 전용 상태</td>
		<td>false</td>
		<td></td>
	</tr>
	<tr>
		<td>롤백 대상 예외</td>
		<td>설정하지 않음</td>
		<td>실행 시 예외가 롤백 대상</td>
	</tr>
	<tr>
		<td>커밋 대상 예외</td>
		<td>설정하지 않음</td>
		<td>검사 예외가 커밋 대상</td>
	</tr>
</table>

#### Bean 정의 파일에 의한 선언적 트랜잭션
tx 스키마를 사용해 트랜잭션의 어드바이스를 설정하고 정의 정보를 설정할 수 있다..
```xml
<tx:advice id="transactionAdvice" transaction-manager="transactionManager">
	<tx:attributes>
		<tx:method name="update*" 
			propagation:="REQUIRED"
			isolation="READ_COMMITTED"
			timeout="10"
			read-only="false"
			rollback-for="BusinessException"/>
	</tx:attributes>
</tx:advice>
```
#### 어노테이션에 의한 선언적 트랜잭션
@Transactional 어노테이션을 사용한다.
```java
@Transactional(
	propagation=Propagation.REQUIRED,
	isolation=Isolation.READ_COMMITTED,
	timeout=10,
	readOnly=false
	rollbackFor="BusinessException.class")
public void update...
```
### 명시적 트랜잭션 사용법
PlatformTransactionManager를 Autowired로 인젝션해 사용한다.
#### 명시적 트랜잭션의 용도
같은 클래스 안의 처리 일부에서 트랜잭션 처리를 하고 싶을 떄 사용할 수 있다.