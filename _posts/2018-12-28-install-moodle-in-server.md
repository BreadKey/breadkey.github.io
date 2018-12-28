---
layout: post
title: 서버에 무들 설치하기
author: breadkey
categories: study
tags: [linux, docker, moodle]
---
[무들 설치 가이드](https://docs.moodle.org/36/en/Installation_quick_guide)

## 설치환경
> **CentosOS 7.6.1810** x86_64

* * *
## 환경설정
무들을 설치하기 위해선 웹 서버, 데이터베이스, PHP가 필요하다.
### 1. 데이터베이스 설치
<!--
MySQL은 Client와 Server로 나뉘는데, 이 둘은 다음과 같은 역할을 한다. 
> **Client**
: MySql 서버에 연결할 수 있도록 해준다. 클라이언트에서 명령어를 통해 MySQL 서버에 존재하는 데이터베이스를 사용할 수 있다.

> **Server**
: 서버에서 쿼리를 통해 데이터베이스를 관리할 수 있게 해준다. 

MySQL 패키지를 설치하면 클라이언트와 서버 모두 설치된다.
-->
나는 MySQL에서 파생되었고, MySQL과 호환되며 MySQL보다 더 많은 기능을 가지고 있는 **MariaDB**를 설치하고자 했다. [MariaDB와 MySQL 기능비교](https://mariadb.com/kb/ko/mariadb-vs-mysql-features/)   
<br>
```
# yum install mariadb-server
```
MariaDB를 설치하기 위해 위 명령어를 실행하니 다음과 같은 오류가 발생했다.
{% include figure.html src="/assets/img/mirror-error.PNG" %}
현재 yum의 베이스 레포지토리가 mirror.kakao.com인데, 이 레포지토리에 MariaDB 패키지가 존재하지 않아서 발생하는 문제이다. 따라서 MariaDB를 설치할 레포지토리를 등록했다.
```
# vi /etc/yum.repos.d/MariaDB.repo
```
```
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.4/centos7-amd64
gpgkey = https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck = 1
```
이후 다시 정상적인 설치 절차를 수행하였다.
> 1. mariadb를 설치한다.
```
# yum install mariadb-server
```  
2. 설치가 완료되면 다음 명령어를 통해 부팅 시 MariaDB가 시작할 수 있도록 하고 MariaDB를 시작한다.
```
# systemctl enable mariadb
# systemctl start mariadb
```
3. 설치가 제대로 됐는지 다음 명령어를 통해 확인한다
```
# systemctl status mariadb
```
4. 보안과 관련된 것들을 설정한다.
```
# mysql_secure_installation
```
5. 설정이 완료된 후 MariaDB에 정상적으로 연결되는지 확인한다.
```
# mysql -u root -p
```
{% include figure.html src='/assets/img/start-mariadb.PNG' figcaption='show databases 쿼리 결과' %}
### 2. PHP 설치
[무들 릴리즈 노트](https://docs.moodle.org/dev/Moodle_3.6_release_notes)에 따르면 PHP 버전은 7.0.0 이상이여야 하는데 7.3버전은 현재 적용이 불가능 하므로 PHP 7.2버전을 설치했다.

CentOS에 PHP7.x를 설치하기 위해서 EPEL 레포지토리와 Remi 레포지토리가 설치되있어야 한다.
> 1. EPEL 레포지토리 설치
```
# yum install epel-relase
# yum install yum-utils
```
2. Remin 레포지토리 설치
```
# yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
```
3. PHP 7.2 설치
	1. 먼저 다음 명령어를 통해 PHP 7.2 Remi 레포지토리를 활성화한다.
	```
	# yum config-manater --enable remi-php72
	```
	2. 활성화되면 PHP 7.2와 몇가지 모듈들을 설치한다.
	```
	# yum install php php-common php-opcache php-mcrypt php-cli php-gd php-curl php-mysqlnd
	```

### 3. 웹 서버 설치
현재 가장 많이 쓰이는 Apache를 설치하기로 했다.
```
# yum install httpd
```
PHP를 아파치에서 작동하게 하려면 단순히 아파치를 재시작하면 된다.
```
# systemctl restart httpd
```
{% include figure.html src='/assets/img/access-success.PNG' figcaption='80번 포트를 열어둔 상태라면 서버의 ip 주소로 접속했을 떄 위와 같은 페이지를 볼 수 있다.'  %}

* * *
## Docker를 사용해 Moodle 설치
### 레포지토리를 이용한 도커 설치   
[CentOS 도커 설치 가이드](https://docs.docker.com/install/linux/docker-ce/centos/)
> ### 1. 레포지토리 설정
>> #### 1. 필요한 패키지 설치
* yum-utils
* device-mapper-persistent-data
* lvm2

>> #### 2. 안정적인 레포지토리 설정
```
# yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```
#### 3. 옵션: edge와 test 레포지토리 활성화하기
```
# yum-config-manager --enable docker-ce-edge
# yum-config-manager --enable docker-ce-test
```
--disable를 통해 비활성화할 수 있다.

> ### 2. Docker CE 설치
>> #### 1. 최신버전으로 설치
```
# yum install docker-ce
```
>> #### 2. 특정버전으로 설치   
다음 명령어로 버전들을 확인한다.
```
$ yum list docker-ce --showduplicates | sort -r
```
{% include figure.html src='/assets/img/docker-available-versions.PNG' figcaption='현재 사용 가능한 버전들' %}
>> 다음 명령어로 원하는 버전을 설치한다.
```
# yum install docker-ce-<원하는 버전>
```

> ### 3. 도커 시작
```
# systemctl start docker
```
> ### 4. 제대로 설치됐는지 확인
```
# docker run hello-world
```
이 명령어는 테스트 이미지를 다운받고 컨테이너에서 실행시킨다.
{% include figure.html src='/assets/img/docker-hello-world.PNG' figcaption='정상적으로 설치됐다면 위와 같은 메세지를 확인할 수 있다' %}