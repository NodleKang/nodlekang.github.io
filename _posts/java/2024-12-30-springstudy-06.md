---
layout: single
title: "[스프링스터디] 6장(작성중)"
date: 2024-12-30 18:00:00 +0900
categories: 
  - Spring
tag: 
  - Spring
  - Aspects
  - AOP
  - Annotation
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 "스프링스터디" 학습 모임에서 "6장. 스프링 AOP로 애스펙트 사용" 부분을 공부한 내용을 정리한 포스트입니다.

책을 읽기는 했지만, 내용을 이해하기에는 블로그들이 좀 더 좋아서 주로 블로그를 읽고 정리한 포스트입니다.

글의 전반적인 톤은 네이버 블로거 "메르"님이 글 쓰시는 톤을 따라 했습니다.

# AOP 란

AOP(Aspects Oriented Programming)는 관점 지향 프로그래밍이라 함.

어떤 로직을 기준으로 핵심 관심사(core concern)와 횡단 관심사(cross cutting concern)로 관점을 분리해서 보고, 모듈화하는 것임.

Aspects에 사전적 의미를 찾아보면 '관점'이라는 뜻도 있지만 다른 뜻도 있음.

'문법 형식의 하나로서 반복 등을 나타내는 동사의 형태'
{: .notice--info}

프로그램 측면에서 생각해보면 `반복되서 나타나는 중복된 코드`로 생각할 수도 있음.

생각해보면 로깅, 권한 검사 등과 같은 기능이 비즈니스 기능과 관계없이 부가적으로 실행하는 중복된 코드일 가능성이 큼.

즉, 비즈니스 기능과 관계없이 부가적으로 실행하는 중복된 코드들을 횡단 관심사(cross cutting concern)라고 할 수 있음.

## 관심의 분리

![관심사](/assets/images/post/java/2024-12-30-springstudy-06/concerns.png)

AOP 매커니즘은 프로그램을 관심사를 기준으로 핵심 관심사(core concern)와 횡단 관심사(cross cutting concern)로 분류함.

핵심 관심사는 비즈니스 기능이고, 횡단 관심사는 비즈니스 기능과는 다른 관심 영역임.

대표적인 횡단 관심사는 다음과 같음
* 로깅: 메소드 실행 시간이나 반환 값 등을 기록하는 데 사용
* 트랜잭션 관리: 데이터베이스 트랜잭션 관리에 사용
* 보안 검사: 권한 검사 등 보안 작업에 사용

# AOP 필요성

OOP는 프로그램 규모가 커질수록 공통적으로 발생하는 부가 기능(로깅, 보안, 트랜잭션 관리 등)을 처리하기 어려워짐.

부가 기능들은 여러 모듈에 걸쳐서 중복으로 나타나는데, 이로 인해 코드 가독성이 떨어지고 유지보수가 복잡해짐.

AOP는 부가 기능(횡단 관심사)을 `Aspect`라는 모듈 형태로 만들어서 설계하고 개발을 함.

`Aspect` 모듈에는 부가 기능(횡단 관심사)을 내포하고 있으며 자체적으로 부가 기능을 여러 객체의 핵심 기능에 교차로 적용을 시켜줌.

그러면 추상화를 통해 분리하는 작업도 필요 없어지고, 핵심 기능에 부가 기능의 코드가 남아 있지 않아도 됨.

* OOP와 AOP의 용도
  * OOP: 핵심 관심사의 모듈화
  * AOP: 인프라 혹은 횡단 관심사의 모듈화

# AOP 용어들

AOP의 개발 방식은 핵심 관심사를 Object로, 횡단 관심사는 Aspect라는 모듈로 모듈화함.

Aspect라는 모듈로 모듈화하는데, 몇 가지 AOP의 용어들을 알아야 함.

이 용어들은 스프링에 한정된 용어가 아니라 여러 AOP 프레임워크에서 통상적으로 사용되는 용어들임.

| 용어 | 설명 |
|:---:|---|
| Aspect | 횡단 관심사의 모듈화 |
| JoinPint | 프로그램 실행 중에 AOP가 적용될 특징 지점 <br> 메소드 실행, Exception 처리 등 |
| Advice | 특정 JoinPoint에 적용될 코드 |
| Introduction (inter-type) | Aspect 모듈 내부에 선언한 클래스 혹은 인터페이스와 그 외의 변수와 메소드 |
| PointCut | 여러 JoinPoint 중에 Advice를 적용할 특정 위치 |
| Target Object | Advice가 적용될 핵심 객체 <br> 비즈니스 기능을 구현한 클래스 |
| AOP Proxy | Aspect를 대신 수행하기 위해 AOP 프레임워크에 의해 성성된 객체 |
| Weaving | Target Object에 Advice 해서 횡단 관심사 코드가 포함된 객체가 생성되는 일련의 과정 |

Aspect = Advice + PointCut + Introduction(inter-type)
{: .notice--info}

아래 두 그림은 모두 AOP 개념 이해를 돕기 위한 것임.

![AOP-2](/assets/images/post/java/2024-12-30-springstudy-06/aop2.png)

![AOP-1](/assets/images/post/java/2024-12-30-springstudy-06/aop1.png)

# 스프링에서 간단한 Aspects 구현하기

간단한 Aspects를 구현해 보겠음.

## 의존성 주입

spring-aspects 의존성을 주입해야 함.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```


# 참고

* [Spring AOP 블로그1](https://kha0213.github.io/spring/spring-aop/)
* [Spring AOP 블로그2](https://velog.io/@chullll/Spring-AOP-95fce9zs)
* [AOP : Aspect Oriented Programming 개념](https://gmoon92.github.io/spring/aop/2019/01/15/aspect-oriented-programming-concept.html)
