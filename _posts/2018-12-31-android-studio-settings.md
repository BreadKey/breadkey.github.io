---
layout: post
author: breadkey
title: 안드로이드 스튜디오 환경설정
categories: study
tags: android
---
[SDK 도구 권장패키지](https://developer.android.com/studio/intro/update?hl=ko)
## 1. SDK Tools
> ### Android SDK Build-Tools
>> **필수**. 안드로이드 앱을 빌드하는 도구를 포함함

> ### Android SDK Platform-Tools
>> **필수**. [adb](https://developer.android.com/studio/command-line/adb?hl=ko)를 비롯하여 안드로이드 플랫폼에 필요한 다양한 도구를 포함함
>>> *안드로이드 플랫폼: 버전벌로 만들어진 실행환경. PC에서는 **에뮬레이터** 실제 단말에서는 **OS**라고 생각하면 된다.*{: style="font-size: 0.8em;"}

> ### ANdroid SDK Tools
>> **필수**. 안드로이 에뮬레이터, ProGuard와 같은 필수적인 도구를 포함함

> ### Android Support Repository
>> 권장. 지원 라이브러리를 위한 로컬 Maven 레포지토리를 포함하며, 대부분의 안드로이드 버전과 호환되는 확장 API 세트를 제공함. 웨어, TV, 구글 캐스트와 같은 제품에 필요

> ### Google Repository
>> 권장. Google 라이브러리를 위한 로컬 Maven 레포지토리를 포함하며, Firebase, 구글맵스, 게임 도전 과제 및 리더보드를 비롯한 다양한 기능과 서비스를 앱에 제공함

> ### Android SDK Platform
>> **필수**. 애플리케이션을 컴파일하려면 개발 환경에서 *최소한 하나 이상의 플랫폼*이 필요함. 최신 버전에서 앱을 빌드해야 앱이 최신 안드로이드 기기에서 실행될 때 새로운 기능을 사용할 수 있음

> ### Intel 또는 ARM 시스템 이미지
>> 권장. 에뮬레이터를 실행하려면 시스템 이미지가 필요한데, 각 플랫폼 버전은 지원되는 시스템 이미지를 포함함

> ### Google Play APK Expansion library
>> 최대 용량제한을 넘을 수 있게 하는데 도움을 줌

> ### Google Play Instant Development SDK
>> 설치없이 앱을 사용할 수 있게 도와줌

> ### Google Play Licensing library
>> 앱에 구글 라이센스 정책을 적용할 수 있도록 도와줌

> ### Google Play services
>> 구글 맵등 구글 서비스를 앱에서 사용할 수 있음

> ### Google USB Driver
>> 윈도우즈 전용을 구글 넥서스 기기에서 adb 디버깅을 하는데 반드시 필요함

> ### Google Web Driver
>> 웹앱을 테스트하기 위한 브라우저 자동화 도구

## 2. 액티비티
> ![](/assets/img/android-activities.PNG)
앱을 실행했을 떄 처음 보이는 화면의 모양

> ![](/assets/img/activity-name.PNG)
**Activity Name** :   
**Layout Name** :

