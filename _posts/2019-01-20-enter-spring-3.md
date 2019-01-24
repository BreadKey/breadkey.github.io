---
title: 스프링 입문 3. 스프링 AOP
author: breadkey
categories: study
tags: spring
layout: post
---
공통화할 수 있는 처리는 오브젝트 안에 없는 편이 소스 코드의 가독성도 좋아지고 컴포넌트로서의 역활도 명확해진다. 테스트도 쉬워진다.  
　  
어떤 오브젝트가 원래 하지 않아도 될 로깅이나 트랜잭션 등의 처리를 그 오브젝트에서 분리해서 별도의 오브젝트로 구현하는 기술이다.

## AOP란?
> 업무 등 특점 책임이 있는 클래스, 예를 들어 주문 클래스나 계좌 클래스 안에 본질적인 처리만 기술하고, 본질적이지 않은 처리는 밖으로 꺼내는 기술이다.  
　  
로그 출력이나 예외 처리 등 공통화할 수 있는 처리를 Aspect라는 하나의 단위로 모아서 어떤 오브젝트가 원래 해야 할 일만을 하게 만드는 기술이다.
### AOP 용어
>> #### 애스펙트(Aspect)
횡단 관심사의 동작과 그 횡단 관심사를 적용하는 소스 코드상의 포인트를 모은 것이다. 하나 또는 그 이상의 어드바이스(동작)와 포인트컷(동작을 적용하는 조건)을 조합한 것이다.
#### 조인 포인트(Joint Point)
어드바이스가 실행하는 동작을 끼워 넣을 수 있는 때를 말한다. 코드가 실행된 때는 반드시 어드바이스가 동작하는 때이다.  
　  
개발자가 고려해서 만들어 넣을 수 없는 AOP의 사양이다. 스프링에서는 메서드가 호출될 때와 메서드가 원래 호출한 곳으로 돌아갈 때가 어드바이스를 끼워 넣을 수 있는 조인 포인트이다.
#### 어드바이스
조인 포인트에서 실행되는 코드를 말한다. 로그 출력이나 트랜잭션 관리 등의 코드가 기술된다.
#### 포인트컷
포인트와 어드바이스의 중간에 있으면서 처리가 조인 포인트에 이르렀을 때 어드바이스를 호출할지 선별한다. 포인트컷에 '메서드명에 hello라는 문자열이 있으면 어드바이스를 호출하지 않는다' 혹은 '메서드명에 Service라는 문자열이 있으면 어드바이스를 호출한다'라고 기술해두면 어드바이스가 호출되지 않는다.

> ### 스프링이 제공하는 어드바이스
>> 모듈 본래 기능이 아닌 것을 모듈에서 분리해 어드바이스에 기술하는데, 어드바이스에는 종류가 있다.

<table>
	<tr>
		<th>어드바이스의 형태</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>Before</td>
		<td>조인포인트 앞에서 실행할 어드바이스</td>
	</tr>
	<tr>
		<td>After</td>
		<td>조인포인트 뒤에서 실행할 어드바이스</td>
	</tr>
	<tr>
		<td>AfterReturning</td>
		<td>조인포인트가 완전히 정상 종료한 다음 실행되는 어드바이스</td>
	</tr>
	<tr>
		<td>Around</td>
		<td>조인포인트 앞뒤에서 실행되는 어드바이스</td>
	</tr>
	<tr>
		<td>AfterThrowing</td>
		<td>조인포인트에서 예외가 발생했을 때 실행되는 어드바이스</td>
	</tr>
</table>

>> 트랜잭션처럼 대표적인 어드바이스는 직접 만들 필요 없이 AOP 제품에서 이미 트랜잭션 처리를 구현해서 제공하주므로 이를 이용한다.

