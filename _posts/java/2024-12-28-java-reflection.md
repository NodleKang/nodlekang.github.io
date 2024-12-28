---
layout: single
title: "[Java] Reflection 개념과 사용"
date: 2024-12-28 13:00:00 +0900
categories: 
  - Java
tag: 
  - Java
  - Reflection
toc: true
toc_label: 목차
toc_sticky: true
---

본 포스트는 Java에 Reflection 개념과 예제를 학습한 내용을 정리한 포스트입니다.

글의 전반적인 톤은 네이버 블로거 "메르"님이 글 쓰시는 톤을 따라 했습니다.

# 학습 배경

스프링 AOP 학습을 하다가 어노테이션을 제대로 알아야겠다 싶어서 어노테이션을 학습하다 보니 리플렉션을 학습할 필요까지 생겨서 공부함.

# 리플렉션 이란?

![강아지](/assets/images/post/java/2024-12-28-java-reflection/dog-in-mirror.png)

리플렉션 정의를 보면 런타임에 클래스와 인터페이스 등을 검사하고 조작할 수 있는 기능이라고 되어 있음.

간단히 생각하면 거울에 투영된 강아지를 보듯이 클래스를 직접 조작하는 것이 아니라 JVM 메모리 영역의 정보를 보고 클래스를 조작하는 기술임.

JVM은 클래스 정보를 클래스 로드를 통해 읽어와서 해당 정보를 런타임 동안 JVM의 Method Area에 정적 변수를 포함한 모든 정보를 클래스 수준 데이터로 저장함.

![이미지](/assets/images/post/java/2024-12-28-java-reflection/java-reflection.png)

리플렉션을 좀 더 깊게 생각해보면 **어플리케이션 실행 중**에 사용자, OS, 다른 어플리케이션 등과 **상호작용**하면서 클래스와 인터페이스 등을 검사하고 조작할 수 있는 기능인 것임.

# 리플렉션 동작 원리

리플렉션 동작 원리를 알기 위해서는 JVM 동작 방식을 알아야 함.

![JVM동작방식](/assets/images/post/java/2024-12-28-java-reflection/jvm-works.png)

* 자바 소스를 컴파일러가 컴파일하면 바이트 코드가 됨.
* 이걸 클래스 로드가 JVM Runtime Data Areas에 올려줌.
* JVM Runtime Data Areas 중에 Method Area에 클래스 수준의 정보(클래스에 어떤 정보들이 있는지에 대한 메타 데이터)를 저장함.

포스트 첫 부분에 봤던 강아지 이미지를 생각하면 자바 소스는 강아지이고, Method Area를 거울에 비친 강아지라고 할 수 있음.

# 리플렉션 사용법

리플렉션 동작 원리를 생각하면 JVM Method Area에 있는 메타데이터를 가지고 런타임에 클래스를 검사하고 조작하는 기능이라고 할 수 있음.

JVM Method Area에 접근할 수 있으면 리플렉션을 사용할 수 있는 것임.

## Class 클래스

JVM Method Area에 접근할 때는 **`Class`** 클래스를 이용함.

**`Class`** 클래스는 JVM Method Area에 있는 클래스와 인터페이스 정보를 가져오는 전용 클래스임.

**`Class`** 클래스는 **`java.lang`** 패키지에서 제공되며, public 생성자가 존재하지 않아서 사용자가 직접 생성할 수 있는 방법은 없음.

**`Class`** 클래스의 객체는 JVM이 자동으로 생성해줌.

### Class 객체 획득 방법

Class 객체를 획득하는 방법은 3가지가 있음.

* 클래스의 `class` 프로퍼티를 이용해서 획득하기
* 인스턴스의 `getClass()` 메소드를 사용하기
* `Class` 클래스의 `forName()` 정적 메소드에 FQCN(Fully Qualified Class Name)을 전달해서 해당 경로와 대응하는 클래스에 대한 `Class` 클래스의 객체를 획득하기

각 방법을 이용해서 Class 객체를 획득하는 예는 다음과 같음.

```java
public class Main {

  public static void main(String[] args) throws ClassNotFoundException {
    
    // (1) 클래스타입.class
    final Class<MyClass> clazz1 = MyClass.class;

    // (2) 객체.getClass()
    final MyClass obj = new MyClass();
    final Class<? extends MyClass> clazz2 = obj.getClass();

    // (3) Class.forName('풀패키지경로')
    final Class<?> clazz3 = Class.forName(className: 'com.mycompany.app.MyClass');
  }
}
```

## 리플렉션에서 정보 얻기

