---
layout: single
title: "[스프링스터디] 2장"
date: 2024-12-13 16:00:00 +0900
categories: 
  - Spring
tag: 
  - Spring
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 "스프링스터디" 학습 모임에서 "2장. 스프링 컨텍스트: 빈 정의" 부분을 공부한 내용을 정리한 포스트입니다.

글의 전반적인 톤은 네이버 블로거 "메르"님의 글쓰는 방식을 따라 했습니다.

# 빈(Bean)

빈(Bean)은 스프링 컨테이너가 관리하는 객체임.

비유하자면 건물을 짓는 작업에 필요한 인력, 재료, 도구 등 각각의 요소를 빈(Bean)이라고 할 수 있음.

빈이 스프링 컨텍스트에 등록되면 스프링이 빈을 관리함.

창문 설계도가 있어야 창문을 만들 수 있듯이, 빈(Bean)도 클래스가 있어야 만들 수 있음.

모든 빈은 클래스로부터 생성되지만, 모든 클래스가 빈을 만드는 것은 아님.

클래스를 이용해서 만들어진 객체 중에서 스프링 컨텍스트에 등록된 객체만이 빈(Bean)임.

# 빈(Bean) 등록

## 시나리오

익숙한 업무를 바탕으로 스프링 학습을 진행하면 이해가 쉬울 것 같음.

그래서 다음 시나리오를 바탕으로 설명하겠음.

"엔지니어"는 "노트북"을 사용한다.
{: .notice--info}

## 코드로 구현하기

시나리오에서 빈(Bean)으로 등록할만한 대상은 엔지니어와 노트북임.

빈(Bean)을 사용하려면 스프링 컨텍스트에 등록을 해야 함.

빈(Bean)을 등록하는 방법들은 다음과 같이 여러 가지가 있는데, 이 중에 상황에 따라 필요한 방법을 골라서 사용하면 됨.

1. @Bean 애너테이션을 사용한 빈(Bean) 등록 - 수동으로 빈 등록
2. 스테레오 타입 애너테이션을 사용해서 빈(Bean) 등록 - 자동으로 빈 등록, **일반적으로 가장 많이 사용하는 방법임**
3. registerBean 메소드를 사용해서 빈(Bean) 등록 - 스프링 5에서 소개되었음

### @Bean 메서드를 사용한 빈 등록

시나리오에 있는 "엔지니어"를 빈을 등록해 보겠음.

@Bean 메서드를 사용하면 빈을 등록할 수 있는데, 이 방식은 매뉴얼하게 빈을 등록하는 방법임.

@Bean 메서드는 구성 클래스(=@Configuration 애너테이션이 붙은 클래스)에 작성함.

이 방식은 빈(Bean)을 생성한 후 초기화 작업을 하거나, 같은 클래스를 가지고 여러 빈(Bean)을 생성할 때 사용함.

```java
package beans;

// 엔지니어 클래스는 Component 어노테이션을 사용하지 않았으므로 수동으로 빈(Bean)을 등록해야 함
public class 엔지니어 {

    private String name;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

```java
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
public class 기술지원구성 {

    // 빈(Bean)을 수동으로 등록
    // `@Configuration`과 `@Bean` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)을 등록
    @Bean
    public 엔지니어 작업자() {
        엔지니어 작업자 = new 엔지니어();
        작업자.setName("Middleware 담당자");
        return 작업자;
    }

    // 빈(Bean)을 수동으로 등록
    // `@Configuration`과 `@Bean` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)을 등록
    @Bean
    public String 버전() {
        return "베타 1.0";
    }

}
```

```java
public class Main {