> ### 프락시를 이용한 AOP
>> AOP 구현 방법에는 몇 가지가 있지만, 스프링에서도 이용하는 proxy를 알아본다.  
{% include figure.html src="/assets/img/proxy-aop.png" figcaption="프락시 기반의 AOP 예" %}
먼저 RImpl 클래스의 어떤 메서드를 실행해도 어드바이스가 동작하게 설정됐다고 가정한다.  
　  
DIxAOP 컨테이너는 R 인터페이스를 구현한 프락시 클래스의 인스턴스를 자동으로 생성해서 Q 클래스의 R 인터페이스형 인스턴스 변수에 인젝션한다. Q 클래스는 R 인터페이스를 구현한 클래스의 인스턴스가 인젝션되므로 그 인스턴스가 진짜 RImpl 클래스의 인스턴스인지 눈치 채지 못한다. 자동 생성된 프락시 클래스의 인스턴스는 진짜 Rimpl 클래스로 구현된 메서드를 호출하게 구현돼 있고, 종류에 따라 RImpl 클래스의 메서드를 호출하기 전후에 어드바이스를 호출한다. 인터페이스를 구현하는 클래스의 인터페이스라면 무엇이든 인젝션할 수 잇다는 특성을 이용한 프락시를 이용해 AOP를 구현하는 것이다.  
　  
단순한 자바 프로그래밍으로 구성되므로 클래스 바이너리를 수정하는 다른 방식의 AOP와 비교하면, 메서드 이외의 필드 등 세밀한 AOP를 할 수 없고, 성능면에서 우수하지 않다. 다만 프락시 기반 AOP라도 트랜잭션 관리, 오브젝트 그룹, 시큐리티, 형의 재정의등 AOP로 구현되는 대부분의 기능을 수행할 수 있다.  
　  
인터페이스를 구현하지 않은 클래스에 AOP를 하는 경우에는, 기본 JDK Dynamic Proxy가 아닌 GGLib Proxy를 사용하고 있다.(원래 인터페이스가 없으면 JDK Proxxy는 이용할 수 없다.)

> ### AOP 사용법
>> AOP를 이용해 업무 처리를 분리하는 것은 소스 코드의 가독성이 현저하게 떨어지므로 해서는 안된다. 예를들어 소비세 처리를 AOP에서 해버리면 어떻게 소비세가 더해졌는지 확인하기 힘들어진다.  
　  
로그 출력이나 트랜잭션 처리 등은 **공통화**할 수 있는 처리이므로 분리해도 되는 처리이다. 지금까지 공통 라이브러리로 제공하면 된다고 여기던 처리가 AOP로 구현할 처리의 후보이다.  
　  
개발 프로젝트 내에서 공통화팀이나 기반팀에 속하는 사람들은 AOP를 사용하고, 업무 처리를 프로그래밍하는 사람들은 AOP를 사용하지 말아야 한다.

