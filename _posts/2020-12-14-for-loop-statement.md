---
title: for문 조건문
author: breadkey
categories: bandage
layout: post
---

# Fault
```dart
String string = 'test';
final minLength = 6;
 
for (int _ = 0; _ < minLength - string.length; _++) {
  string = ' $string';
}
 
expect(string.length, 6);
```
## Result
```
Expected: <6>
  Actual: <5>
```
## Why?
의미를 생각하면 맞는 것 같으나, for 조건문이 ```true```인지 확인하기 위해 다시 한 번 조건문을 거쳐가므로 변경된 ```string.length```를 사용하게 된다.

# Correct
```dart
String string = 'test';
final minLength = 6;
final padding = minLength - string.length;
 
for (int _ = 0; _ < padding; _++) {
  string = ' $string';
}
 
expect(string.length, 6);
```