    public static void main(String[] args) {
        // `기술지원구성` 클래스를 사용해서 spring context 초기화
        var context = new AnnotationConfigApplicationContext(기술지원구성.class);

        // spring context 에서 `엔지니어` 클래스의 빈(Bean)을 가져옴
        엔지니어 작업자 = context.getBean(엔지니어.class);
        System.out.println("작업자: " + 작업자.getName());

        // spring context 에서 `String` 클래스의 빈(Bean)을 가져옴
        String 버전 = context.getBean(String.class);
        System.out.println("프로그램 버전: " + 버전);
    }
}
```

### 스테레오 타입 애너테이션을 사용한 빈(Bean) 등록

시나리오에 있는 **"노트북"**을 빈으로 등록해 보겠음

스테레오타입 애너테이션을 클래스에 붙여서 빈을 등록할 수 있는데, 이 방식은 자동으로 빈을 등록하는 방법임.

스테레오타입 애너테이션은 스프링 프레임워크에서 자주 사용되는 빈(Bean)의 역할을 정의하는 것임.

스테레오타입 애너테이션을 사용하는 것이 **일반적인 프로젝트에서 선호하는 방식**임.

참고로 스테레오 타입 애너테이션은 여러 애너테이션을 조합하여 더 간편하게 사용할 수 있도록 한 것임.

대표적인 스테레오 타입 애너테이션은 @Component, @Service, @Repository, @Controller 등이 있음.

클래스 위에 스테레오 타입 애너테이션을 사용하면 빈(Bean)으로 등록할 수 있음.

```java
@Component
public class 노트북 {

    private String type;

    public 노트북() {
        this.type = "표준노트북";
    }

    public 노트북(String type) {
        this.type = type;
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

}
```

스테레오 타입 애너테이션이 붙어 있는 빈(Bean)이 등록되게 하려면 @Configuration 에서 @ComponentScan 애너테이션을 사용해서 찾아내도록 해야 함

```java
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
@ComponentScan(basePackages = "beans") // `@ComponentScan` 어노테이션을 사용해서 `beans` 패키지에 있는 빈(Bean)을 자동으로 등록
public class 기술지원구성 {
     ...
}
```

```java
public class Main {

    public static void main(String[] args) {
        // `기술지원구성` 클래스를 사용해서 spring context 초기화
        var context = new AnnotationConfigApplicationContext(기술지원구성.class);

        // spring context 에서 `노트북` 클래스의 빈(Bean)을 가져옴
        노트북 유휴노트북 = context.getBean(노트북.class);
        System.out.println("유휴노트북: " + 유휴노트북.getType());
    }
}
```

### registerBean 메서드를 사용한 빈 등록

registerBean 메서드를 사용해서 빈을 등록하는 방법도 있음. (나도 사용한 적이 없어서 넘어가겠음)

```java
public class Main {
    public static void main(String[] args) {
        var context = new AnnotationConfigApplicationContext();
        context.registerBean(MyBean.class);
    }
}
```

## 빈(Bean) 등록과 싱글톤 패턴

스프링에서 빈(Bean)은 기본적으로 싱글톤 패턴으로 동작함.

싱글톤 패턴은 객체를 하나만 생성하고, 이 객체를 여러 곳에서 공유하여 사용하는 패턴임.

스프링에서 빈(Bean)을 등록하면 스프링 컨텍스트가 빈(Bean)을 생성하고, 이 빈(Bean)을 여러 곳에서 공유하여 사용함.


## 같은 클래스로 빈(Bean) 여러 개 등록하기

앞의 시나리오를 생각해보면, 프로젝트 전체에 엔지니어 1명과 노트북 1개만 있는 것임.

즉, 어떤 요청이 와도 같은 엔지니어가 같은 노트북을 가지고 작업을 해야 함.

그럼 상황이 바뀌면 어떻게 해야 할지 알아보겠음

"엔지니어"는 "Middleware엔지니어"와 "APM엔지니어"가 있다.
{: .notice--info}

### 코드로 구현하기

이런 경우에는 스테레오 타입 애너테이션은 이용할 수 없음.

@Bean 애너테이션을 사용해서 빈(Bean)을 등록해야 함.

```java
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
@ComponentScan(basePackages = "beans") // `@ComponentScan` 어노테이션을 사용해서 `beans` 패키지에 있는 빈(Bean)을 자동으로 등록
public class 기술지원구성 {

    // 빈(Bean)을 수동으로 등록
    // `@Configuration`과 `@Bean` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)을 등록
    @Bean
    public 엔지니어 작업자() {
        엔지니어 작업자 = new 엔지니어();
        작업자.setName("Middleware 담당자");
        return 작업자;
    }

    // 빈(Bean)을 수동으로 등록
    // `@Configuration`과 `@Bean` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)을 등록
    @Bean
    @Primary
    public 엔지니어 작업자2() {
        엔지니어 작업자 = new 엔지니어();
        작업자.setName("APM 담당자");
        return 작업자;
    }

}
```
