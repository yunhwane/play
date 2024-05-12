# 코틀린에서 null을 다루는 방법

### 1. kotlin에서의 null 체크 


```java
public boolean startsWithA(String str) {
     return str.startsWith("A");
}
```

#### 이 자바 코드는 안전한 코드일까 ?

- str의 null 이 오게 되면, NPE 발생

``` kotlin
public boolean startsWithA(String str) {
     if(str == null) {
         ... 다양한 null 조건 처리 
     }
     return str.startsWith("A");
}
```
- null 체크를 통해 처리해줘야한다.

#### 코틀린으로 사용했을 때 null 처리 

``` kotlin
fun startsWithA1(str: String?) : Boolean {
    if(str == null) {
       ... 다양한 조건 처리 
    }
    return str.startsWith("A")
}
```
```kotlin
fun startsWithA1(str: String?) : Boolean? {
    if(str == null) {
       return null
    }
    return str.startsWith("A")
}
```
- 반환 값이 null 로 처리할 수도 있다.
- kotlin에서는 null 가능한 타입은 완전히 다르게 취급한다.

### null 가능한 타입만을 위한 기능은 없을까 ?

#### Safe call
```kotlin
val str: String? = "ABC"
str.length // 불가능하다.
str?.length // safe call
```
- null 이 아니면 실행하고, null 이면 실행하지 않는다


#### Elvis 연산자 ?: 
```kotlin
val str: String? = "ABC"
str?.length ?: 0 
```
- str 이 null 이면 elvis 연산자까지 합쳐 0이 최종 호출되고, str 이 null 아닐 때 연산 값이 나온다.

### 자바 코드를 코틀린스럽게 바꿔보자 

- Safe call 과 Elvis 연산자를 통한 null 처리
  
```kotlin
fun startWithA1(str : String?) Boolean {
  return str?.startsWith("A") ?: null 일때 다양한 조건 처리  
}

fun startWithA1(str : String?) Boolean? {
  return str?.startsWith("A")  
}
```
- early return도 사용할 수 있다.  

```kotlin
fun calculate(number: Long?): Long {
    number ?: return 0
    // 다음로직 
}
```
### nullable type 이지만, 아무리 생각해도 null이 될 수 없는 경우 


```kotlin
fun startsWithA(str: String?) Boolean {
   return str!!.startsWith("A")
}
```
- 만약 null 이 들어오면 런타임 NPE 발생

### 객체의 nullable

```kotlin
val person = Person("공부하는 개발자")

fun startsWith(str: String) Boolean  {
    return str.startsWith("A")
}
```
- 만약, 자바의 person 클래스에서 @Notnull이면 에러가 안나고, @Nullable 이면 에러가 발생한다.
- 자바 코드의 어노테이션 정보가 없으면, 런타임시 NPE가 발생하게 된다.
- 만약 코틀린과 자바를 같이 쓸 때, null 정보를 꼼꼼하게 작성 해줘야한다.  

### 자바 코드를 사용할 때 플랫폼 타입

```java
public class JavaContext {

  public String getValue(String key) {
    if ("A".equals(key) || "B".equals(key)) {
      return "OK";
    }
    return null;
  }

}
```
- 만약 사이즈가 커져서 자바 컨텍스트를 3군데에서 사용하게 되면 어떤 문제가 발생할까 ?

```kotlin
class A(private val javaContext: JavaContext) {
  fun logic1() {
    val str: String = javaContext.getValue("A")
    // ... 생략
  }
}

class B(private val javaContext: JavaContext) {
  // logic2, 위와 비슷하다.
}

class C(private val javaContext : JavaContext) {
  // logic3, 위와 비슷하다.
}
```
- 자바 컨텍스트의 의존성이 지나치게 퍼져 있다. A, B일때,  non-null String 반환하겠지만 만약 자바 컨텍스트가 바뀐다면, 3가지의 클래스를 모두 변경해줘야한다.
- 또한, 사용하는 사용처가 늘어날 수록 NPE 이슈가 발생할 수 있다.

```kotlin
class JavaContextWrapper(private val javaContext: JavaContext) {
  fun getValue(key: String): String {
    return javaContext.getValue(key)
      ?: throw IllegalArgumentException("key=${key}는 null을 반환했습니다.")
  }
}
```
- 자바컨텍스트를 직접사용하는 것이 아닌 wrapper로 한번 감싸서 처리하면, JavaContext의 null 관련 정책 변경 시 wrapper만 수정하면 됨
- wrapping을 한다라는 의미는 Java의 플랫폼 타입과 직접적으로 맞닿는 우리의 Kotlin 코드를 최소한으로 하여, 영향범위를 최소화하고, 예상치 못한 일이 발생했을 때 대응해야 하는 cost를 최소로 한다라는 의미가 된다


