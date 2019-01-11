---
title: 자바로 배우는 자료구조와 알고리즘
author: breadkey
layout: post
categories: study
tags: [java, data-structure]
---
[참고서](https://book.naver.com/bookdb/book_detail.nhn?bid=13648903)
## 자바의 리스트가 두 종류인 이유
> 수행하는 동작에 따라 ArrayList, LinkedList의 속도, 메모리 사용량이 다르다.

## Comparable interface
> 특정 클래스 리스트를 만들고 클래스끼리 비교할 수 있게 하려면 Comparable<T> 인터페이스를 구현하면 된다. 예를들어 정수 리스트에 사용되는 java.lang.Integer의 소스를 보면 다음과 같다.
```
public final class Integer extends Number implements Comparable<Ineger> {
	...
	@Override
	public int compareTo(Integer anotherInteger) {
		int thisVal = this.value;
		int anotherVal = anotherInteger.value;
		return (thisVal < anotherVal? -1 : (thisVal == anotherVal? 0 : 1));
	}
	...
}
```

## List interface
> 이 인터페이스를 구현하려면 add, get, remove와 약 20가지 메서드를 작성하여야 한다. 이렇게 구현된 List는 다른 구현된 List들과 상호교환(LinkedList와 ArrayList의 교환 등)할 수 있다. 따라서 필요한 경우가 아니면 인스턴스의 타입은 List로 정의해 놓는 것이 나중에 필요에 따라 바꾸게 될 때 용이하다.

## 알고리즘 분석
> 수행하는 동작에 따라 구현된 리스트의 속도, 메모리가 다르다고 하였는데, 어떤 프로그램에 어떤 리스트가 좋을지 확인하는 방법중 하나는 둘 다 시도해보고 걸린 시간을 확인하는 것이다. 이러한 접근법을 프로파일링이라고 하는데, 이는 몇가지 문제가 존재한다.
1. 알고리즘을 비교하려면 그것을 모두 구현해봐야 한다.
2. 결과는 사용하는 컴퓨터의 성능에 의존하므로 다른 컴퓨터에선 다른 알고리즘이 더 좋을 수도 있다.
3. 결과는 데이터에 의존하기도 한다.

> 


- 작성중
