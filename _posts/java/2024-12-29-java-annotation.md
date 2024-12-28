---
layout: single
title: "[Java] Annotation 개념과 예제 (작성중)"
date: 2024-12-29 13:00:00 +0900
categories: 
  - Java
tag: 
  - Java
  - Anntation
  - Reflection
  - Annotaion Processor
  - Retention
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 Java에 Annotaion 개념과 예제를 학습한 내용을 정리한 포스트입니다.

글의 전반적인 톤은 네이버 블로거 "메르"님이 글 쓰시는 톤을 따라 했습니다.

# 학습 배경

스프링 AOP를 학습해보니 어노테이션을 이해하고 있어야 AOP를 이해할 수 있다는 것을 깨달아서 공부함.

# 어노테이션이 뭘까?

Java로 코딩하다 보면 @Override, @Bean, @ComponentScan 등 어노테이션을 자주 보게 됨.

어노테이션(annotation)이라는 단어를 사전에 찾으면 "주석"이라고 나오는데, 

`/** ... */` 형식으로 쓰는 게 사람을 위한 주석이라면 

어노테이션은 컴파일러에게 도움을 주기 위한 주석이라고 볼 수 있음.

골뱅이(@)를 사용해서 주석처럼 달아 주면 됨.

[Java 공식문서](https://docs.oracle.com/javase/tutorial/java/annotations/)에 보면 코드에 영향을 주지 않는 **메타 데이터터**라고 함.

너무 복잡하게 생각하지 말고 우선은 **단순히 코드에 영향을 주지 않는 메타 데이터를 주입하는 문법** 정도로만 생각해도 됨.

어노테이션을 사용하면 다음과 같은 일을 할 수 있음

* 컴파일러가 코드 작성 문법 에러를 검사할 수 있는 정보 제공
* 빌드나 배포할 때에 코드를 자동으로 생성할 수 있게 하는 정보 제공
* 런타임시에 특정 기능을 실행하게 하는 정보 제공

# 어노테이션 종류

어노테이션은 크게 3가지로 구분됨

* 빌트인 어노테이이션: Java에서 기본적으로 제공하는 어노테이션. 예: @Override, @SupressWarnings
* 메타 어노테이션: 어노테이션을 위한 어노테이션임. 어노테이션의 적용 대상이나 유지 기간 등을 지정함
* 사용자 정의 어노테이션: 사용자가 만든 어노테이션

## 빌트인 어노테이션

Java에서 기본적으로 제공하는 어노테이션들이 여러 가지 있음.

여기서는 그 중 몇 가지를 살펴봄.

### @Override

컴파일러에게 오버라이딩할 메소드라는 걸 알리는 어노테이션임.

주로 상속받은 메소드를 오버라이드할 때 사용됨.

```java
@Override
public void getInfo() {
    System.put.println("test");
}
```

### @SuppressWarnings

컴파일러의 특정 경고를 출력하지 않게 설정하는 어노테이션임.

컴파일러 경고는 경고일 뿐이기 때문에 개발자가 상황을 알고 있다면 컴파일 로그만 지저분해져서 진짜 잡아야 할 경고가 안 보일 수 있기 때문에 사용함.

이 어노테이션은 파라미터를 받아서 사용하는데, 각 의미는 다음과 같음.

* @SuppressWarnings("all"): 모든 경고  
* @SuppressWarnings("cast"): 타입 형변환 관련 경고 억제 
* @SuppressWarnings("dep-ann"): 사용하지 말아야 할 주석 관련 경고 억제
* @SuppressWarnings("deprecated"): Deprecated 메소드를 사용할 경우 발생하는 경고 억제 
* @SuppressWarnings("fallthrough"): switch 문에서 break 구문 누락 관련 경고 억제 
* @SuppressWarnings("finally"): finally 블럭 관련 경고 억제 
* @SuppressWarnings("null"): null 관련 경고 억제 
* @SuppressWarnings("rawtypes"): 제너릭을 사용하는 클래스 파라미터 변수가 특정되지 않았을 때의 경고 억제 
* @SuppressWarnings("unchecked"): 검증되지 않은 연산자 관련 경고 억제 
* @SuppressWarnings("unused"): 사용하지 않는 코드 관련 경고 억제

## 메타 어노테이션

어노테이션을 위한 어노테이션으로 어노테이션의 적용 대상이나 유지 기간 등을 지정함.

주요 메타 어노테이션에는 @Retention, @Target 등이 있음.

### @Retention

어노테이션이 어느 시점까지 유지되어야 하는지 지정하는 어노테이션임.

어노테이션을 컴파일, 배포 혹은 런타임 중에 어느 시점까지 유지할지 정하는 거라고 생각하면 됨.

* **@Retention(RetentionPolicy.SOURCE)**
  - 소스 파일에만 존재하고, 클래스 파일에는 존재하지 않음.
* **@Retention(RetentionPolicy.CLASS)**
  - 클래스 파일에만 존재하고, 실행시점(Runtime)에는 사용 불가함.
  - 기본값
* **@Retention(RetentionPolicy.RUNTIME)**
  - 클래스 파일에 존재하여 실행시에 사용 가능함.

### @Target

생성할 어노테이션이 적용될 위치를 지정함.

* **@Target(ElementType.TYPE)**: 클래스,  인터페이스, 열거 타입에 어노테이션 적용
* **@Target(ElementType.ANNOTAION_TYPE)**: 어노테이션에 어노테이션 적용
* **@Target(ElementType.FIELD)**: 필드에 어노테이션 적용
* **@Target(ElementType.CONSTRUCTOR)**: 생성자에 어노테이션 적용
* **@Target(ElementType.METHOD)**: 메소드에 어노테이션 적용
* **@Target(ElementType.LOCAL_VARIABLE)**: 로컬 변수에 어노테이션 적용
* **@Target(ElementType.PACKAGE)**: 패키지에 어노테이션 적용

### @Inherited

자식 클래스가 어노테이션을 상속 받을 수 있음.

### @Repeatable

반복적으로 어노테이션을 선언할 수 있음.

### @Documented

자바 문서에도 어노테이션 정보가 표현됨.

## 사용자 정의 어노테이션

사용자 정의 어노테이션을 만들려면 **`@interface 어노테이션이름`** 형식으로 어노테이션을 정의해야 함.

어노테이션을 정의할 때, 생성할 어노테이션에 대한 메타 어노테이션을 어노테이션 정의 앞에 붙여줌.

다음과 같이 어노테이션을 정의할 수 있음.

```java
@Retention(RetentionPolicy.RUNTIME) // 런타임까지 어노테이션 유지
@Target(ElementType.TYPE) // 클래스,  인터페이스, 열거 타입에 어노테이션 적용
public @interface MyRuntimeAnnotation {
  String value() default "default value";
}
```

다음과 같은 어노테이션 사용 예제 코드를 보겠음. 

```java
public class Main {
  public static void main(String[] args) {
    A a = new A();
    System.out.println(a.value());
  }
}

@MyRuntimeAnnotation
class A{}
```

코드를 보면 정상적으로 실행되면서 "default value"이 출력될 것 같음. 하지만 실제는 컴파일 오류가 발생함.

왜냐면 어노테이션은 **코드에 영향을 주지 않는 메타 정보**이기 때문임.

만약 **`A`** 클래스에서 **`value()`**를 호출할 수 있었다면 **`A`**에 영향을 주는 것이었을 것임.

여기까지 어노테이션은 그저 문자열 정보를 가지고 있을 뿐임.

그럼 어떻게 사용해야 하냐면 **리플렉션**을 이용해야 함.

### 리플렉션(Reflection)

리플렉션은 런타임에 동적으로 특정 클래스의 정보를 추출할 수 있는 기법임.

리플렉션을 이용해서 어노테이션 정보를 가져와 보겠음.

```java
public class Main {
  public static void main(String[] args) {

    A a = new A();

    Annotaion[] annotations = a.getClass().getAnnotations();
    for (Annotaion annotation : annotations) {
      if (annotation instanceof MyRuntimeAnnotation) {
        MyRuntimeAnnotation myAnnotation = (MyRuntimeAnnotation) annotation;
        System.out.println("Annotation: " + annotation); // Annotation: @MyRuntimeAnnotation(value=default value)
        System.out.println("value: " + myAnnotation.value()); // Value: default value
      }
    }

  }
}
```

예제에서는 클래스로 객체를 생성해서 어노테이션 정보를 가져왔지만, 

객체 생성 없이 클래스 정보를 가져오거나 특정 패키지 안에 클래스를 모두 가져오는 것도 가능함.

### 어노테이션 프로세서(Annotaion Processor)

`@Retention(RetentionPolicy.RUNTIME)`이 아닐 때는 리플렉션을 사용하지 못 함.

그런 경우에는 어노테이션 프로세서를 생성해서 사용함.

어노테이션 프로세서는 컴파일 단계에서 Annotaion에 정의된 로직이 동작하게 하는 것임.

컴파일 단계에서 실행되기 때문에 빌드 단계에서 에러를 출력하게 할 수도 있고, 소스 코드나 바이트 코드를 생성하게 할 수도 있음.


# 참고

* [예제를 통해 어노테이션의 큰 그림을 이해하기기](https://velog.io/@l_cloud/%EC%98%88%EC%A0%9C%EB%A5%BC-%ED%86%B5%ED%95%B4-%EC%96%B4%EB%85%B8%ED%85%8C%EC%9D%B4%EC%85%98%EC%9D%98-%ED%81%B0-%EA%B7%B8%EB%A6%BC%EC%9D%84-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
