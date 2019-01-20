---
title: 스프링 입문 2. 스프링 DI
author: breadkey
categories: study
tags: spring
layout: post
---
## DI란?
> 인터페이스를 이용해 컴포넌트화를 실현하는 것이다. DI를 직역하면 의존관계 주입으로, 쉽게 말해 오브젝트 사이의 의존 관계를 만드는 것이다. 오브젝트의 어떤 프로퍼티에 그 오브젝트가 이용할 오브젝트를 설정한다는 의미이다.  
　  
DI를 구현하는 컨테이너는 의존성 주입 밖에도 클래스의 인스턴스화 등의 생명 주기 관리 기능이 있는 경우가 많다.  
　  
인터페이스를 사용하지 않는 간단한 예를 들어 설명하면, 포켓몬 정보를 가져오고 갱신하는 등 관리할 수 있고 포켓몬 [종족값](http://ko.pokemon.wikia.com/wiki/%EC%A2%85%EC%A1%B1%EA%B0%92)의 총합을 계산해주는 포켓몬 도감 웹 애플리케이션을 생각해보자.  
[소스코드](https://github.com/study-return-study/spring-study/tree/study-di/src/stduy_di/breadkey_pokedex)

{% include figure.html src="/assets/img/sample_pokedex_design.png" figcaption="최초의 Pokedex 애플리케이션" %}
> PokedexRun이 뷰와 컨트롤러를 대신한다. PokedexService는 비즈니스 로직을 실현한다. PokemonDao는 데이터베이스 액세스를 한다.(데이터베이스 액세스 오브젝트는 뒤에 Dao라고 붙인다.)

{% include figure.html src="/assets/img/sample_pokdex_with_di_design.png" figcaption="DI를 사용한 Pokedex 애플리케이션" %}
> 이어서 DI 컨테이너를 이용하는 예이다. DI 컨테이너를 이용하면 PokdexRun이 이용하는 PokedexService의 인스턴스, 그리고 PokdexService가 이용하는 PokemonDao의 인스턴스를 DI가 생성해준다. 그리고 ProductDao의 인스턴스를 이용하는 ProductService에 관계를 인젝선해준다.  
　  
main 메서드가 있는 PokedexRun은 DI 컨테이너의 생성이나 PokdexService의 인스턴스 취득 등을 해야 하지만, 이러한 작업은 실제 코드에선 배제된다.  
　  
DI 컨테이너가 다루는 PokedexService 클래스, PokedexDao 클래스에는 DI를 위한 특수한 메커니즘(어노테이션이나 Bean 정의파일 이용)이 필요하다. 이 메커니즘을 따르면 인스턴스를 생성할 클래스, 전달받을 클래스 모두 POJO(Plain Old Java Object)로 작성해도 문제가 없다.  
　  
DI를 통해 new 연산자가 사라지는데, 이를 통해 구현 비의존 컴포넌트화를 구현할 수 있게 된다. 구현에 의존하지 말아야하므로 위와같은 설계는 원칙적으로 잘못됐다. 인터페이스기반 컴포넌트화를 하려면 PokdexService와 PokedexDao는 인터페이스로 정의하고, 그 구현 클래스는 인터페이스 뒤에 Impl을 덧붙인 것으로 한다.

{% include figure.html src="/assets/img/sample_pokdex_with_di_design2.png" figcaption="Service와 DAO가 컴포넌트화된 Pokedex 애플리케이션" %}

> DI 컨테이너에 담긴 클래스의 인스턴스화는 (디폴트) 1회만 실행된다. 이로인해 Singleton으로 만들고 싶은 컴포넌트를 Singleton으로 만들지 않아도 간단히 실현되게 해준다.  
　  
인터페이스와 DI 컨테이너를 이용함으로써 부품화의 이점을 누릴 수 있으며, 개발 효율 상승, 변경 확장 용이, 고품질의 애플리케이션이 된다.

* * *
## DI를 사용할 곳
> DI는 데이터베이스에서 값을 가지고와서 인스턴스화하는 작업에 어울리지 않는다. 컨트롤러 - 서비스 - DAO의 의존 관계를 구축할 떄 어울린다. 서비스 - 도메인, DAO - 도메인 의존 관계의 구축에는 어울리지 않는다.  
　  
모든 클래스가 부품이 될 리는 없으므로 아무 클래스에나 인터페이스를 붙이지 않도록 한다.

* * *
## 어노테이션을 이용한 DI
> 크게 XML로 작성된 Bean 정의 파일을 이용한 DI, 어노테이션을 이용한 DI, 자바 프로그램에 의한 DI가 있다. 간단하게 DI를 이용할 수 있는 어노테이션으로 구현할 수 있는 방법을 알아보자.

> ### @Autowired와 @Component
>> 인스턴스 변수 앞에 @Autowired를 붙이면 DI 컨테이너가 그 인스턴스 변수의 형에 대입할 수 있는 클래스를 @Component가 붙은 클래스 중에서 찾아내 인젝션해준다.(Bean 정의에서 클래스를 스캔할 범위를 정해야 한다.)  
　  
접근자가 private(private PokdemonDao pokemonDao)더라도 인젝션할 수 있다. 클래스가 여러 개 있어도 형이 다르면 인젝션 되지 않는데, 이렇게 형을 보고 인젝션하는 방법을 byType이라도 한다.  
　  
클래스를 만든 것만으로는 DI를 할 수 없다. XML로 기술된 Bean 정의파일을 만들어야 의존 관계를 주입할 수 있다.

#### applicationContext.xml  
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
    <context:annotation-config/>
    <context:component-scan base-package="di_annotation.pokedex.di.business"/>
    <context:component-scan base-package="di_annotation.pokedex.di.dataaccess"/>
</beans>
```
>> XML의 스키마로서 bean과 context가 선언되었다. 주요 스키마는 다음과 같다.

<table style="width: 100%; font-size: small;">
	<tr>
		<th>명칭</th>
		<th>스키마 파일</th>
		<th>URL</th>
		<th>해설</th>
	</tr>
	<tr>
		<td>bean 스키마</td>
		<td>spring-beans.xsd</td>
		<td>http://www.springframework.org/schema/beans</td>
		<td>Bean(컴포넌트) 설정</td>
	</tr>
	<tr>
		<td>context 스키마</td>
		<td>spring-context.xsd</td>
		<td>http://www.springframework.org/schema/context</td>
		<td>Bean(컴포넌트) 검색과 어노테이션 설정</td>
	</tr>
	<tr>
		<td>util 스키마</td>
		<td>spring-util.xsd</td>
		<td>http://www.springframwork.org/schema/util</td>
		<td>정의와 프로퍼티 파일을 불러오는 등의 유틸리티 기능 설정</td>
	</tr>
	<tr>
		<td>jee 스키마</td>
		<td>spring-jee.xsd</td>
		<td>http://www.springframwork.org/schema/jee</td>
		<td>JNDI의 lookup 및 EJB의 lookup 설정</td>
	</tr>
	<tr>
		<td>lang 스키마</td>
		<td>spring-lang.xsd</td>
		<td>http://www.springframwork.org/schema/lang</td>
		<td>스크립트 언어를 이용할 경우의 설정</td>
	</tr>
	<tr>
		<td>aop 스키마</td>
		<td>spring-aop.xsd</td>
		<td>http://www.springframwork.org/schema/aop</td>
		<td>AOP 설정</td>
	</tr>
	<tr>
		<td>tx 스키마</td>
		<td>spring-tx.xsd</td>
		<td>http://www.springframwork.org/schema/tx</td>
		<td>트랜잭션 설정</td>
	</tr>
	<tr>
		<td>mvc 스키마</td>
		<td>spring-mvc.xsd</td>
		<td>http://www.springframwork.org/schema/mvc</td>
		<td>Spring MVC 설정</td>
	</tr>
</table>

>> 스키마에 이어서 기술되는 <context:~ 태그는 @Autowired와 @Component를 구현하기 위한 태그이다.

<table>
	<tr>
		<th style="width: 35%">태그</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>< context:annotation-config /></td>
		<td>@Autowired, @Resource를 이용할 떄의 선언이다. <br/>
		다음에 설명할 context:component-scan이나 스프링 MVC에서 설명할 mvc:annotation-driven이 Bean 정의 파일에 기술됐으면 생략할 수도 있다.</td>
	</tr>
	<tr>
		<td>< context:component-scan base-package="패키지명" /></td>
		<td>
			@Component, @Service 등의 어노테이션이 설정된 클래스를 읽어 들여 DI 컨테이너에 등록하고 base-pakage 속성으로 지정한 패키지 아래의 컴포넌트를 검색한다. import처럼 *를 이용할 수 있다.<br/>
			또한, 개발자들이 실수로 틀린 패키지를 만들거나, 틀린 이름을 컴포넌트로 등록하더라도 사용할 수 있게 하기 위해서 context:exclude-filter나 use-default-fileters="false", context:include-filter를 조합해 컴포넌트를 한정할 수도 있다.
		</td>
	</tr>
</table>

> ### @Autowired와 @Component를 동작시켜보자  

```
package di_annotation.pokedex;

import di_annotation.pokedex.di.business.domain.Pokemon;
import di_annotation.pokedex.di.business.service.PokedexService;
import org.springframework.beans.factory.BeanFactory;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class PokedexRun {
    public static void main(String[] args) {
        PokedexRun run = new PokedexRun();
        run.execute();
    }

    @SuppressWarnings("res")
    public void execute() {
        BeanFactory context = new ClassPathXmlApplicationContext("/di_annotation/pokedex/config/applicationContext.xml");
        PokedexService pokedexService = context.getBean(PokedexService.class);
        Pokemon pikachu = pokedexService.findByPokemonId(25);
        int totalBaseStats = pokedexService.calculateTotalBaseStats(pikachu);

        System.out.println(pikachu.getName() + "의 종족값 총합은 " + totalBaseStats + "입니다.");
    }
}
```

> ### AutoWired
>> 인젝션을 받기 위한 설정이다. 인스턴스 변수 앞에 붙이는 것 뿐만 아니라 메서드 선언 앞에도 붙일 수 있다.  
```
@Autowired
public void setFoo(Foo foo, Bar bar) {
	this.foo = foo;
	tihs.bar = bar;
}
```  
이렇게 함으로써 Foo, Bar 인스턴스 둘다 인젝션할 수 있다. 마찬가지로 생성자에도 이용할 수 있다.  
　  
인젝션할 수 있는 형이 2개 존재한다면 바로 에러가 발생한다.  
　  
이렇게하면 테스트용 클래스 등 다른 클래스로 바꿀 경우에 불편한데, 다음과 같이 해결할 수 있다.  
　  
첫 번쨰는, 디폴트 Bean을 설정하는 @Primary를 @Beean이나 @Component에 부여하는 방법이다.(정의 파일에는 < bean primary="true">) 가장 간단한 방법이다.
```
@Component
@Pirmary
public class ProductDamImpl implements ProductDao...
```  
두 번째는 @Autowired와 병행해서 @Qualifier를 하는 방법이다. 단, 이 경우는 @Component에도 이름을 지정해야 한다. 이렇게 이름을 통해 인젝션 하는 방법을 byName이라고 한다. 같은 이름이 붙은 클래스가 중복되면 오류가 발생한다.
```
@Autowired
@Qualifier("productDao")
private ProductDao productDao;
```  
　  
```
@Component("productDao")
public class ProductDaoImpl implements ProductDao...
```
마지막 방법은 Bean 정의 파일에서 context:component-scan을 이용하는 것이다. 어떤 컴포넌트를 테스트용으로 바꾸고자 할 때는 그 컴포넌트 부분만 테스트용 부품을 스캔하도록 수정하는 방법이다.  
　  
마지막 방법이 불필요한 컴파일이나 수정 실수에 의한 버그도 막는 방법이다. Mock을 사용시는 다음과같이 할 수 있다.

```
// Mocdk 클래스에는 어노테이션을을 설정하지 않는다.
public class MockProductDao implements ProductDao {

}
```  
```
<context:component-scan base-package="package">
	// include-filter로 스캔할 대상으로 지정하고 type으로 필터링에 사용될 타입을 지정한다.
	<context:include-filter tpye="regex" expression="package.di.dataaccess.Mock.*"/>
	// exclude-filter로 스캔에서 제외할 대상을 지정하고 type으로 필터링에 사용될 타입을 지정한다. 여기선 어노테이션 중에서 Repository 어노테이션이 붙은 클래스를 스캔에서 제외한다.
	<context:exclude-filter type="annotation" expression="org.springframework.stereotype.Repository"/>
</context:component-scan>
```
>> @Resource 어노테이션을 사용해도 @Autowired와 똑같은 일을 할 수 있다. 그러나 @Resource는 JNDI를 이용한 DataSource 등의 인젝션을 위한 사양이고 변경될 가능성도 있다.

> ### @Component
>> 주로 인젝션을 위한 인스턴스를 설정하는 것이다. 클래스 앞에 @Component를 붙이면 Di 컨테이너가 찾아서 관리하고 @Autowired가 붙은 인스턴스 변수나 매서드에 인젝션해준다.  
　  
@Component에서 확장된 어노테이션은 다음과 같다.
<table>
	<tr>
		<th>어노테이션</th>
		<th>설명</th>
	</tr>
	<tr>
		<td>@Controller</td>
		<td>* 프레젠테이션 층 스프링 MVC용 어노테이션</td>
	</tr>
	<tr>
		<td>@Service</td>
		<td>* 비즈니스 로직 층 Service용 어노테이션으로 @Component와 동일 <br/>
		* 이 어노테이션으로 트랜잭션 관리를 할 수 있다는 소문이 있지만 아직 실현되지 않음</td>
	</tr>
	<tr>
		<td>@Repository</td>
		<td>* 데이터 액세스 층의 DAO용 어노테이션<br/>
		* 데이터 액세스에 관련된 예외를 모드 DataAccessException으로 변환</td>
	</tr>
	<tr>
		<td>@Configuration</td>
		<td>* Bean 정의를 자바 프로그램에서 실행하는 JavaConfig용 어노테이션</td>
	</tr>
</table>

>> 클래스가 어느 레이어에 배치될지 고려해서 확장 어노테이션을 사용하면 좋다.  
　  
>> 컴포넌트와 함꼐 이용하는 어노테이션의 하나로 @Scope가 있다. @Scope 뒤에 Value 속성을 지정하면 인스턴스화와 소멸을 제어할 수 있다.

<table>
	<tr>
		<th>Value 속성</th>
		<th>설명</th>
		<th>상수</th>
	</tr>
	<tr>
		<td>singleton</td>
		<td>인스턴스를 싱글턴으로 함<br/>
		Scope를 지정하지 않으면 Component는 자동 싱글턴</td>
		<td>BeanDefinition.SCOPE_SINGLETON</td>
	</tr>
	<tr>
		<td>prototype</td>
		<td>이용할 때마다 인스턴스화함</td>
		<td>BeanDefinition.SCOPE_PROTOTYPE</td>
	</tr>
	<tr>
		<td>request</td>
		<td>Servlet API의 request 스코프인 동안만 인스턴스가 생존함</td>
		<td>WebApplicationContext.SCOPE_REQUEST</td>
	</tr>
	<tr>
		<td>session</td>
		<td>Servlet API의 session 스코프인 동안만 인스턴스가 생존함</td>
		<td>WebApplicationContext.SCOPE_SESSION</td>
	</tr>
	<tr>
		<td>application</td>
		<td>Servlet API의 application 스코프인 동안만 인스턴스가 생존함</td>
		<td>WebApplicationContext.SCOPE_APPLICATION</td>
	</tr>
</table>

>>  servlet API에 대응하는 스코프를 지정하려면 RequestContextListener나 RequestContextFilter를 웹 애플리케이션에 설정해야 한다. 스프링 MVC의 DispatcherServlet을 도입하고있으면 설정할 필요가 없다.  
　  
스코프가 긴 Bean을 인젝션하는 경우 Scoped Proxy 설정이 필수이다. 사용자 A의 세션을 관리하는 Bean이 한 번 싱글턴으로 관리되고 있는 인스턴스에 인젝션된다면, 그 인스턴스가 종료될 때까지 A의 Vean이 설정된 채 남아있고, 사용자 B나 C의 Bean으로 치환하지 않는다.


```
<bean id="xxx" class="xxx" scope="session">
    <aop:scoped-proxy /> // Scoped Porxy를 설정한다
</bean>
```  
>> 어노테이션을 설정하는 경우

```
@Scope(
	value = WebApplicationContext.SESSION_SCOPE),
	proxyMode = ScopedProxyMode.TARGET_CLASS)
```
>> component-scan으로 설정할 수도 있다.

```
<context:component-scan base-package="xxx" scoped-proxy="targetClass"/>
```

>> 이 밖에도 @Lazy가 존재하는데, 인스턴스의 생성을 지연시키는 어노테이션이다. @Lazy가 없으면 DI컨테이너가 시작될 때 @Component가 붙은 클래스들을 전부 한 번에 인스턴스화한다. 테스트 단계에서 효과적인 어노테이션이다.

> ### 생명주기 관리
>> 인스턴스 생성과 소멸 타이밍에 호출되는 메서드를 설정하기 위해 @PostConstruct와 @PreDestory라는 2개의 어노테이션이 있다. 이 두 어노테이션은 Java SE6에서 준비된 어노테이션이다.  
　  
@PostConstruct는 DI 컨테이너에 의해 인스턴스 변수에 무언가 인젝션된 다음에 호출된다.(생성자에서 초기 처리를 하는 것과 비슷하다.)  
　  
자바에는 소멸자가 없으므로 종료 처리를 하고 싶다면 @PreDestory를 사용해야한다.

> ### 리플렉션 문제
>> 리플렉션에 의한 성능 문제보다 데이터베이스 쪽이 문제가 더 크다.

* * *
## Bean 정의 파일을 이용한 DI
> 어노테이션을 활용한 DI는 매우 편리해서 소규모 개발에 자주 사용되지만, 대규모 개발에는 수많은 프로그래머들이 사용한 어노테이션을 관리하기란 쉽지 않다. 따라서 대규모 개발에서는 아키텍트팀이나 기반팀이 Bean 정의 파일을 이용해서 DI를 관리하는 경우가 많다.  
　  
다른 프레임워크에서 제공하고 있는 클래스를 DI 컨테이너에서 관리하는 경우, 기능 확장아니 변경을 설정 파일에서 할 필요성이 있는 경우(은행 송금 지급을 신용카드 지급으로 변경하는 경우) 등은 어노테이션을 사용한 DI와 Bean 정의 파일에 의한 DI를 겸용하는 방법도 자주 사용된다.
> ### BeanFactory
>> DI 컨테이너의 핵심은 BeanFactory이다. 실행 시 건네지는 Bean 정의 파일(기본적으로 applicationContext.xml)을 바탕으로 인스턴스를 생성하고 인스턴스의 인젝션을 처리한다.  
　  
개발자가 BeanFactory를 직접 이용할 일은 별로 없지만, DI 컨테이너로부터 인스턴스를 얻는다는 말은 구체적으로 BeanFactory로부터 인스턴스를 얻는다는 것이다.

> ### Bean 정의 파일
>> XML 파일에 기술하는 것이 일반적이다.

config/applicationContext.xml
```
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="pokedexService"
          class="di_bean.pokedex.di.business.service.PokedexServiceImpl"
          autowire="byType" />
    <bean id="pokedexDao"
          class="di_bean.pokedex.di.dataaccess.PokemonDaoImpl"/>
</beans>
```
>> 어노테이션을 삭제하는 대신 정의 파일에 인스턴스화할 오브젝트를 bean 태그에 정의한다. 또한 인스턴스화된 오브젝트를 사용하는 쪽은 Setter 메소드가 필요해진다.
```
public void setPokemonDao(PokemonDao pokemonDao) {
	this.pokemonDao = pokemonDao;
}
```  

>> bean 태그의 기본적인 사용 방법은 다음과 같다

```
<beans>
	...
	<!-- 클래스 Y에 클래스 X를 Autowired로 인젝션 -->
	<bean id="x" class="package.X" />
	<bean id="y" class="package.Y" autowire="byType" />

	<!-- 클래스 B에 클래스 A를 명시적으로 인젝션 -->
	<bean id="a" class="package.A" />
	<bean id="b" class="package.B">
		<property name="인스턴스 변수명" ref="a" />
	</bean>

	<!-- 프로퍼티에 문자열과 스칼라값을 설정 -->
	<bean id="c" class="package.C">
		<property name="인스턴스 변수명" value="Hello" />
		<property name="인스턴스 변수명" value="109" />
	</bean>
</beans>
```

> ### bean 태그
>> @Autowired, @Scope, @Lazy 등을 Bean 정의 파일 설정만으로 구현할 수 있다.

<table>
	<tr>
		<th>속성</th>
		<th colspan="2">의미</th>
	</tr>
	<tr>
		<td>id</td>
		<td colspan="2">오브젝트를 유일하게 하는 ID</td>
	</tr>
	<tr>
		<td>name</td>
		<td colspan="2">오브젝트명을 정의 <br/>
			오브젝트에 여러 이름을 설정하고 싶을 떄나 ID에는 설정할 수 없는 이름을 지정할 때 이용 <br/>
			ex) name="/showEmployee" name="bean1, bean2"</td>
	</tr>
	<tr>
		<td>class</td>
		<td colspan="2">id의 실체. 패캐지명과 클래스명으로 구성</td>
	</tr>
	<tr>
		<td>scope</td>
		<td colspan="2">오브젝트의 스코프를 지정</td>
	</tr>
	<tr>
		<td>parent</td>
		<td colspan="2">설정을 물려받을 오브젝트명을 지정</td>
	</tr>
	<tr>
		<td rowspan="2">abstract</td>
		<td>true</td>
		<td>인스턴스를 만들지 않고 공통 설정을 정의해두고</td>
	</tr>
	<tr>
		<td>false</td>
		<td>생략 시 기본값. 인스턴스를 만들고 싶을 떄 지정</td>
	</tr>
	<tr>
		<td rowspan="2">singleton</td>
		<td>true</td>
		<td>생략 시 기본값. getBean 메서드로 얻는 인스턴스는 싱글턴</td>
	</tr>
	<tr>
		<td>false</td>
		<td>getBean 메서드로 얻는 인스턴스는 매번 인스턴스화된 것</td>
	</tr>
	<tr>
		<td rowspan="2">lazy-init</td>
		<td>true</td>
		<td>인스턴스 생성을 지연시킴</td>
	</tr>
	<tr>
		<td>false</td>
		<td>생략 시 기본값. BeanFactory 시작 시 인스턴스를 생성</td>
	</tr>
	<tr>
		<td rowspan="4">autowire</td>
		<td>no</td>
		<td>생략 시 기본값. property 태그에는 ref 태그로 지정된 오브젝트가 인스턴스 변수로 설정됨</td>
	</tr>
	<tr>
		<td>byName</td>
		<td>프로퍼티명과 일치하는 오브젝트명의 Bean이 자동으로 인젝션됨<br/>
			ex) setEmployee 메서드가 있으면 -> id 혹은 이름이 employee인 오브젝트가 인젝션됨</td>
	</tr>
	<tr>
		<td>byType</td>
		<td>프로퍼티형과 일치하는 Bean이 인젝션됨<br/>
			ex) setEmployee(Employee emp) 메서드가 있으면 -> 형이 Employee인 오브젝트가 인젝션됨</td>
	</tr>
	<tr>
		<td>constructor</td>
		<td>생성자를 이용해 인젝션</td>
	</tr>
	<tr>
		<td>depend-on</td>
		<td colspan="2">의존 관계의 대상이 되는 오브젝트가 있는지 검사</td>
	</tr>
	<tr>
		<td>init-method</td>
		<td colspan="2">메서드명을 기술함으로써 인스턴스 변수의 설정 후에 호출됨<br/>
			여기서 지정하는 메서드에 인수가 없고 반환값이 void 형이 됨</td>
	</tr>
	<tr>
		<td>destory-method</td>
		<td colspan="2">메서드명을 기술함으로써 시스템 종료 시 호출됨<br/>
			여기서 지정하는 메서드에는 인수가 없고 반환값이 void 형이 됨. 또한 메서드를 가진 오브젝트는 싱글턴</td>
	</tr>
</table>

> ### Bean 정의 파일 분할
>> Bean 정의 파일이 크면 읽기가 힘들다. 정의 파일을 분할할 수 있다.  
　  
분할한 Bean 정의 파일은 개별적으로 읽어와도 좋지만 import 태그를 이용해 읽어올 수도 있다.

> ### 프로퍼티 파일 이용
>> [소스코드](https://github.com/study-return-study/spring-study/tree/master/study_di/breadkey/src/di_bean/property_file)  
```
    <util:properties id="msgProperties"
                     location="di_bean/property_file/config/message.properties"/>
    <bean id="message" class="di_bean.property_file.MessageServiceImpl">
        <property name="message" value="#{msgProperties.message}"/>
    </bean>
```  
bean 정의 파일에 위와 같은 내용이 포함된다. 인스턴스화 할 오브젝트로 bean 태그를 통해 MessageServiceImpl을 정의하고 프로퍼티 파일 안에 있는 message 프로퍼티를 MessageServiceImpl에 인젝션한다.

* * *
## JavaConfig를 이용한 DI
> 자동으로 컴파일해주는 IDE를 이용해 혼자 프로그래밍하는 경우 편리하다. XML에 기술했던 정의를 자바 프로그램으로 작성할 수 있게 됐기 때문에 JavaConfig를 사용하는 경향이 강해질 것이다.  
　  
XML의 Bean 정의보다 뛰어난 점이 타입 세이프(프로퍼티명이나 클래스명이 틀렸을 경우 컴파일 에러를 내는 것)이다.  
　  
혼자 개발하는 경우 JavaConfig, 리더가 있어서 관리할 수 있는 소규모 개발이면 어노테이션을, 대규모 개발이면 Bean 정의 파일을 중심으로 한 일부 어노테이션을 이용하는 것을 추천한다. 트랜잭션은 Bean 정의 파일에 정의하고 DI와 AOP의 대상이 되는 컴포넌트의 정의는 어노테이션을 이용하는 조합도 괜찮다.  
　  
어느 방법으로라도 자주 문제가 되는 것이 트랜잭션 관리이다. 이 부분은 잘 관리된 설계서가 반드시 필요하다.

> ### 예제를 이용한 JavaConfig
>> bean 파일을 사용한 DI의 applicationContext.xml을 다음과 같이 JavaConfig로 변경할 수 있다.  
config/AppConfig.java
```
@Configuration
public class AppConfig {
    @Bean(autowire = Autowire.BY_TYPE)
    public PokemonService pokemonService() {
        return new PokemonServiceImpl();
    }
    　
    @Bean
    public PokemonDao pokemonDao() {
        return new PokeamonDaoImpl();
    }
}
```
사용은 다음처럼 사용한다.
PokedexRun.java
```
public class PokedexRun {
	...
	// main
	...
	　
	public vod execute() {
		ApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
		　
		PokedexService pokedexService = context.getBean(PokedexService.class);
		...
	}
}
```
component scan도 사용할 수 있다.
```
@Configuration
@ComponentScan("스캔 범위")
public class AppConfig { ... }
```
복수의 Bean을 import하 듯이 복수의 JavaConfig도 import할 수 있다.
```
@Configuration
@Import(콘피그1.class, 콘피그2.class)
public class AppConfig { ... }
```
생성자를 통한 인젝션도 가능하다. 이 방법은 Setter 인젝션을 이용할 경우에도 같은 방법으로 사용할 수 있다. 먼저 PokemonDao Bean은 다음과 같이 정의해 놓았다.  
/config/AppConfigDao.java
```
@Configuration
public class AppConfigDao {
	@Bean
	public PokemonDao pokemonDao() {
		return new PokemonDaoImpl;
	}
}
```
```
...
@Bean
public PokededxService pokedexService() {
	return new PokedexServiceImpl(pokemonDao);
}
```
여기서 **pokemonDao**는 3가지 방법으로 인젝션할 수 있다.
1. **@Bean 메서드를 인수로부터 취득**
```
@Import(AppConfigDao.class)
... {
	@Bean
	public PokemonService pokemonService(PokemonDao pokemonDao) {
		return new PokemonServiceImpl(pokemonDao);
	}
}
```
2. **@Bean 메서드를 불러들여서 취득**  
config/AppContextSetDaoByMethod.java
```
@Bean
public PokemonService pokemonService() {
	return new PokemonServiceImpl(pokemonDao());
}
　
@Bean
public BaseStatsCalculateService baseStatsCalculateService() {
	return new BaseStatsCalculateServiceImpl(pokemonDao());
}
```
- JavaConfig안에 @Bean pokemonDao() 메서드가 정의되어있어야한다.
- @Bean 메서드는 처음 호출되었을 떄 DI 컨테이너에 등록하고, 같은 호출이 있을 때 DI 컨테이너에서 반환해주므로 pokemonDao() 메서드는 한 번만 실행된다!(물론 스코프가 싱글턴)
3. **@Autowired 프로퍼티에서 취득**
```
@Import(AppConfigDao.class)
... {
	@Autowired
	private PokemonDao pokemonDao;
　
	@Bean
	public PokemonService pokemonService() {
		return new PokemonService(pokemonDao);
	}
}
```

> ### JavaConfig를 사용할 곳에 대해서
>> IDE를 써서 개발하는 경우 JavaConfig를 써는 것에 쉽게 이점을 느낄 수 있다.(xml에 익숙하지 않아서 그런지 제일 편한 것 같다.)  
　  
단 복수의 인원으로 진행할 때 인스턴스화되는 클래스를 다른 사람이 만들게 되면 JavaConfig에는 클래스가 없습니다라는 경고가 발생할 것이다. 특히 JavaConfig는 프로그램이므로 머릿속에서 정확하게 분리해두지 않으면, 무엇이 처리를 위한 클래스인지, 무엇이 설정을 기술한 클래스인지 혼란스러워질 가능성도 크다.

* * *
## ApplicationContext
> BeanFactory를 확장한 것이다. Bean 정의 파일 읽기, 메시지 소스, 이벤트 처리 등의 기능을 BeanFactory에 추가한 것이다.
> ### 웹 애플리케이션에서의 Bean 정의 파일 읽기
>> 웹 애플리케이션은 ContextLoaderListener 클래스나 ContextLoaderServlet 클래스에 의해 자동으로 ApplicationContext가 로드되므로 이를 이용하게 된다.  
　  
위에서 해오던 방식은 클라이언트가 요청할 때마다 매번 클래스 안에서 DI 컨테이너를 생성한다. ContextListener 클래스와 Bean 정의 파일을 다음과 같이 정의하여 매번 DI 컨테이너를 생성하지 않도록 한다.  
web.xml
```
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/bean 정의 파일.xml</param-value>
	</context-param>
	...
	<listener>
		<listener-class>
			org.springframework.web.context.ContextLoaderListener
		</listener-class>
	</listener>
	<!-- 리스너를 ContextLoaderServlet으로
	<servlet>
		<servlet-name>context</servlet-name>
		<servlet-class>org.springframework.web.context.ContextLoaderServlet</servlet-class>
		<load-on-startup>1</load-on-startup>
	</servlet>
	-->
```  
Bean 정의 파일이 여러 개일 경우 공백이나 ; 또는 ,로 구분한다.  
　  
클래스 경로상에 있을 떄는 classpath:설정 파일의 경로 형식 으로 읽어 들일 수 있다.  
　  
간단한 예제 애플리케이션을 만들고 싶을 떄는 해오던 것 처럼 해오면 된다. Bean 정의 파일이 여러 개일 때는 new ClassPathXmlApplicationContext("a.xml", "b.xml", "c.xml"); 이렇게 넘겨주면 된다.  
　  
POJO로 만든 클래스에서 사용하고 싶은 경우 @Autowired를 통해 ApplicationContext를 인젝션할 수 있다.

> ### 웹 애플리케이션에서 JavaConfig 읽어 들이기
>> ```
	...
	<context-param>
		// 명시적으로 지정해야함
		<param-name>contextClass</param-name>
		<param-value>
			org.springframework.web.context.support.AnnotationContextWebApplicationContext
		</param-value>
	</context-param>
	<context-param>
		// JavaConfig 지정
		<param-name>contextConfigLocation</param-name>
		<param-value>JavaConfig 클래스</param-value>
	</context-param>
	리스너 생략...
	<servlet>
		<servlet-name>sampleServlet</servlet-name>
		<servlet-class>
			org.springframwork.web.servlet.DispatcherServlet
		</servlet-class>
		<init-param>
			// 명시적으로 지정
			<param-name>contextClass</param-name>
			<param-value>
				org.springframwork.web.context.support.AnnotationConfigWebApplicationContext
			</param-value>
		</init-param>
	</servlet>
```

> ### 메시지 소스
>> ApplicationContext는 MessageSource 인터페이스를 구현하여 ApplicationContext가 다루는 특정 언어, 지역, 문화 환경에 의존하는 부분을 시스템에서 분리하도록 돼 있다.  
　  
메시지를 등록하려면 Bean 정의 파일에 메시지 소스 오브젝트를 등록하고, 메시지를 얻을 떄는 ApplicationContext의 getMessage() 메서드를 이용한다. 또는 MessageSource 형의 오브젝트를 @Autowired로 인젝션해두고 MessageSource#getMessage로 취득하는 방법도 있다. 메세지만을 사용하는 경우라면 이렇게 MessageSource를 인젝션 하는것이 목표를 명확하게 한다.

> ### 이벤트 처리
>> ApplicationContext는 다음 5가지의 이벤트를 발생시킨다.

<table>
	<tr>
		<th>이벤트명</th>
		<th>발생 시점</th>
	</tr>
	<tr>
		<td>ContextRefreshedEvent</td>
		<td>Bean 생명 주기의 초기화 상태 후 발생</td>
	</tr>
	<tr>
		<td>ContextStartedEvent</td>
		<td>ApplicationContext가 시작했을 때 발생</td>
	</tr>
	<tr>
		<td>ContextStoppedEvent</td>
		<td>ApplicationContext가 정지했을 때 발생</td>
	</tr>
	<tr>
		<td>ContextClosedEvent</td>
		<td>ApplicationContext의 close 메서드가 호출됐을 때 발생</td>
	</tr>
	<tr>
		<td>RequestHandledEvent</td>
		<td>웹 시스템 고유의 이벤트, HTTP 요청에 의해 서비스가 호출됐을 때 발생</td>
	</tr>
</table>

>> ApplicationListener 인터페이스를 구현한 클래스를 DI 컨테이너에 등록함으로써 받을 수 있다.

* * *
## 스프링 로깅
> Commons Logging으로 로그를 출력하며 Log4j 라이브러리가 있으면 Log4j를 사용할 수 있다. Log4j 라이브러리를 클래스 경로에 두고 /WEB-INF/log4j.xml을 배치한다.

* * *
## 스프링 유닛 테스트
> ```
// 스프링의 유닛 테스트를 한다는 선언
@RunWith(SpringJUnit4ClassRunner.class)
// 유닛테스트에 사용할 config 기술
@ContextConfiguration(locations = {"/di_bean/pokedex/config/applicationContext.xml"})
//테스트 대상 인터페이스
public class PokedexServiceTest {
    // 테스트에 사용할 구현 오브젝트 인젝션
    @Autowired
    PokedexService pokedexService;
    　  
    @Test
    public void findPokemonById() {
        Pokemon raichu = new Pokemon(
                26,
                "라이츄",
                60,
                90
                ,55,
                90,
                80,
                110
        );
        　  
        pokedexService.addPokemon(raichu);
        Pokemon foundPokemon = pokedexService.findByPokemonId(26);
        assertEquals(raichu, foundPokemon);
    }
}
```  
@Sql을 사용해서 데이터베이스의 테이블을 초기화할 수 있다.
　  
외부에 공개된 부품 단위로 타당한 범위안에서 테스트를 진행한다.

* * *
## Bean 정의 파일의 프로파일 기능
> Bean 정의 파일을 프로파일 형태로 그룹화해서 DI 컨테이너로 작성할 때 프로파일을 지정하는 것으로, 어떤 Bean 정의 파일을 유효화할 것인지를 지정하는 기능이다.
```
	<beans>
		<beans profile="test">
			...
		</beans>
		　  
		<beans proifle="production">
			...
		</beans>
	</beans>
```
이후 DI 컨테이너에 프로파일을 지정하면 된다.
### DI 컨테이너에 직접 지정하는 방법
>> ```
GenericXmlApplicationContext factory = new GenericXmlApplicationContext();
factory.getEnvorinment().setActiveProfiles("test");
factory.load("정의 파일");
factory.refresh();
```
setActiveProfiles 이름에서 보듯이 여러 개 지정할 수도 있다.

> ### SpringJUnity4ClassRunner 테스트 클래스를 지정하는 방법
>> @ActiveProfiles 어노테이션으로 설정 가능하다
```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("정의 파일")
@ActiveProfiles("test");
public class ...Test {
	...
}
```
마찬가지로 여러 개 지정할 수 있다.

> ### 웹 애플리케이션에서 지정하는 방법
>> ContextLoaderListener에서 작성되는 DI 컨테이너에서 지정하기
```
	<context-param>
		<param-name>spring.profiles.active</param-name>
		<param-value>production</param-value>
	</context-param>
```
쉼표로 여러개 지정할 수 있다.  
　  
DispatcherServlet으로 작성되는 DI 컨테이너에 프로파일 지정하기
```
	<servlet>
		...
		<init-param>
			<param-name>spring.profiles.active</param-name>
			<param-value>production</param-value>
		</init-param>
		...
	</servlet>
```

* * *
## 생명 주기
> JUnit으로 테스트를 하는 경ㅈ우에도, 엔터프라이즈 시스템에서 동작하는 경우에도 초기화(initialization) - 이용(use) - 종료(destruction)의 세 단계로 진행된다.  
　  
**Initialization**: 이용하기 위해서 애플리케이션을 생섬함, 시스템 리소스를 확보함  
**Use**: 애플리케이션에서 이용됨(애플리케이션의 99.9%가 이 단계)  
**Destruction**: 종료 처리. 시스템의 리소스를 돌려줌, 애플리케이션은 가비지 콜렉션의 대상이 됨  
　  
초기화는 애플리케이션을 실행한 순간부터 ApplicationContext의 인스턴스가 취득되기까지고, 그 후 Service 및 Dao의 동작은 이용 단계이다. 애플리케이션의 실행이 종료되기 직전의 짧은 순간이 종료에 해당한다.
### 초기화
>> 크게 Bean 정의 로드와 Bean 생성 및 초기화 두 가지 처리를 실행한다.  
　  
XML에 기술된 Bean 정의 파일과 어노테이션, JavaConfig에 기술된 Bean 정의를 BeanFactory 인터페이스를 확장한 ApplicationContext의 인스턴스(실제로는 구현 클래스인 XmlWebApplicationContext의 인스턴스)에 읽어 들이고, BeanFactoryPostProcessor의 인스턴스(구현 클래스인 PropertyPlacehlderConfigure 클래스의 인스턴스)가 ApplicationContext가 읽어 들인 Bean 정의 파일을 참조하면서 PropertyPlaceholderConfigurer 인스턴스면 프로파일을 읽어 들인다.  
　  
ApplicationContext 인스턴스는 Bean 생성 및 초기화를 하기 시작한다. 클래스를 인스턴스화하고 인스턴스를 다른 인스턴스에 인젝션한다. 계속해서 BeanPostProcessor 인스턴스를 이용해서 @PostConstruct 및 bean 태그의 init-method속성에 지정된 메서드를 호출해서 초기화를 실시한다.  
　  
이 과정이 완료되면 getBean 메서드 등을 이용할 수 있는 상태가 된다.

> ### 이용
>> getBean으로 Service 및 Dao 인스턴스를 취득해서 인스턴스의 매서드들을 사용하는 때를 말한다.

> ### 종료
>> @PreDestory 및 bean 태그의 destory-method 속성에 지정된 메서드를 불러들이고 종료를 처리한다.
