# static 

```java
public class JavaPerson {

  private static final int MIN_AGE = 1;

  public static JavaPerson newBaby(String name) {
    return new JavaPerson(name, MIN_AGE);
  }

  private String name;

  private int age;

  private JavaPerson(String name, int age) {
    this.name = name;
    this.age = age;
  }

}
```

코틀린 코드로 옮겨보면,

```kotlin
class Person private constructor(var name: String, var age: Int,) {
      companion object {
          private val MIN_AGE = 0
          fun newBaby(name : String): Person {
               return Person(name, MIN_AGE)
          }
      }
}
```

자바에서의 static 은 클래스가 인스턴스화 될 때 새로운 값이 복제 되는 것이 아니라 정적으로 인스턴스끼리의 값을 공유하는 것을 말하는데,
코틀린에서도 클래스와 유일하게 정적으로 소통하는 오브젝트라고 보면됨 

#### const ?
- const는 컴파일 시 할당되고, val 은 런타임시에 할당되기 때문에 진짜 상수를 처리할때 const를 사용한다.


- 이름도 붙일 수 있고, 인터페이스 구현도 가능하다. 
```kotlin
class Person private constructor(var name: String, var age: Int,) {
      companion object Factory : Log {
          private const MIN_AGE = 0
          fun newBaby(name : String): Person {
               return Person(name, MIN_AGE)
          }
          override fun log() {
              println("나는 person 클래스의 로그이다")
          }
      }
}

interface Log {
    fun log()
}
```

### 자바에서 companion object 를 사용하려면 ?
```kotlin
companion object Factory : Log {
          private const MIN_AGE = 0
          @JvmStatic
          fun newBaby(name : String): Person {
               return Person(name, MIN_AGE)
          }
          override fun log() {
              println("나는 person 클래스의 로그이다")
          }
      }
```

- JvmStatic 어노테이션을 static 메서드 처럼 사용이 가능하다.

# 싱글톤

```java
public class JavaSingleton {

  private static final JavaSingleton INSTANCE = new JavaSingleton();

  private JavaSingleton() { }

  public static JavaSingleton getInstance() {
    return INSTANCE;
  }
}
```
코틀린에서는 
```
object Singleton {
    var a : Int = 0
}
```

# 익명 클래스
- 특정 인터페이스나 클래스를 상속받은 구현체를 일회성으로 사용할 때 쓰는 클래스

```kotlin
fun main() {
  moveSomething(object : Movable {
     override fun move() {
         println("move") 
     }
     override fun fly() {
         println("fly")
     }
  })
}

private fun moveSomething(movable : Movable) {
   movable.move()
   movable.fly()
}

interface Movable {
    fun move();
    fun fly();
}
```
코틀린에서는 object: 타입이름으로 익명클래스를 표현한다. 


