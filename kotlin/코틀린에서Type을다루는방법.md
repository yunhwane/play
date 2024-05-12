
### 기본 타입

```kotlin
val number1 = 3 //Int
val number2 = 3L // Long
val nubmer3 = 3.f // Float
val number4 = 3.0 // Double
```
- 코틀린에서는 선언된 기본값을 보고 타입을 추론한다.
- 자바에서는 암시적 변환이 있지만 코틀린에서는 명시적으로 변경해줘야 한다.

```kotlin
val number1 : Int = 4
val number2 : Long = number1.toLong()
```
- 만약 nullable 변수면 적절한 처리가 필요하다.

```kotlin
val number1: Int? = 3
val number2: Long = number1?.toLong() ?: 0L
```

### 일반 타입 

```java
public static void printAgeIfPerson(Object obj) {
   if(obj instanceof Person) {
       Person person = (Person) obj;
       System.out.println(person.getAge());
   }
}
```
- java에서 intstanceof 는 변수가 주어진 타입이면, true 그렇지 않으면 false

```kotlin
fun printAgeIfPerson(obj : Any) {
      if(obj is Person) {
         val person = obj as Person
         println(person.age)
      }
}
```
- is -> instanceof
- as -> 객체로 간주한다
- !is -> instanceof 반대 
- 이것을 스마트 캐스트라고 한다.

```kotlin
fun main() {
     printAgeIfPerson(null)
}

fun printAgeIfPerson(obj: Any?) {
     val person  = obj as Person
     println(person.age)
}
```
- NPE 발생함
- 어떻게 처리해줘야 할까 ?

```kotlin
fun main() {
     printAgeIfPerson(null)
}

fun printAgeIfPerson(obj: Any?) {
     val person  = obj as? Person
     println(person?.age)
}
```
- as? 키워드를 통해서, 만약 obj가 null 이면 person이 null이도록 처리해줌
- person? 를 통해 null이 가능하도록 처리해줌


### IS
![스크린샷 2024-05-12 오후 9 04 49](https://github.com/yunhwane/play/assets/147581818/6f8fb2d9-8937-415e-b188-c632d5ae2006)
![스크린샷 2024-05-12 오후 9 05 42](https://github.com/yunhwane/play/assets/147581818/5e308356-cc31-450a-9b2c-011c6274e460)

### AS
![스크린샷 2024-05-12 오후 9 05 58](https://github.com/yunhwane/play/assets/147581818/97a04a1f-de8c-44a8-88e1-5753cd99bcc4)
![스크린샷 2024-05-12 오후 9 06 15](https://github.com/yunhwane/play/assets/147581818/8c679efc-4fbf-4f97-88c9-7511d81c7b22)



### Kotlin 특이한 타입 Any

1. Java의 Object 클래스
2. 모든 Primitive Type 의 최상위 타입도 Any 이다.
3. Any 자체로는 null을 포함할 수 없어 null을 포함하고 싶다면, Any?
4. Any 에 equals / hashCode / toString 존재


### Kotlin 특이한 타입 Unit

1. java의 void와 동일한 역할
2. void와 다르게 Unit 그 자체로 타입인자 사용 가능
3. 함수형 프로그래밍에서 Unit 은 단 하나의 인스턴스만 갖는 타입

### Kotlin 특이한 타입 Nothing 
1. 함수가 정상적으로 끝나지 않은 사실을 표현하는 역할  
2. 무조건 예외를 반환하는 함수, 무한 루프 함수

## 문자열 타입을 어떻게 사용할까 ?

```kotlin
val person = Person("전윤환", 100)
println("이름 : ${person.name}")
```
- ${변수}를 사용하는 것이 가독성, 일괄 변환, 정규식 활용 측면에서 좋음

```kotlin
val name = "전윤환"
"""
   ABC
    GDGD
    FDSAFAFDAS
    ${name}
""".trimIndent()
```

- 문자열에서 특정 문자를 가져오기
```kotlin
val str = "ABC"
println(str[0])
println(str[2])\
```