* * *
## 어노테이션을 이용한 AOP
> 먼저 AspectJ를 설치해야 한다.  
1. [AspectJ 다운로드 페이지](https://www.eclipse.org/aspectj/downloads.php#install)에서 원하는 버전을 다운받는다.
2. 다운받은 디렉토리에서 java -jar aspectj-x.x.x.jar를 입력해서 설치를 진행한다.
3. 설치가 완료되면 인텔리제이 프로젝트 스트럭쳐(ctrl + alt + shift + s)를 열어 global libaries > + java > aspectj가 설치된 경로(보통 c:\aspectjx.x\lib)의 asepectjrt.jar를 등록한다.
4. 마찬가지로 global libraries에 aspectweaver.jar를 등록한다.
5. 등록된 둘을 프로젝트의 모듈로 설정한다.

> Aspect에 @Component를 붙이고 Bean 정의 파일에 <aop:aspectj-autoproxy>만 추가하면 된다.  
MyFirstAspect.java
```
@Aspect
@Component
public class MyFirstAspect {
	...
}
```
applicationContext.xml
```
...
	<context:conponent-scan base-package="패키지" />
	<aop:aspectj-autoproxy />
...
```
@Before, @After, @AfterReturning, @Around, @AfterThrowing 어노테이션으로 어드바이스들을 설정할 수 있다.

> ### 포인트컷 기술 방법
>> ```
@Before("execution(* findPokemon(int))")
public void before() {
	...
}
```
어노테이션 괄호안에 **execution(* findPokemon(int))** 형식으로 기술한다. AspejctJ라는 아주 유명한 AOP 제품에서 사용하는 포인트컷 지정법을 스프링에 도입한 것이다. AspjectJ에서는 호출하는 족의 메서드나 클래스, 호출되는 쪽의 메서드나 클래스를 조건으로 지정할 수 있는 프리미티브 포인트컷이라고 불리는 포인트 컷이 있다.  
　  
먼저 execution에 한정해 포인트컷을 지정하는 방법을 익혀본다. execution에서는 **호출되는 쪽**의 메서드(생성자)를 조건으로 포인트컷을 기술한다. 와일드카드 방식으로 지정할 수 있다. 다음은 모두 HogeBean이라는 클래스의 exMethod 메서드를 지정하는 방법이다.
```
execution(public String com.starlight.service.business.HogeBean.exMethod())
　
execution(* com.starlight.service.business..*Bean.*(..))
　
execution(* *..*exMethod())
　 
execution(* *.. HogeBean.*())
　
execution(public String ex*(..))
　
execution(* com.starlight.service.business.*.*(..))
```
execution의 기본 구문은 다음과 같다(^는 공백을 표시)  
execution(메서드의 수식자^메서드의 반환값^패키지.클래스 or 인터페이스.메서드명(인수형|, 인수형...|)^throw^예외)  
* 메서드의 수식자나 throws^예외는 생략 가능
* 메서드의 반환값형, 패키지와 클래스명, 인터페이스명에는 와일드카드(*)를 이용 가능
* "*"는 "."(패키지 구분 문자)와 일치하지 않으므로 복수 패키지와 일치시키려면 ".."를 사용
* 메서드의 인수에 ".."를 기술하면 모든 인수와 일치시킬 수 있음  
　  
또한 포인트컷은 몇 가지 포인트컷을 조합해서 설정할 수도 있다. 조건의 지정에 논리 연산자를 이용할 수 있다. and, or, not은 AspectJ에서 이용할 수 없는 스프링 고유의 표기법이다.

<table>
	<tr>
		<th>논리 연산자</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>|| 또는 or</td>
		<td>논리합을 의미하는 논리 연산자 <br/>
			ex) execution(* *..AopExBean.exMethod()) or <br/>
			　　execution(* *..AopExBeanParent.exMethod()) <br/>
				AopExBean의 exMethod 또는 AopExBeanParent의 exMethod를 지정</td>
	</tr>
	<tr>
		<td>&& 또는 and</td>
		<td>논리곱을 의미하는 논리 연산자 <br/>
			ex) execution(* *AopExBean.exMethod()) && <br/>
			　　execution(* *..AopExBeanParent.exMethod()) <br/>
			AopExBean의 exMethod, 그리고 AopExBeanParent의 exMethod 메서드 지정</td>
	</tr>
	<tr>
		<td>! 또는 not</td>
		<td>부정을 의미하는 논리 연산자 <br/>
			ex) execution(* exMethod()) and not <br/>
			　　execution(* *..AopExBeanParent.*()) <br/>
			AopExBeanParent 외 클래스의 exMethod 지정</td>
	</tr>
</table>
>> &&를 사용하는 경우 Bean 정의 파일에서는 &amp;amp;&amp;amp;로 써야 하므로 and, or, not으로 통일하는 편이 바람직하다.

