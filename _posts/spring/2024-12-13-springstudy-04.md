---
layout: single
title: "[스프링스터디] 4장"
date: 2024-12-13 17:00:00 +0900
categories: 
  - Spring
tag: 
  - Spring
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 "스프링스터디" 학습 모임에서 "4장. 추상화(PSA) - 기능의 정의와 구현 분리" 부분을 공부한 내용을 정리한 포스트입니다.

원래 책에 4장 제목은 "4장 스프링 컨텍스트: 추상화" 이지만, 개인적으로 이해가 쉽도록 제목을 변경하였습니다.

본 글에 사용되는 예제 소스 전체는 [GitHub](https://github.com/NodleKang/springtextbook)에서 확인할 수 있습니다. 

글의 전반적인 톤은 네이버 블로거 "메르"님이 글 쓰시는 톤을 따라 했습니다.

# 추상화(PSA) - 기능의 정의와 구현 분리

교제에는 추상화라고 하지만 일반적으로는 "스프링 PSA(Portable Service Abstraction)"라는 용어로 더 많이 불려짐.

뭔가 어려운 것 같지만 간단히 생각하면 어떤 기능의 정의와 구현을 분리하는 것임.

기능의 정의와 구현을 분리하기 위해 원래 Java에서 제공하는 "인터페이스"를 사용함.

## Java 인터페이스

Java에서 인터페이스는 기능의 정의와 구현을 분리하기 위한 방법임.

인터페이스에는 클래스가 구현해야 하는 메서드의 명세만을 정의하고, 실제 구현은 다른 클래스에서 함.

인터페이스를 사용하면 기능의 정의와 구현을 분리할 수 있어 유지 보수가 쉬워짐.

비유하자면 건축사가 철근을 사용하는 경우, 철근의 기능만 정의하고 철근의 구현은 철근 제조사에게 맡기는 것과 같음.

## 시나리오1

시나리오를 추가해 보겠음.

* "엔지니어"는 "노트북"을 "사용"한다.
* "엔지니어"는 "기술지원서비스"를 제공한다.
* 실제로 할 수 있는 "기술지원서비스"에는 "톰캣기술지원서비스"도 있고, "어플리케이션기술지원서비스"도 있다.
* 고객은 "엔지니어"에게 "기술지원"을 요청하지만, 기술적인 구체적인 내용에는 관심이 없다.

### 코드로 구현하기

스프링에서는 기능은 제공하되, 구체적인 구현은 숨기기 위해서 Java에서 제공하는 인터페이스를 사용함.

이 방법은 구현을 숨기기 때문에 구현이 변경되어도 사용하는 쪽에서는 영향을 받지 않음.

우선 "기술지원서비스"의 기능만 정의함

```java
public interface 기술지원서비스 {
    void provideSupport();
}
```

"기술지원서비스"에서 실제로 하는 작업을 구현하는 클래스를 추가함

```java
public class 톰캣기술지원서비스 implements 기술지원서비스 {

    @Override
    public void provideSupport() {
        System.out.println("Tomcat 기술지원을 제공합니다.");
    }
}
```

```java
public class 어플리케이션기술지원서비스 implements 기술지원서비스 {

    @Override
    public void provideSupport() {
        System.out.println("Applicaton 기술지원을 제공합니다.");
    }
}
```

"엔지니어"가 "기술지원서비스"를 하도록 수정함

"엔지니어"는 "기술지원서비스" 종류가 늘어나거나 변경되어도 수정할 필요가 없음

```java
@Component
public class 엔지니어 {

    private String name;

    private final 노트북 laptop;
    private final 기술지원서비스 technicalSupport; // 기능이 정의되어 있는 인터페이스

    // 자동 와이어링 - 생성자를 통한 의존성 주입
    @Autowired
    public 엔지니어(노트북 laptop, 기술지원서비스 technicalSupport) {
        this.laptop = laptop;
        this.technicalSupport = technicalSupport;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public 노트북 getLaptop() {
        return laptop;
    }

    public void doWork() {
        laptop.turnOn();
        laptop.work();
        technicalSupport.provideSupport(); // 기능 사용
        System.out.printf("%s 가 노트북으로 작업을 완료했습니다.", name);
    }
}
```

"기술지원서비스"를 구현한 클래스를 이용해서 빈(Bean)을 등록함

"기술지원서비스"가 여러 가지인데, 특별히 어떤 "기술지원서비스"를 사용하겠다고 명시한 경우가 아니라면 "@Primary 애너테이션"이 있는 빈(Bean)을 우선적으로 사용하게 함.

```java
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
@ComponentScan(basePackages = "beans") // `@ComponentScan` 어노테이션을 사용해서 `beans` 패키지에 있는 빈(Bean)을 자동으로 등록
public class 기술지원구성 {

    @Bean
    @Primary // `@Primary` 어노테이션을 사용해서 `기술지원` 빈(Bean)을 자동 와이어링할 때 우선적으로 사용하게 지정
    @Qualifier("tomcat") // `@Qualifier` 어노테이션을 사용해서 톰캣기술지원 빈(Bean)을 구분하는 이름을 지정
    public 기술지원서비스 톰캣기술지원() {
        return new 톰캣기술지원서비스();
    }

    @Bean
    @Qualifier("application") // `@Qualifier` 어노테이션을 사용해서 어플리케이션기술지원서비스 빈(Bean)을 구분하는 이름을 지정
    public 기술지원서비스 어플리케이션기술지원서비스() {
        return new 어플리케이션기술지원서비스();
    }

}
```

스프링 컨텍스트에서 "엔지니어"를 불러와서 "기술지원서비스"를 실행하게 함.

명시한 "기술지원서비스"가 없으니까 "@Primary 애너테이션"이 있는 "톰캣기술지원" 빈(Bean)을 우선적으로 사용함.

```java
public class Main {

    public static void main(String[] args) {
        // `기술지원구성` 클래스를 사용해서 spring context 초기화
        var context = new AnnotationConfigApplicationContext(기술지원구성.class);

        System.out.printf("스프링 컨텍스트 초기화가 완료되었습니다.\n");

        // spring context 에서 `엔지니어` 클래스의 빈(Bean)을 가져옴
        엔지니어 worker = context.getBean(엔지니어.class);
        worker.setName("Middleware작업자");

        worker.doWork();

        context.close();
    }

}
```

만약 "엔지니어"가 "기술지원서비스"를 할 때, "어플리케이션기술지원"을 하게 하려면 아래와 같이 사용할 수 있음. (@Qualifier 애너테이션 사용)

```java
@Component
public class 엔지니어 {

    private String name;

    private final 노트북 laptop;
    private final 기술지원서비스 technicalSupport;

    @Autowired
    public 엔지니어(노트북 laptop, @Qualifier("application") 기술지원서비스 technicalSupport) {
        this.laptop = laptop;
        this.technicalSupport = technicalSupport;
    }

    ...
}
```

## 시나리오2

시나리오를 추가해 보겠음.

* "엔지니어"는 "노트북"을 "사용"한다.
* "엔지니어"는 "기술지원서비스"를 제공한다.
* 실제로 할 수 있는 "기술지원서비스"에는 "톰캣기술지원서비스"도 있고, "어플리케이션기술지원서비스"도 있다.
* 고객은 "엔지니어"에게 "기술지원"을 요청하지만, 기술적인 구체적인 내용에는 관심이 없다.
* "엔지니어"는 "기술지원"업무가 끝나면 "보고서를 저장소에 저장"해 놓아야 한다.
* "보고서"는 "파일"로 저장하거나 "DB"에 저장할 수 있다.

### 코드로 구현하기

앞에서 한 것처럼 "보고서"를 저장소에 저장하는 기능을 정의와 구현을 분리해서 구현하겠음. (Java에서 제공하는 인터페이스를 사용함.)

이 방법은 구현을 숨기기 때문에 구현이 변경되어도 사용하는 쪽에서는 영향을 받지 않음.

우선 "보고서저장소"의 기능만 정의함

```java
public interface 보고서저장소 {
    void save(String report);
}
```

"보고서저장소"에서 실제로 하는 작업을 구현하는 클래스를 추가함

```java
public class 파일보고서저장소 implements 보고서저장소 {

    @Override
    public void save(String report) {
        System.out.println("파일에 " + report + "를 저장합니다.");
    }
}
```

```java
public class DB보고서저장소 implements 보고서저장소 {

    @Override
    public void save(String report) {
        System.out.println("DB에 " + report + "를 저장합니다.");
    }
}
```

"엔지니어"가 "보고서저장소"에 보고서를 저장하도록 수정함.

"엔지니어"는 "보고서저장소" 종류가 늘어나거나 변경되어도 수정할 필요가 없음

```java
@Component
public class 엔지니어 {

    private String name;

    private final 노트북 laptop;
    private final 기술지원서비스 technicalSupport;
    private final 보고서저장소 reportRepository;

    // 자동 와이어링 - 생성자를 통한 의존성 주입
    @Autowired
    public 엔지니어(노트북 laptop, 기술지원서비스 technicalSupport, 보고서저장소 reportRepository) {
        this.laptop = laptop;
        this.technicalSupport = technicalSupport;
        this.reportRepository = reportRepository;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public 노트북 getLaptop() {
        return laptop;
    }

    public void doWork() {
        laptop.turnOn();
        laptop.work();
        technicalSupport.provideSupport(); // 기술 지원 서비스 사용
        System.out.printf("%s 가 노트북으로 작업을 완료했습니다.\n", name);
        reportRepository.save(name + "의 작업 보고서"); // 보고서 저장소 사용
    }
}
```

"보고서저장소"를 구현한 클래스를 이용해서 빈(Bean)을 등록함

"보고서저장소"가 여러 가지인데, 특별히 어떤 "보고서저장소"를 사용하겠다고 명시한 경우가 아니라면 "@Primary 애너테이션"이 있는 빈(Bean)을 우선적으로 사용하게 함.

```java
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
@ComponentScan(basePackages = "beans") // `@ComponentScan` 어노테이션을 사용해서 `beans` 패키지에 있는 빈(Bean)을 자동으로 등록
public class 기술지원구성 {

    ...

    @Bean
    @Primary // `@Primary` 어노테이션을 사용해서 `보고서저장소` 빈(Bean)을 자동 와이어링할 때 `파일보고서저장소`를 우선적으로 사용하게 지정
    public 보고서저장소 파일보고서저장소() {
        return new 파일보고서저장소();
    }

    @Bean
    @Qualifier("DB") // `@Qualifier` 어노테이션을 사용해서 톰캣기술지원 빈(Bean)을 구분하는 이름을 지정
    public 보고서저장소 DB보고서저장소() {
        return new DB보고서저장소();
    }

}
```

스프링 컨텍스트에서 "엔지니어"를 불러와서 "보고서저장소"에 보고서를 저장하게 함.

명시한 "보고서저장소"가 없으니까 "@Primary 애너테이션"이 있는 "파일보고서저장소" 빈(Bean)을 우선적으로 사용함.

```java
public class Main {

    public static void main(String[] args) {
        // `기술지원구성` 클래스를 사용해서 spring context 초기화
        var context = new AnnotationConfigApplicationContext(기술지원구성.class);

        System.out.printf("스프링 컨텍스트 초기화가 완료되었습니다.\n");

        // spring context 에서 `엔지니어` 클래스의 빈(Bean)을 가져옴
        엔지니어 worker = context.getBean(엔지니어.class);
        worker.setName("Middleware작업자");

        worker.doWork();

        context.close();
    }

}
```

## 주요 스테레오 타입 애너테이션

스프링에서는 다양한 스테레오 타입 애너테이션을 제공하고 있음.

스테레오 타입 애너테이션은 여러 애너테이션을 조합하여 더 간편하게 사용할 수 있도록 한 것임.

스테레오 타입 애너테이션은 @Component, @Service, @Repository, @Controller 등이 있음.

* @Component: 일반적인 스테레오 타입 애너테이션으로, 빈(Bean)을 등록할 때 사용함.
* @Service: 서비스 레이어에서 사용하는 스테레오 타입 애너테이션으로, 비즈니스 로직을 처리함.
* @Repository: 데이터베이스 레이어에서 사용하는 스테레오 타입 애너테이션으로, 데이터베이스와의 연동을 처리함.
* @Controller: 프리젠테이션 레이어에서 사용하는 스테레오 타입 애너테이션으로, 사용자의 요청을 처리함.
* @Configuration: 설정 파일에서 사용하는 스테레오 타입 애너테이션으로, 빈(Bean)을 등록할 때 사용함.
* @Bean: 설정 파일에서 사용하는 스테레오 타입 애너테이션으로, 빈(Bean)을 등록할 때 사용함.
* @Autowired: 의존성 주입을 할 때 사용하는 스테레오 타입 애너테이션으로, 빈(Bean)을 주입할 때 사용함.
* @Qualifier: 여러 빈(Bean) 중에서 특정 빈(Bean)을 선택할 때 사용하는 스테레오 타입 애너테이션으로, 빈(Bean)을 선택할 때 사용함.
* @Primary: 여러 빈(Bean) 중에서 기본 빈(Bean)을 선택할 때 사용하는 스테레오 타입 애너테이션으로, 기본 빈(Bean)을 선택할 때 사용함.

### @Service 애너테이션

@Service 애너테이션은 서비스 레이어에서 사용하는 스테레오 타입 애너테이션임.

서비스 레이어는 비즈니스 로직을 처리하는 레이어임.

@Component 애너테이션이 있는데도 굳이 @Service을 사용하는 건 @Service로 표시된 클래스들에 대해서 특정 aspect를 적용하기가 더 쉽기 때문임.

예를 들어, 모든 서비스 메소드에 대해 트랜잭션을 적용하거나 로깅을 추가하는 등의 작업을 할 수 있게 됨. (6장에서 aspect 관련 내용이 나올 것임)

```java
@Service
@Primary
@Qualifier("tomcat")
public class 톰캣기술지원서비스 implements 기술지원서비스 {

    @Override
    public void provideSupport() {
        System.out.println("Tomcat 기술지원을 제공합니다.");
    }
}
```

```java
@Service
@Qualifier("application")
public class 어플리케이션기술지원서비스 implements 기술지원서비스 {

    @Override
    public void provideSupport() {
        System.out.println("Applicaton 기술지원을 제공합니다.");
    }
}
```

스테레오 타입 애너테이션을 사용했으므로 스프링 컨텍스트에 자동으로 빈(Bean)이 등록되도록 설정을 수정함

```java
@Configuration
@ComponentScan(basePackages = {"beans", "service", "repository"})
public class 기술지원구성 {

}
```

### @Repository 애너테이션

@Repository 애너테이션은 데이터베이스 레이어에서 사용하는 스테레오 타입 애너테이션임.

데이터베이스 레이어는 데이터베이스와의 연동을 처리하는 레이어임.

@Component 애너테이션이 있는데도 굳이 @Repository을 사용하는 건 @Repository가 붙은 클래스에서 발생하는 예외를 스프링의 DataAccessException으로 자동 변환해주기 때문임.

그리고 @Repository로 표시된 클래스들에 대해서 특정 aspect를 적용하기가 더 쉽기 때문임.

예를 들어, 데이터베이스와의 트랜잭션 관리를 쉽게 할 수 있게 됨. (6장에서 aspect 관련 내용이 나올 것임. 아마도...)

```java
@Repository
@Primary
public class 파일보고서저장소 implements 보고서저장소 {

    @Override
    public void save(String report) {
        System.out.println("파일에 " + report + "를 저장합니다.");
    }
}
```

```java
@Repository
@Qualifier("DB")
public class DB보고서저장소 implements 보고서저장소 {

    @Override
    public void save(String report) {
        System.out.println("DB에 " + report + "를 저장합니다.");
    }
}
```

스테레오 타입 애너테이션을 사용했으므로 스프링 컨텍스트에 자동으로 빈(Bean)이 등록되도록 설정을 수정함

```java
@Configuration
@ComponentScan(basePackages = {"beans", "service", "repository"})
public class 기술지원구성 {

}
```
