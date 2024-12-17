---
layout: single
title: "[스프링스터디] 3장"
date: 2024-12-13 16:30:00 +0900
categories: 
  - Spring
tag: 
  - Spring
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 "스프링스터디" 학습 모임에서 "3장. 의존성 주입 (DI, Dependency Injection)" 부분을 공부한 내용을 정리한 포스트입니다.

원래 책에 3장 제목은 "3장 스프링 컨텍스트: 빈 작성" 이지만, 개인적으로 이해가 쉽도록 제목을 변경하였습니다.

본 글에 사용되는 예제 소스 전체는 [GitHub](https://github.com/NodleKang/springtextbook)에서 확인할 수 있습니다.

글의 전반적인 톤은 네이버 블로거 "메르"님이 글 쓰시는 톤을 따라 했습니다.

# 의존성 주입 (DI, Dependency Injection)

스프링에서 의존성 주입은 빈(Bean) 간의 의존 관계를 설정하는 방법임.

"의존"이라는 건 무언가에 의존한다는 의미이고, "주입"이라는 건 외부에서 무언가를 주입한다는 의미임.

스프링에서 의존성을 주입한다고 하는 건, 스프링 컨텍스트가 빈(Bean)을 주입하기 때문에 빈(Bean) 간의 의존 관계가 설정된다는 의미임.

비유하자면 건물을 지을 때 필요한 건축사, 철근, 시멘트 등이 서로 의존 관계에 있음.

건축 현장 관리자가 철근을 필요로 하는 건축사에게 철근을 줘서 건물을 지을 수 있도록 하는 것과 같음.

스프링에서 의존성 주입은 와이어링이라고 부르기도 함.

## 시나리오

시나리오를 다시 보겠음.

"엔지니어"는 "노트북"을 "사용"한다.
{: .notice--info}

엔지니어가 노트북을 사용하게 하려면, 엔지니어에게 노트북을 주입해줘야 하는 것임

## 코드로 구현하기

스프링에서 의존성 주입은 와이어링이라고 부르기도 함.

와이어링 방법에는 암묵적 와이어링(@Autowired 애너테이션을 사용한 방법)과 다이렉트 와이어링이 있음.

각각 자동 의존성 주입과 명시적 의존성 주입으로 구분해서 생각해도 됨.

### @Autowired 애너테이션을 사용한 의존성 주입

@Autowired 애너테이션을 사용한 빈(Bean) 간의 의존 관계 설정 방법은 생성자 주입, 필드 주입, 세터 주입이 있음. (settter 주입 방식은 생략)

#### 생성자 주입

생성자 주입은 빈(Bean)을 생성할 때 생성자를 통해 의존성을 주입하는 방법임.

가장 많이 사용되며, 권장하는 방법임.

```java
@Component // `@Component` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)으로 등록
public class 엔지니어 {

    private String name;

    private final 노트북 laptop;

    @Autowired
    public 엔지니어(노트북 laptop) {
        this.laptop = laptop;
        System.out.printf("Auto Wiring 방식 중 생성자 주입 방식을 사용해서 %s 이 주입되었습니다.\n"
                , laptop.getType());
    }

    public void setName(String name) {
        this.name = name;
    }

    public void doWork() {
        laptop.turnOn();
        laptop.work();
        System.out.println("엔지니어가 노트북으로 작업을 완료했습니다.");
    }
}
```

스프링 컨텍스트 구성에서 엔지니어를 생성할 때에 노트북을 주입함.

```java
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
@ComponentScan(basePackages = "beans") // `@ComponentScan` 어노테이션을 사용해서 `beans` 패키지에 있는 빈(Bean)을 자동으로 등록
public class 기술지원구성 {

}
```

```java
public class Main {

    public static void main(String[] args) {
        // `기술지원구성` 클래스를 사용해서 spring context 초기화
        var context = new AnnotationConfigApplicationContext(기술지원구성.class);
        System.out.printf("스프링 컨텍스트 초기화가 완료되었습니다.\n");

        // spring context 에서 `엔지니어` 클래스의 빈(Bean)을 가져옴
        엔지니어 작업자 = context.getBean(엔지니어.class);
        작업자.setName("Middleware 담당자");

        작업자.doWork();

        context.close();
    }

}
```

#### 필드 주입

필드 주입은 필드를 통해 빈(Bean)을 주입하는 방법임.

필드 주입 방식을 사용하면 생성자 주입과 달리 생성자를 작성하지 않아도 됨.

```java
@Component // `@Component` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)으로 등록
public class 엔지니어 {

    private String name;

    private 노트북 laptop;

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public 노트북 getLaptop() {
        return laptop;
    }

    @Autowired
    public void setLaptop(노트북 laptop) {
        this.laptop = laptop;
        System.out.printf("Auto Wiring 방식 중 필드 주입 방식을 사용해서 %s 이 주입되었습니다.\n"
                , laptop.getType());
    }

    public void doWork() {
        laptop.turnOn();
        laptop.work();
        System.out.println("엔지니어가 노트북으로 작업을 완료했습니다.");
    }
}
```

```
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
@ComponentScan(basePackages = "beans") // `@ComponentScan` 어노테이션을 사용해서 `beans` 패키지에 있는 빈(Bean)을 자동으로 등록
public class 기술지원구성 {

}
```

```java
public class Main {

    public static void main(String[] args) {
        // `기술지원구성` 클래스를 사용해서 spring context 초기화
        var context = new AnnotationConfigApplicationContext(기술지원구성.class);
        System.out.printf("스프링 컨텍스트 초기화가 완료되었습니다.\n");

        // spring context 에서 `엔지니어` 클래스의 빈(Bean)을 가져옴
        엔지니어 작업자 = context.getBean(엔지니어.class);
        작업자.setName("Middleware 담당자");

        작업자.doWork();

        context.close();
    }

}
```

### 다이렉트 와이어링 (Direct Wiring)

다이렉트 와이어링은 직접 빈(Bean)을 주입하는 방법임.

다이렉트 와이어링은 생성자 주입, 메서드 직접 호출, 세터 주입 방식 등을 사용할 수 있음. (settter 주입 방식은 생략)

#### 생성자 주입

생성자 주입은 빈(Bean)을 생성할 때 생성자를 통해 의존성을 주입하는 방법임.

교제에서는 이 내용을 "3.1.2 @Bean 메서드의 매개변수로 빈 와이어링하기"라고 나와 있음

```java 
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
// 자동으로 찾을 수 없는 빈(Bean)을 수동으로 등록
public class 기술지원구성 {

    // `노트북`를 빈(Bean)으로 등록
    // `@Configuration`과 `@Bean` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)을 수동으로 등록
    @Bean
    public 노트북 laptop() {
        노트북 laptop = new 노트북("LG Gram"); // 빈(Bean) 생성
        return laptop;
    }

    // `엔지니어`를 빈(Bean)으로 등록
    // `@Configuration`과 `@Bean` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)을 수동으로 등록
    @Bean
    public 엔지니어 작업자(노트북 laptop) {
        엔지니어 작업자 = new 엔지니어(laptop); // 다이렉트 와이어링 - 생성자를 통한 의존성 주입
        작업자.setName("Middleware 담당자");
        System.out.printf("""
                Direct Wiring 방식 중 생성자 주입을 사용해서
                작업자 %s에게 %s를 주입했습니다.
                """, 작업자.getName(), laptop.getType());
        return 작업자;
    }

}
```

#### 메서드 직접 호출

메서드 직접 호출은 빈(Bean)을 생성할 때, 다른 Bean을 생성하는 메서드를 직접 호출해서 빈을 등록하는 방법임

교제에서는 이 내용을 "3.1.1 두 @Bean 메서드 간 직접 메소드를 호출하는 빈 작성"라고 나와 있음

```java
@Configuration // `@Configuration` 어노테이션을 사용해서 스프링 설정 클래스임을 명시
// 자동으로 찾을 수 없는 빈(Bean)을 수동으로 등록
public class 기술지원구성 {

    // 다른 빈(Bean)에서 노트북을 주입해서 사용할 수 있도록 빈(Bean)으로 등록
    // `@Configuration`과 `@Bean` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)을 수동으로 등록
    @Bean
    public 노트북 laptop() {
        노트북 laptop = new 노트북();
        laptop.setType("LG Gram");
        return laptop;
    }

    // `엔지니어`를 빈(Bean)으로 등록
    // `@Configuration`과 `@Bean` 어노테이션을 사용해서 스프링 컨테이너에 빈(Bean)을 수동으로 등록
    @Bean
    public 엔지니어 작업자() {
        엔지니어 작업자 = new 엔지니어();
        작업자.setName("Middleware 담당자");
        작업자.setLaptop(laptop()); // 다이렉트 와이어링 - 빈(Bean) 생성 메소드를 직접 호출
        System.out.printf("""
                Direct Wiring 방식 중 Method 주입을 사용해서
                작업자 %s에게 노트북 %s를 주입했습니다.
                """, 작업자.getName(), 작업자.getLaptop().getType());
        return 작업자;
    }

}
```
