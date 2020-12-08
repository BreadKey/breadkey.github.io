---
layout: post
title: IntelliJ에서 Maven을 활용하여 Spring 개발환경 구축하기
author: breadkey
categories: study
tags: [java, intellij, maven, spring]
---

## Maven이란?
> 아파치 재단에서 개발하는 Java용 프로젝트 관리 도구이다. 팀의 의사소통 기능도 제공하며 빌드에 관한 기능들을 제공하여 빌드 도구도 된다. 또한 라이브러리 관리도 pom.xml 파일을 통해 할 수 있다.

* * *
## pom.xml
>> [참고문서](https://maven.apache.org/pom.html)  
POM은 Project Object Model의 약자이다. 구성 파일뿐 아니라 관련된 개발자와 역할, 조직 및 라이센스, 프로젝트가있는 위치의 URL, 프로젝트의 종속성 및 다른 모든 작은 부분까지 포함한다.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
 
  <!-- The Basics -->
  <groupId>...</groupId>
  <artifactId>...</artifactId>
  <version>...</version>
  <packaging>...</packaging>
  <dependencies>...</dependencies>
  <parent>...</parent>
  <dependencyManagement>...</dependencyManagement>
  <modules>...</modules>
  <properties>...</properties>
 
  <!-- Build Settings -->
  <build>...</build>
  <reporting>...</reporting>
 
  <!-- More Project Information -->
  <name>...</name>
  <description>...</description>
  <url>...</url>
  <inceptionYear>...</inceptionYear>
  <licenses>...</licenses>
  <organization>...</organization>
  <developers>...</developers>
  <contributors>...</contributors>
 
  <!-- Environment Settings -->
  <issueManagement>...</issueManagement>
  <ciManagement>...</ciManagement>
  <mailingLists>...</mailingLists>
  <scm>...</scm>
  <prerequisites>...</prerequisites>
  <repositories>...</repositories>
  <pluginRepositories>...</pluginRepositories>
  <distributionManagement>...</distributionManagement>
  <profiles>...</profiles>
</project>
```

> ### The Basics
>> #### groupId
프로젝트나 기관등에서 고유한 부분이다. 예를들어 Maven artifcats의 모든 코어는 groundId org.apache.maven 아래에 있다.
#### artifactId
대부분 프로젝트의 이름을 나타낸다. groupId와 함께한다. 만약 groubId가 org.codehaus.mojo이고 artifactId가 my-project라면 해당 프로젝트는 .../org/codehaus/mojo/my-project에 위치한다.
#### version
코드가 변하면 버전도 변하는데 이 버전에 대한 정보는 여기에 적는다.
#### packaging
패키징을 무엇으로 할지 기술한다. 기본값(적지 않았을 때)은 jar이다.
#### dependencies
대부분의 프로젝트는 다른 사람에게 의존하여 빌드되고 실행되는데, Maven을 통해 의존 목록을 관리할 수 있다. Maven은 dependency에 기술된 부분을 다운로드하고 링크시킨다.

* * *
## pom.xml에 스프링 개발환경 기술하기
> 먼저 인텔리제이에서 새로운 프로젝트를 Maven으로 생성한다.   
　  
Spring Data JPA을 활용하여 DAO를 자동으로 생성하고자 하는데, 이에 필요한 의존성은 다음과 같다.

```xml
    <dependencies>
        <!-- Spring -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-jpa</artifactId>
            <version>1.8.2.RELEASE</version>
        </dependency>

        <!-- Hibernate -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>4.3.11.Final</version>
        </dependency>

        <!-- DB -->
        <dependency>
            <groupId>commons-dbcp</groupId>
            <artifactId>commons-dbcp</artifactId>
            <version>1.4</version>
        </dependency>
        <dependency>
            <groupId>org.hsqldb</groupId>
            <artifactId>hsqldb</artifactId>
            <version>2.3.3</version>
        </dependency>

        <!-- Logging -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.16</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>1.7.5</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>1.7.5</version>
        </dependency>

        <!-- Testing -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
        </dependency>

        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${org.springframework-version}</version>
        </dependency>

    </dependencies>
```
> 스프링 제품들의 version은 ${org.springframework-version}이라고 돼있는데, 이는 properties에 정의한 값을 불러오는 것이다. properties에는 스프링 버전을 다음과 같이 정의했다.
```xml
    <properties>
        <org.springframework-version>4.1.6.RELEASE</org.springframework-version>
    </properties>
```
빌드 환경을 정의한다
```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
```