리플렉션으로 얻을 수 있는 정보는 필드, 메소드, 생성자, Enum, Annotation, 배열, 부모 클래스와 인터페이스 등 다양함.

본 포스트에서는 필드, 메소드, 생성자 위주로 확인해 보겠음.

### getXXXs()와 getDeclaredXXXs()

**`Class`** 객체의 메소드 중에는 getXXXs()와 getDeclaredXXXs()가 있음.

이 메소드들을 이용하면 리플렉션을 통해 정보를 얻거나 가공할 수 있음.

* **getXXXs()**
  - **상속**받은 클래스와 인터페이스를 **포함**하여 **모든 public 요소들**만 가져옴.
  - getFields(), getMethods(), getAnnotations() 등
* **getDeclaredXXXs()**
  - **상속**받은 클래스와 인터페이스를 **제외**하고 해당 클래스에 접근제한자에 관계없이 **직접 정의된 요소만** 가져옴.
  - 해당 클래스에만 직접 정의된 **private, protected, public 요소를 모두** 가져옴.
  - getDeclaredFields(), getDeclaredMethods(), getDeclaredAnnotations() 등

### Field 접근

다음 예는 리플렉션을 이용해서 객체에 private 필드에 접근하는 코드의 예임.

<table>
<tr>
<td style="vertical-align: top;">

```java
class MyClass {
    private final String name = "MyClass";

    private void print() {
        System.out.println(name);
    }
}
```

</td>

<td>

```java
import java.lang.reflect.Field;

public class Main {
    public static void main(String[] args) throws Exception {
        final Class<MyClass> clazz = MyClass.class;
        final Field declaredField = clazz.getDeclaredField("name");
        declaredField.setAccessible(true);

        final MyClass myClass = new MyClass();
        final String name = declaredField.get(myClass).toString();

        System.out.println(name);
    }
}
```

</td>
</tr>
</table>

※ `setAccessible()` 메소드는 주로 접근 제어자에 의해 제한된 클래스 멤버에 대한 접근을 가능하게 하는 메소드임.

### Method 접근

다음 예는 리플렉션을 이용해서 객체에 private 메소드에 접근하는 코드의 예임.

<table>
<tr>
<td style="vertical-align: top;">

```java
class MyClass {
    private final String name = "MyClass";

    private void print() {
        System.out.println(name);
    }
}
```

</td>

<td>

```java
import java.lang.reflect.Method;

public class Main {
    public static void main(String[] args) throws Exception {
        final Class<MyClass> clazz = MyClass.class;
        MyClass myClass = new MyClass();

        Method declaredMethod = clazz.getDeclaredMethod("print");
        declaredMethod.setAccessible(true);
        declaredMethod.invoke(myClass);
    }
}
```

</td>
</tr>
</table>

※ `setAccessible()` 메소드는 주로 접근 제어자에 의해 제한된 클래스 멤버에 대한 접근을 가능하게 하는 메소드임.

### 생성자 접근

다음 예는 리플렉션을 이용해서 객체에 private 메소드에 접근하는 코드의 예임.


<table>
<tr>
<td style="vertical-align: top;">

```java
class MyClass {
    private String name = "MyClass";

    private MyClass(String name) {
        this.name = name;
    }
}
```

</td>

<td>

```java
import java.lang.reflect.Constructor;

public class Main {
    public static void main(String[] args) throws Exception {
        final Class<MyClass> clazz = MyClass.class;

        // 생성자 획득
        final Constructor<MyClass> constructor = clazz.getDeclaredConstructor(String.class);
        constructor.setAccessible(true);

        // 가져온 생성자를 사용해서 객체 생성
        final MyClass myClass = constructor.newInstance("Hello");

        System.out.println(myClass.toString());
    }
}
```

</td>
</tr>
</table>

※ `setAccessible()` 메소드는 주로 접근 제어자에 의해 제한된 클래스 멤버에 대한 접근을 가능하게 하는 메소드임.

# 리플렉션 단점

리플렉션은 런타임에 클래스를 분석하기 때문에 느림.

이런 특징 때문에 컴파일 시점에는 타입 체크가 불가능함.

또한 객체의 추상화가 깨진다는 단점도 존재하기 때문에 정말 필요한 곳에만 리플렉션을 한정적으로 사용해야 함.

# 리플렉션 사용 경험

APM 솔루션을 개발할 때, alert을 발송하는 기능을 플러그인으로 개발해서 적용할 때에 리플렉션을 사용했었음.

소스 코드 안에서는 플러그인이 위치할 패키지 경로를 탐색해서 클래스들을 가져와서 사용하게 함.