> ### 어노테이션으로 어드바이스 만들기
>> #### Before 어드바이스, After 어드바이스
메서드명은 임의이며 메서드의 인수는 없는 것이 기본이다. 메서드의 반환값은 void가 기본이다.
```
@Before(생략)
public void foo(JointPoint jp) {
	Signature sig = jp.getSignature();
	System.out.println("메서드 이름: " + sig.getName());
	Object[] objs = jp.getArgs();
	System.out.println("인수값: " + ojbs[0]);
}
```
위와 같은 방식으로 Before가 어느 메서드에서 실행된 어드바이스인지 확인할 수 있다.
#### AfterReturning 어드바이스
이름에서 보듯이 매서드가 반환한값을 가져올 수 있다.
```
@AfterReturning(value = "execution(* getMessage())", returning = "message")
public void afterReturningMessage(String message) {
	System.our.println(message);
}
```
#### Around 어드바이스
AOP의 대상이 되는 메서드의 호출을 여기서 직접 해줘야 한다는 점이 특이하다.
```
@Around("execution(* getMessage())")
public String aroundMessage(ProceedingJoinPoint pjp) throws Throwable {
	System.out.println("Around Before");
	String message = (String)pjp.proceed();
	System.out.println("Around After");
	return message;
}
```
Around 어드바이스를 사용하면 굳이 다른 어드바이스를 사용하지 않아도 된다. proceed 앞에 기술하면 Before가 되고, proceed를 try catch로 묶으면 AfterReturning과 AfterThrowing이 된다. 그러나 매번 Around를 사용하면 복잡해지므로 필요한 경우만 사용하도록 한다.  
　  
다른 두개의 Around 어드바이스가 하나의 메서드에 관심을 가지고 있어도(두 어드바이스에서 proceed를 하여도) 메서드가 두 번 실행되지 않고 한 번만 실행된다. 물론 두 어드바이스에 기술된 처리는 정상적으로 동작한다.  
#### AfterThrowing 어드바이스
예외가 발생했을 때만 동작하는 어드바이스이다.  
　  
AOP로 예외 처리를 하게 되면 깔끔한 소스 코드를 작성할 수 있다를 맹신해서는 안된다. AOP에는 업무 처리를 기술하지 않는 것이 원칙이기 때문이다. 업무에 관련된 예외는 해당 클래스에 작성하는 것이 맞다.  
　  
규모가 큰 개발에서는 AOP를 기반팀에서 구현하는 등의 역할 분담이 발생하는데, 기반팀은 업무에 대해 상세하게 알지 못할 수 있다. 따라서 업무에 필요한 예외처리를 기반팀은 어떤 예외 처리를 써야 할지도 모른다.

<table>
	<tr>
		<th>어노테이션</th>
		<td>@AfterThrowing(value="PrimitivePointCut", throwing="예외의 변수명")</td>
	</tr>
	<tr>
		<th>내용</th>
		<td>* 메서드명은 임의<br/>
			* 메서드의 인수에는 잡고 싶은 예외를 기술하고 그 변수명은 어노테이션의 throwing 속성으로 지정한 "예외의 변수명"과 같아야 함<br/>
			* 메서드의 반환값은 void가 기본
		</td>
	</tr>
	<tr>
		<th>예제</th>
		<td>@AfterThrowing(value="execution(* getMessage())", throwing="ex") { <br/>
		&emsp;System.out.println("Hello Thrwoing!"); <br/>
		}</td>
	</tr>
</table>

>> ```
@AfterThrowing(value="execution(* getMessage())",thrwoing="ex")
public void someTrouble(Throwable ex) {
	System.out.println("Throwable!");
}
　
@AfterThrowing(value="execution(* getMessage())", thrwoing="ex")
public void anyTrouble(SQLException ex) {
	System.out.println("SQLException");
}
　
@AfterThrowing(value="execution(* getMessage())", throwing="ex")
public void trouble(Exception ex) {
	System.out.println("Exception");
}
```
위와 같은 한 메서드에 대한 복수의 예외처리가 있을 때 어떻게 동작할까?
　  
정답은 SQLException이 발생했을 땐 세개 모두 동작하고, Exception이 발새앴을 땐 Throwable, Exception 두 개가 동작한다.

## JavaConfig를 이용한 AOP
> 어노태이션만 사용해서 AOP를 사용하면 Bean 정의 파일이 어지러워질 수 있다. 혼자서 하고 있다면 JavaConfig의 이용도 생각해보는 것이 좋다.  
　  
context 클래스에서 @EnableAspectJAutoProxy 어노테이션을 추가하면 된다. 이는 aop:aspectj-autoproxy와 같은 역할을 한다.


## Bean 정의 파일을 이용한 AOP
> @Component를 제거하려면 다음과 같이 기술한다.
```
　<bean id="myFirstAspect" class="패키지.MyFirstAspect" />
```
@After, @Before, @Around, @AfterThrowing을 제거하려면 다음과 같이 기술한다.
```
　<aop:config>
	<aop:aspect id="myAspect" ref="myFirstAspect">
		<aop:pointcut id="pc" expression="execution(* findProduct(String))" />
		<aop:before pointcut-ref="pc" method="before" />
		// 다른 어드바이스도 같은 형식이다
		...
　</aop:aspect>
```