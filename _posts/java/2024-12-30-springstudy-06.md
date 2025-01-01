---
layout: single
title: "[스프링스터디] 6장"
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

본 글에 사용되는 예제 소스 전체는 [GitHub](https://github.com/NodleKang/springtextbook)에서 확인할 수 있습니다. 

# AOP 란

AOP(Aspects Oriented Programming)는 관점 지향 프로그래밍이라 함.

어떤 로직을 기준으로 핵심 관심사(core concern)와 횡단 관심사(cross cutting concern)로 관점을 분리해서 보고, 모듈화하는 것임.

핵심 관심사는 비즈니스 기능이고, 횡단 관심사는 비즈니스 기능과는 다른 관심 영역임.

Aspects에 사전적 의미를 찾아보면 '관점'이라는 뜻도 있지만 다른 뜻도 있음.

'문법 형식의 하나로서 반복 등을 나타내는 동사의 형태'
{: .notice--info}

프로그램 측면에서 생각해보면 `반복되서 나타나는 중복된 코드`로 생각할 수도 있음.

생각해보면 로깅, 권한 검사 기능 등이 비즈니스 기능과 관계없이 부가적으로 실행하는 중복된 코드일 가능성이 큼.

이렇게 **비즈니스 기능과 관계없이 부가적으로 실행하는 중복된 코드들을 횡단 관심사(cross cutting concern)**라고 할 수 있는 것임.

![관심사](/assets/images/post/java/2024-12-30-springstudy-06/concerns.png){: width="60%"}

대표적인 횡단 관심사는 다음과 같음
* 로깅: 메소드 실행 시간이나 반환 값 등을 기록하는 데 사용
* 트랜잭션 관리: 데이터베이스 트랜잭션 관리에 사용
* 보안 검사: 권한 검사 등 보안 작업에 사용

# AOP 필요성

AOP는 OOP만으로는 아쉬운 부분을 충족시키기 위해서 나왔다고 할 수 있음.

OOP는 프로그램 규모가 커질수록 공통적으로 발생하는 부가 기능(로깅, 보안, 트랜잭션 관리 등)을 처리하기 어려워짐.

부가 기능들은 여러 모듈에 걸쳐서 중복으로 나타나는데, 이로 인해 코드 가독성이 떨어지고 유지보수가 복잡해짐.

AOP는 부가 기능(횡단 관심사)을 `Aspect`라는 모듈 형태로 만들어서 설계하고 개발을 함.

그러면 추상화를 통해 분리하는 작업도 필요 없어지고, 핵심 기능에 부가 기능의 코드가 남아 있지 않아도 됨.

* OOP와 AOP의 용도
  * OOP: 핵심 관심사의 모듈화
  * AOP: 인프라 혹은 횡단 관심사의 모듈화

# AOP 용어들

AOP의 개발 방식은 핵심 관심사를 Object로, 횡단 관심사는 Aspect라는 모듈로 모듈화함.

AOP에서 사용하는 몇 가지 용어들이 있음.

이 용어들은 스프링에 한정된 용어가 아니라 여러 AOP 프레임워크에서 통상적으로 사용되는 용어들이며, 개념을 알야둬야 함.

| 용어 | 설명 |
|:---:|---|
| Aspect | 횡단 관심사의 모듈화 |
| Target Object | 부가 기능(crosss concerns)을 적용할 대상 <br> 주로 비즈니스 기능을 구현한 클래스임. |
| Advice | 부가 기능(crosss concerns)이 적용될 위치를 선정하는 기능 |
| JoinPint | 프로그램 실행 중에 AOP가 적용될 특정 위치 <br> 주로 메소드가 실행될 때, Exception을 처리해야 할 때 등이 있음 <br> 스프링에서는 메소드가 실행될 때로 한정하고 있음. |
| PointCut | 여러 JoinPoint 중에 Advice가 적용될 지점 |
| Introduction (inter-type) | Aspect 모듈 내부에 선언한 클래스 혹은 인터페이스와 그 외의 변수와 메소드 |
| AOP Proxy | Aspect를 대신 수행하기 위해 AOP 프레임워크에 의해 성성된 객체 |
| Weaving | Target Object에 Advice 해서 횡단 관심사 코드가 포함된 객체가 생성되는 일련의 과정 |

Aspect = Advice + PointCut + Introduction(inter-type)
{: .notice--info}

아래 두 그림은 모두 AOP 개념 이해를 돕기 위한 것임.

![AOP-2](/assets/images/post/java/2024-12-30-springstudy-06/aop2.png){: width="60%"}

![AOP-1](/assets/images/post/java/2024-12-30-springstudy-06/aop1.png){: width="60%"}

## Advice 종류

Advice는 어떤 JoinPoint에 코드를 적용할지 결정하는 것으로 다섯 종류가 있음:

* **Before** : 대상이 호출되기 전에 Advice 기능을 수행함
* **After** : 대상이 호출된 후에 Advice 기능을 수행함
* **After-returning** : 대상이 성공적으로 호출된 후에 Advice 기능을 수행함
* **After-throwing** : 대상이 예외를 던진 후에 Advice 기능을 수행함
* **Around** : Advice가 대상 작업을 감싸서 호출 전과 호출 후에 할 기능을 정의함

# 스프링에서 간단한 Aspects 구현하기

간단한 Aspects를 구현해 보겠음.

예제 소스 전체는 [[GitHub SpringTextBook ch6]](https://github.com/NodleKang/springtextbook/tree/main/ch6-aop)에 있음. 

## 시나리오

[[스프링스터디] 4장]({% post_url java/2024-12-13-springstudy-04 %})에서 사용했던 시나리오에 로깅 기능을 추가해보겠음.

* "엔지니어"는 "노트북"을 "사용"한다.
* "엔지니어"는 "기술지원서비스"를 제공한다.
* 실제로 할 수 있는 "기술지원서비스"에는 "톰캣기술지원서비스"도 있고, "어플리케이션기술지원서비스"도 있다.
* 고객은 "엔지니어"에게 "기술지원"을 요청하지만, 기술적인 구체적인 내용에는 관심이 없다.
* "엔지니어"는 "기술지원"업무가 끝나면 "보고서를 저장소에 저장"해 놓아야 한다.
* "보고서"는 "파일"로 저장하거나 "DB"에 저장할 수 있다.
* **'서비스'패키지에 속하는 "톰캣기술지원서비스"나 "'어플리케이션기술지원서비스"에 메소드가 실행될 때마다 로그를 남기도록 함**

## 의존성 주입

스프링에서 Aspects를 구현하기 위해 spring-aspects 의존성을 주입함.

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>6.1.14</version>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>6.1.14</version>
    </dependency>
</dependencies>
```

## 스프링 앱에서 애스펙트 활성화하기

`@EnableAspectJAutoProxy` 애너테이션을 추가해서 애스펙트 매커니즘을 활성화함.

aspects 패키지에서도 빈을 찾아서 등록하게 함

```java
@Configuration
@ComponentScan(basePackages = {"beans", "service", "repository", "aspects"}) // aspects 패키지에서도 빈을 찾아서 등록하게 함
@EnableAspectJAutoProxy // 스프링에서 애스펙트 매커니즘 활성화
public class 기술지원구성 {

}
```

## 애스펙트 클래스 작성하기

1. `@Aspect` 애너테이션을 붙여서 애스펙트 클래스인 것을 명시함.
2. `@Component` 애너테이션을 붙여서 스프링이 빈(Bean)으로 등록하게 함.
3. `@Around` 애너테이션을 붙여서 스프링이 언제 어떤 메소드를 가로챌지 지시함. (@Around 애너테이션은 advice 종류 중에 하나임.)
4. 애스펙트 클래스에서는 로깅을 남기면서도, 원래 실행됐어야 하는 메소드를 실행함.

```java
// `@Aspect` 애너테이션을 보고 애스펙트 클래스라는 걸 알게됨
@Aspect
// `@Component` 애너테이션을 보고 스프링 컨테이너가 빈(Bean)으로 자동으로 등록
@Component
public class LoggingAspect {
  // @Around 애너테이션
  // : AOP의 Advice에 해당하는 부분임
  // : 메소드 실행 전후에 적용됨
  // execution(* service.*.*(..))
  // : AOP의 PointCut에 해당하는 부분으로 AspectJ 표현식으로 작성되어 있음
  // : 무슨 값을 반환하든 관계없이 service 패키지에 있는 모든 클래스의 모든 메소드가 실행(execution)될 때마다 가로챔
  @Around("execution(* service.*.*(..))")
  public Object log(ProceedingJoinPoint joinPoint) throws Throwable {

    String methodName = joinPoint.getSignature().getName();
    Object[] arguments = joinPoint.getArgs();

    System.out.println("== 로깅 애스펙트 : 메소드 " + methodName + " 가 파라미터 "
            + Arrays.asList(arguments) + "를 가지고 실행됩니다.");

    Object returnedByMethod = joinPoint.proceed();

    System.out.println("== 로깅 애스펙트 : " + methodName + " 메소드가 실행되었으며, "
            + returnedByMethod + " 를 반환했습니다.");

    return returnedByMethod;
  }
}
```

## 애스펙트가 적용된 부분 찍어보기

애스펙트를 service 패키지에 적용했으므로, service 패키지의 클래스를 호출하는 코드에 잘 적용됐는지 확인하는 코드를 추가함.

```java
@Component
public class 엔지니어 {

    ... 생략 ...

    public void doWork() {
        laptop.turnOn();
        laptop.work();
        technicalSupport.provideSupport(); // 기술 지원 서비스 사용
        System.out.println("== AOP 구현을 위한 프록시 패턴이 적용됐는지 확인하는 내용 찍어보기");
        System.out.println(technicalSupport.getClass().getName()+" 클래스가 사용됩니다.");
        System.out.println("==============");
        System.out.printf("%s 가 노트북으로 작업을 완료했습니다.\n", name);
        reportRepository.save(name + "의 작업 보고서"); // 보고서 저장소 사용
    }
}
```

## 실행 결과

시나리오 대로 개발한 코드를 실행하면 아래와 같이 "<로깅 애스펙트>" 내용을 확인할 수 있음.

```text
스프링 컨텍스트 초기화가 완료되었습니다.
노트북을 켭니다.
노트북으로 작업합니다.
== 로깅 애스펙트 : 메소드 provideSupport 가 파라미터 []를 가지고 실행됩니다.
Tomcat 기술지원을 제공합니다.
== 로깅 애스펙트 : provideSupport 메소드가 실행되었으며, null 를 반환했습니다.
== AOP 구현을 위한 프록시 패턴이 적용됐는지 확인하는 내용 찍어보기
jdk.proxy2.$Proxy24 클래스가 사용됩니다.
==============
Middleware솔루션작업자 가 노트북으로 작업을 완료했습니다.
파일에 Middleware솔루션작업자의 작업 보고서를 저장합니다.
```

## 애너테이션 기반으로 PointCut 적용하기

앞에 예에서 작성한 애스펙트 클래스에 PointCut을 애노테이션 기반으로도 변경할 수 있음

만약 `@LoggingCheck`라는 사용자 정의 애너테이션을 만들어뒀다면 아래 코드처럼 AspectJ 표현식 대신 애너테이션을 사용하게 수정할 수 있는 것임.

```java
@Aspect
@Component
public class LoggingAspect {

  // @Around 애너테이션
  // : AOP의 Advice에 해당하는 부분임
  // : 메소드 실행 전후에 적용됨
  // execution("@annotation(LoggingCheck)")
  // : 만약 
  @Around("execution(* service.*.*(..))")
  public Object log(ProceedingJoinPoint joinPoint) throws Throwable {
    ... 생략 ...
  }
}
```

# AOP 구현 방법

스프링에 한정해서가 아니라 Java에서 AOP를 구현하는 방법은 크게 3가지가 있음(컴파일, 클래스 로드시, 프록시 패턴)

`J`라는 클래스에 Aspects를 적용하는 것일 예로 들어보겠음.

## 컴파일 시점

J.java 소스를 J.class 파일로 컴파일하는 시점에 적용될 Aspects를 끼워 넣어서 AOP를 구현하는 방식임.

## 클래스 로드 시점

J.java 소스를 J.class 파일로 컴파일까지 끝난 후에, 클래스 로더가 메모리에 올릴 때에 Aspects를 끼워 넣어서 AOP를 구현하는 방식임.

## 프록시 패턴 방식

타겟 클래스 `J`를 부가 기능을 제공하는 프록시로 감싸서 실행하는 방식임.

**스프링 AOP에서 사용하는 방식**임.

스프링은 IoC(Inversion of Control)와 DI(dependencies Injection)를 기반으로 하기 때문에 가능한 방식임.

앞에 확인한 예제의 결과에서 "jdk.proxy2.$Proxy24 클래스가 사용됩니다." 라는 문장이 출력되었는데, 이 부분으 바로 스프링이 프록시 패턴으로 AOP를 적용한 것을 알 수 있는 부분임.

# Spring AOP vs AspectJ

| 구분 | Spring AOP | AspectJ |
|:---:|:---:|:---:|
| 목표 | 간단한 AOP 기능 제공 | 완벽한 AOP 기능 제공 |
| JoinPint | 메소드 레벨만 지원 | 셍성자, 필드, 메소드 등 다양하게 지원 |
| Weaving | 런타임시에만 가능 | 런타임을 제공하지 않음. <br> compile-time, post-compile, load-time 제공 |
| 대상 | Spring Container가 관리하는 Bean에만 가능 | 모든 Java Object에 가능 |

# 참고

* [Spring AOP 블로그1](https://kha0213.github.io/spring/spring-aop/)
* [Spring AOP 블로그2](https://velog.io/@chullll/Spring-AOP-95fce9zs)
* [AOP : Aspect Oriented Programming 개념](https://gmoon92.github.io/spring/aop/2019/01/15/aspect-oriented-programming-concept.html)
* [제이의 Spring AOP](https://youtu.be/Hm0w_9ngDpM?si=fsVitsh73EnY20ll)
