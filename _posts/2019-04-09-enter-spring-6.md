---
title: 스프링 입문 6. 프레젠테이션 층의 설계와 구현
author: breadkey
categories: study
tags: spring
layout: post
---

## 스프링 MVC의 개요
### 스프링 MvC와 MVC2 패턴
J2EE 패턴의 FrontController 패턴을 기초로 하지만 클래스가 역할별로 분명하게 분리되고, 클래스간 관계가 느슨하다. 각 Model View Controller는 인터페이스가 정의되며 상호간 의존이 없다.
### 스프링 MVC와 어노테이션
스프링 1부터 포함됐지만 학습할 내용이 많았고 설정의 확장이 복잡해 주목받지 못했다. 하지만 스프링 2.5에서 어노테이션을 채용했을 때 부터 클래스에 @Controller 어노테이션만 선언하면 되고 실행 메서드의 매핑도 @RequestMapping 어노테이션만 설정해주면 되므로 매우 간단해졌다.

한 클래스에 요청을 처리하는 메서드를 몇 개라도 구현할 수 있고, 메서드 이름도 자유로우며 불필요한 인수도 필요 없다.

URL 일부를 인수로 정의할 수도 있다.
### 스프링 MVC와 REST
REST는 웹상의 정보를 하나하나 리소스로 파악하고 그 식별자로 URI를 할당해 정보를 고유하게 특정하는 웹 애플리케이션 개발 방법 중 하나이다.