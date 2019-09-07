---
title: 컴파일러 Lex
author: breadkey
categories: study
tags: compiler
layout: post
---
# Lex
Lexcial Analyzer는 소스 코드에서 lexeme을 골라내 이 들을 keyword, operator, identifier등으로 나눈 토큰으로 만든다.    
　  
Lecial Analyzer를 구현하는 방법은 Hand-written, regular expression으로 패턴을 기술하고 패턴대로 토큰을 만드는 Pattern-directed 방법이 있다.

Pattern-directed 방식으로 구현한 것이 lex이다.

## Lex의 구조
Lex는 다음 두가지를 포함한다.
* Lex compiler
* Lex language

Lex는 Lex 언어로 작성된 소스 코드(*.l)를 Lex 컴파일러 c코드(lex.yy.c)로 컴파일한다.

### Lex 소스 코드
definitions 
  변수, 상수, 정의 등을 선언한다  
%%  
rules    
  regular expression과 action으로 이루어진다  
%%  
auxiliary procedures  
  action에 필요한 procedure들로 이루어진다  

### 생성된 Lexical Analyzer
컴파일을 통해 lex.yy.c 라는 Lexcial Analyzer가 생성된다.
#### yylex()
lex.yy.c가 가지게 되는 스캐닝 루틴이다. 
* global input file(기본은 stdin)에서부터 토큰을 스캔한다.
* EOF를 만나거나 return을 수행할 때 까지 계속된다.
* return을 수행함으로써 종료된다면 스캐너는 다시 불러질 수 있으며 스캐닝을 재개할 수 있다.
* yylex()는 EOF에서 0을 반환하며 나머지 경우에는 action routine에 정의된 값을 반환한다. 