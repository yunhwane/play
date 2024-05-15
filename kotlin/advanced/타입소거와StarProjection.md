자바에서는 지금도 raw type 을 만들 수 있다.

코틀린은 언어 초기부터 제네릭이 고려 되었기 때문에 raw type을 만들 수 없음

```kotlin

fun main() {
   val numbers: List = listOf(1,2,3)
}

```
- raw type 에러 발생한다.
- 코틀린도 jvm 위에서 동작하기 때문에 runtime 때는 타입 정보가 사라진다. 이것을 타입 소거(Type erasure) 라고 부른다.

### 타입 소거 

```kotlin
fun checkStringList(data: Any) {
  if(data is List<String>) {

  }
}
```
- Cannot check for instance of erased type: List <String> 에러가 발생한다.
- 런타임때는 String 정보가 사라지기 때문에 List<String> 인지 알 수 없다.

### StarProjection 으로 최소한의 리스트인지는 확인할 수 있다.

```kotlin
fun checkStringList(data: Any) {
  if(data is List<*>) {

  }
}
```
- star projection 을 활용하여 최소한의 리스트인지는 확인할 수 있다.
- 해당 타입 파라미터에 어떤 타입이 들어있는지는 모른다는 의미이다.

```kotlin
fun checkStringList(data: Any) {
  if(data is List<*>) {
     val element: Any? = data[0]
  }
}
```
- 리스트인건 확실하기 때문에 리스트의 기능을 사용할 수 있음
- 데이터 삽입은 불가능하다. 왜냐하면 어떤 타입이 있는지 모르기 때문에 삽입은 불가능하다.

### 코틀린의 컴파일러
```kotlin
fun checkMutableList(data: Collection<String>) {
  if(data is MutableList<String>) {
    data.add("hello world")
  }
}
```
이 함수에서 보면, collection<String> 으로 파라미터에서 함수에 들어왔다는 자체가 Mutable 타입이기에 삽입이 가능하다.

```kotlin

fun main() {
  val num = 3
  num.toSuperString()

  val str = "ABC"
  str.toSuperString()
}

fun<T> T.toSuperString() {
  println(T::class.java.name)
}
```
T가 런타임때 알 수 없기 때문에 T::class 알 수 없음

```kotlin
fun List<*>.hasAnyInstanceOfString(): Boolean {
    return this.any {it is String}
}

fun List<*>.hasAnyInstanceOfInt(): Boolean {
    return this.any {it is Int}
}
```
- 어떤 리스트에 대해서 특정 타입이 하나라도 있는지 확인하는 함수를 작성한다고 할때 모든 타입을 다 만들어줘야한다.
- T::Class를 가져오고 싶다고 했을 때 어떻게 할까?

```kotlin
inline fun <reified T> List<*>.hasAnyInstanceOf(): Boolean {
   return this.any {it is T}
}
```
inline : 코드의 본문을 호출 지점으로 이동시켜 컴파일 되는 함수
reified 키워드 특징, T 인스턴스를 만들거나, T의 companion object 를 가져올 수는 없다.

대표적인 filterIsInstance<>() 함수가 표준 라이브러리의 inline reified 키워드의 대표적인 예시이다.
