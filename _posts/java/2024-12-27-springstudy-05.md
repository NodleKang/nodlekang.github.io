---
layout: single
title: "[스프링스터디] 5장"
date: 2024-12-27 13:00:00 +0900
categories: 
  - Spring
tag: 
  - Spring
  - Spring Scope
  - Singleton
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 "스프링스터디" 학습 모임에서 "5장. 스프링 컨텍스트: 빈의 스코프 및 수명주기" 부분을 공부한 내용을 정리한 포스트입니다.

글의 전반적인 톤은 네이버 블로거 "메르"님이 글 쓰시는 톤을 따라 했습니다.

책에서는 '싱글톤 빈 스코프'와 '프로토타입 빈 스코프'를 설명하고 있는데, 제가 개발하면서 한 번도 사용하지 않은 '프로토타입 빈 스코프' 부분은 제외하고 '싱글톤 빈 스코프' 부분만 정리 했습니다.

# 스코프(Scope)

스프링에서는 빈(Bean)을 생성하고 생명주기(Life Cycle)을 관리하는 방식을 스코프(Scope)라고 함.

스프링에서 자주 볼 수 있는 스코프는 '싱글톤 빈 스코프' 임.

# 싱글톤 빈 스코프

## 작동 방식과 싱글톤 스코프 빈 생성

스프링은 컨텍스트를 로드할 때 싱글톤 빈을 생성하고 빈에 이름(Bean ID)를 할당함.

특정 빈을 참조할 때 항상 동일한 인스턴스를 얻기 때문에 이를 '싱글톤 빈 스코프'라고 함.

<div class="notice-info" markdown="1">
흔히 얘기하는 '싱글톤 디자인 패턴'과 스프링에 '싱글톤 빈 스코프'는 다른 것임.

'싱글톤 디자인 패턴'은 어플리케이션 전체에서 특정 타입의 객체가 하나만 있어야 하지만,

스프링에 '싱글톤 빈 스코프'는 'Bean ID'만 유일하다면 같은 타입의 객체도 여러 개 있을 수 있음.
</div>

### @Bean으로 싱글톤 스코프 빈 생성 예

* 스프링 컨텍스트에 빈 추가

```java
package config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import service.CommonService;

@Configuration
public class ProjectConfig {

    @Bean // 스프링 컨텍스트에 CommonService 빈 추가
    public CommonService commonService() {
        return new CommonService();
    }
}
```

* 스프링 컨텍스트에 등록된 빈 사용 테스트

```java
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import service.CommonService;

import java.io.ObjectInputFilter;

public class Main {

    public static void main(String[] args) {
        var c = new AnnotationConfigApplicationContext(ObjectInputFilter.Config.class);

        // 같은 'Bean ID'를 사용하기 때문에, 스프링 컨텍스트에 있는 같은 빈을 참조함
        var cs1 = c.getBean("commonService", CommonService.class);
        var cs2 = c.getBean("commonService", CommonService.class);

        // 같은 빈을 참조하기 때문에 결과는 true 로 출력됨
        boolean b1 = cs1 == cs2;
        System.out.println(b1);
    }
}
```

### 스테레오타입 어노테이션으로 싱글톤 빈 생성

기본적으로 스프링에서 싱글톤 빈 등록은 @Bean 어노테이션을 사용할 때나 스테레오 타입 어노테이션을 사용할 때가 다르지 않음.

다만, **스테레오 타입 어노테이션을 사용할 때는 클래스 이름이 Bean ID가 됨**

## 싱글톤 빈 사용시 주의 점

스프링 컨텍스트에서 객체로 빈을 등록한다면, 불변인 경우에만 싱글톤 빈으로 등록해야 함.

스프렝 프레임워크에서 제공되는 기능이 객체에 필요하지 않다면 빈으로 등록을 할 필요가 없음.

왜냐하면 **race condition** 때문임.

**race condition**은 멀티스레드 아키텍처에서 여러 스레드가 같은 공유 자원을 서로 변경하려고 경쟁할 때 발생할 수 있음.

만약 빈이 동시성을 위해서 설계되지 않으면 예기치 않은 결과나 실행 예외를 유발하게 되는 것임. 

## 빈(Bean) 객체 생성 시점

스프링은 기본적으로 컨텍스트를 초기화할 때 모든 싱글톤 빈을 생성함.

이걸 **즉시 객체 생성** 방식이라고 함. (eager instantiation)

다른  방식으로 **지연 객체 생성** 방식이 있음. (lazy instantiation)

지연 객체 생성 방식은 빈이 실제로 필요해질 때까지 생성을 지연시켰다가 사용처가 생기면 객체를 생성하는 방식임.

두 방식의 장단점을 비교하면 다음과 같음.

| 구분 | 즉시 객체 생성 | 지연 객체 생성 |
|:---:|---|---|
| 장점 | * 모든 빈이 준비되어 있으므로 초기 요청 처리 속도가 빠름 <br> * 구성 오류를 조기에 발견할 수 있음 | * 어플리케이션 시작 시간을 단축시킬 수 있음. <br> * 메모리 사용을 최적화할 수 있음 |
| 단점 | * 어플리케이션 시작 시간이 길어질 수 있음 <br> * 사용하지 않는 빈까지 메모리를 차지함 | * 빈이 처음 요청될 때, 빈 생성으로 인한 지연이 있을 수 있음 <br> * 구성 오류를 늦게 발견할 수 있음 |
