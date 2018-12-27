---
layout: post
title: Applying UML and Patterns Chapter.1
author: breadkey
categories: study
tags: object-oriented
---
## Object-Oriented Analyasis and Design
* * *

### 설계(Analysis), 분석(Design)이 무엇인가?
> **분석**은 솔루션이 아닌 문제와 요구사항에 대해 *조사*하는 과정이다. 분석은 광범위한 용어로 *요구사항 분석*(요구사항들에 대한 조사)또는 *객체 분석*(도메인 오브젝트에 대한 조사)를 의미한다.

> **설계**는 구현이 아닌 요구사항을 모두 만족시키는 *개념적인 솔루션*(conceptual solution)이다. 궁극적으로 설계는 구현될 수 있다.

* * *

### 객체지향 설계 분석이 무엇인가?
> **객체지향 분석**은 현실에 존재하는 오브젝트를 찾는 과정이다. 예를 들어 도서관 정보 시스템이 있다고 할 떄, 현실에 존재하는 오브젝트는 책, 도서관, 이용자 등이 있다.   
객체지향 분석은 도메인(소프트웨어를 통해 해결해야할 현실에 존재하는 문제의 영역)을 정의하면서 나온 오브젝트, 오브젝트의 속성, 오브젝트들 끼리의 관계를 나타낸 도메인 모델로 표현될 수 있다.

{% assign imgsrc = "https://upload.wikimedia.org/wikipedia/commons/2/2d/Domain_model.png" %}
{% assign imgtitle = "건강 보험 플랜의 샘플 도메인 모델" %}
{% assign figcaption = '도메인 모델의 예(출처: <a href="https://ko.wikipedia.org/wiki/%EB%8F%84%EB%A9%94%EC%9D%B8_%EB%AA%A8%EB%8D%B8">위키피디아</a>)' %}
{% include figure.html %}

> **객체지향 설계**는 분석을 통해 찾은 현실에 존재하는 오브젝트들을 토대로 소프트웨어내의 오브젝트를 찾고 이 오브젝트들이 요구사항을 모두 만족하기 위해 어떻게 관계를 가지고 협동해야하는지 정의하는 과정이다. 설계는 소프트웨어를 구현할 때 사용된다.   
객체지향 설계는 소프트웨어 오브젝트들과 그들의 관계를 나타낸 **인터랙션 다이어그램**과 **클래스 다이어그램**으로 표현될 수 있다.

{% assign imgsrc = "https://upload.wikimedia.org/wikipedia/commons/thumb/9/9b/CheckEmail.svg/510px-CheckEmail.svg.png" %}
{% assign imgtitle = "시퀀스 다이어그램" %}
{% assign figcaption = '인터랙션 다이어그램 중 하나인 시퀀스 다이어그램(출처: <a href="https://en.wikipedia.org/wiki/Sequence_diagram">위키피디아</a>)' %}
{% include figure.html %}

{% assign imgsrc = "https://upload.wikimedia.org/wikipedia/commons/thumb/d/d6/Uml_diagram2.png/630px-Uml_diagram2.png" %}
{% assign imgtitle = "클래스 다이어그램" %}
{% assign figcaption = '클래스 다이어그램의 예(출처: <a href="https://en.wikipedia.org/wiki/Class_diagram">위키피디아</a>)' %}
{% include figure.html %}

> 객체지향 설계 및 분석은 **요구사항 분석**과 매우 밀접한 관계를 가진다. 요구사항은 **유스 케이스**를 작성함으로써 분석될 수 있다.

* * *