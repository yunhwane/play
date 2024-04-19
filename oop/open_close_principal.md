이 코드를 보자 

```kotlin
class Calculator {

    fun getRectangleArea(rectangle: Rectangle) : Double {
        return rectangle.height * rectangle.width
    }

    fun getCircleArea(circle : Circle) : Double {
        return circle.radius * circle.radius * Math.PI;
    }
    
}

data class Circle(val radius: Int) {
}
data class Rectangle(val height : Double, val width : Double) {    
}

```

- 여기서 5각형, 6각형 등 기능을 추가해야하는 상황이 발생한다면 ?

```kotlin
class Calculator {

    fun getRectangleArea(rectangle: Rectangle) : Double {
        return rectangle.height * rectangle.width
    }

    fun getCircleArea(circle : Circle) : Double {
        return circle.radius * circle.radius * Math.PI;
    }

    fun getPentagonArea(pentagon: Pentagon) : Double {
        ... 5각형 추가 로직 
    }

     ...6각형 추가 로직 
}

data class Circle(val radius: Int) {
}
data class Rectangle(val height : Double, val width : Double) {    
}
data class Pentagon(...) : Double {
}
... 6각형 추가로직 

```
- Calculator 클래스가 수정되어야하는 이슈가 발생함


### 어떻게 Calculator 클래스를 변경하지 않고 기능만 추가할 수 있을까 ?
- 다형성을 이용하자.
- 공통적인 부분을 보자.

```kotlin

class Calculator {

   fun getArea(shape: Shape) : Double {
       return shape.calculatorArea();
   }
    
}

data class Circle(val radius: Int) : Shape() {

    override fun calculatorArea(): Double {
        return this.radius * this.radius * Math.PI
    }

}
data class Rectangle(val height : Double, val width : Double) : Shape() {

    override fun calculatorArea(): Double {
        return this.height * this.width;
    }

}

abstract class Shape {
    abstract fun calculatorArea() : Double
}

```
- Shape 이라는 추상 클래스를 통하여 각 도형에 따른 계산을 자식 클래스에서 처리해줌(다형성)

```kotlin
data class Pentagon(...) : Shape () {
     override fun CalculatorArea() : Double {
            5각형 계산 로직 
     }
}
```

- 기능 추가, 변경 시 클래스 변경하지 않고 추가할 수 있도록 설계가 된다.
- 이 원칙이 확장에는 열려있어야하고, 수정에는 닫혀있어야한다는 개방 폐쇄의 원칙임
