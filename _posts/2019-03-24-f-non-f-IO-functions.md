---
title: A comparison of f-versions and non-f-versions of the I/O functions
author: breadkey
categories: study
tags: system-programming
layout: post
---

f-version 입출력 함수와 non-f-version 함수의 차이점은 다음과 같이 크게 2가지로 볼 수 있다.

## 1. 각 함수들을 정의하고 있는 라이브러리
### 1.1 C 표준 입출력 라이브러리
C 언어를 위한 표준 라이브러리 중 핵심 입출력 함수들을 정의하고 있는 라이브러리이다. C 언어를 위해 존재하므로 어느 환경, 어느 운영체제에서도 사용할 수 있다.  

f-version의 파일 입출력 함수들을 정의하고 있다.

파일의 입출력을 다룰 때 FILE * 즉 FILE Pointer를 통해 파일 입출력을 다룬다. high-level 파일 입출력이다.
### 1.2 C POSIX 파일 입출력 라이브러리
이식성 높은 UNIX 응용 프로그램을 개발하기 위해 정의한 공통 API인 POSIX([출처](https://ko.wikipedia.org/wiki/POSIX))에서 파일 입출력 함수들을 정의하고 있는 라이브러리이다. UNIX를 위해 존재하므로 UNIX계열이 아닌 운영체제, 즉 Windows 운영체제에서는 해당 라이브러리를 사용할 수 없다.   

non-f-version의 파일 입출력 함수들을 정의하고 있다.

Integer 자료형인 File Descriptor를 통해 파일 입출력을 다룬다. System Call을 사용하는 low-level 파일 입출력이다.  

여기있는 함수를 통해 표준 입출력의 함수들(f-version)을 구현할 수 있다.

## 2. 입출력을 다루는 매개체
### 2.1 FILE Pointer
f-version 함수에서 파일 입출력을 다루는 매개체로서 파일과 관련된 정보를 담고있는 FILE 구조체를 가리키고 있다.
{% include figure.html src="/assets/img/file_struct.png" figcaption="그림 1. FILE 구조체" %}

이 구조체가 실제로 파일과 관련된 정보를 담고 있는지 확인하기 위해 한 바이트씩 읽으면서 IO_read_ptr의 주소값을 확인해 보았다.
{% include figure.html src="/assets/img/read_1_byte.png" figcaption="그림 2. Hello World!가 담겨있는 파일을 한 바이트씩 읽기" %}
위에서 볼 수 있듯이 실제로 IO_read_ptr의 주소값도 1씩 증가함을 확인할 수 있다.  

#### 2.1.1 다른 의미의 File Pointer
파일 Stream에서 현재 Byte의 위치를 가리키는 포인터를 의미한다. 그림 2에서의 file pointer이다. 

파일의 한 바이트가 읽혔을 때, 이 포인터는 다음 바이트의 위치를 가리킨다. f-version 에서는 ftell을 통해 이 파일 포인터가 어디 위치해 있는지 알 수 있으며, non-f-version 에서는 lseek을 통해 파일 포인터의 위치를 지정함과 동시에 파일 포인터의 위치를 알 수 있다.
### 2.2 File Descriptor
non-f-version에서 파일을 다루는 매개체로서 특정한 파일에 접근하기 위한 추상적인 키이다([출처](https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%BC_%EC%84%9C%EC%88%A0%EC%9E%90))  
{% include figure.html src="/assets/img/fd_fpt.jpg" figcaption="그림 3. File Descriptor와 File Pointer가 가리키는 내용" %}

UNIX에서 stdin은 0, stdout은 1, stderr는 2로 File Descriptor를 지정해 놓았다. 실제로 stdin FILE구조체 포인터의 fileno 변수를 조회해보면 0이 들어가있다.