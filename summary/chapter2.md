# 코틀린 기초

## 기본 요소: 함수와 변수

### Hello World
```kotlin
fun main(args : Array<String>) {
    println("Hello, world!")
}
```

- 함수 선언 `fun` 키워드 사용
- 파라미터명 뒤에 파라미터 타입 쓴다
- 함수를 최상위 수준에 정의할 수 있다
- `System.out.println` -> `println`
- 세미콜론 미사용


## 함수 
```kotlin
fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}
```
반환 타입은 사이클 콜론(:)뒤에 붙힌다.

> 문(statement)과 식(expression)의 구분
> 코틀린에서 `if`는 식이지 문이 아니다.
> 식은 다른 값을 만들어 내며 다른 식의 하위 요소로 계산에 참여할 수 있는 반면 문은 자신을 둘러싸고 있는 가장 안쪽 
> 블록의 최상위 요소로 존재하며 아무런 값을 만들어 내지 않는다는 차이가 있다
> 자바에서 모든 제어구조가 문인 반면, 코틀린에서는 루프를 제외한 대부분의 제어구조가 식이다

#### 식이 본문인 함수
앞서 본 함수를 `return`을 제거하고 등호(`=`)를 사용하여 간결하게 표현할 수 있다.
```kotlin
fun max(a: Int, b: Int) = if(a > b) a else b
```
본문이 중괄호로 둘러싸인 함수를 **블록이 본문인 함수**, 등호와 식으로 이뤄진 함수를 **식이 본문인 함수**라 한다.
식이 본문인 함수의 경우 사용자가 반환타입을 적지 않아도 컴파일러가 함수 본문 식을 분석해서 결과 타입을 함수 반환타입으로 정해준다.
이렇게 컴파일러가 프로그램 구성요소 타입을 정해주는 기능을 **타입추론**이라 한다.

### 변수
```kotlin
val question = "삶, 우주, 그리고 모든 것에 대한 궁극적인 질문"
val answer = 42
val answer2 : Int = 43
val yearsToCompute = 7.5e6 // 7.5 * 10^6 = 7500000.0
```

#### 변경 가능한 변수와 변경 불가능 한 변수
- `val` value에서 따와, 변경 불가능한 참조를 저장하는 변수
- `var` variable에서 따와, 변경 가능한 참조를 저장하는 변수
꼭 필요할 때만 `var`을 사용하는것이 부수효과가 없는 함수형 코드에 가깝다.

`val`은 한 번만 초기화 되어야 한다. 하지만 어떤 블록이 실행될 때 오직 한 초기화 문장만 실행됨을 컴파일러가 확인할 수 있다면
조건에 따라 val 값을 여러 값으로 초기화 할 수도 있다.

```kotlin
val message: String
if(canPerformOperation()) {
    message = "Success"
} else {
    message = "Failed"
} 
```

`val`에 대한 참조 자체가 불변일지라도 그 참조가 가리키는 객체의 내부값은 변경될 수 있다.
```kotlin
val languages = arrayListOf("Java") 
languages.add("Kotlin")
```

### 더 쉽게 문자열 형식 지정: 문자열 템플릿
문자열 템플릿 사용
```kotlin
fun main(args: Array<String>) { 
    val name = if(args.size > 0 args[0] else "Kotlin")
    println("Hello, %name!") // Java의 ("Hello," + name + "!")과 동일
}
```

```kotlin
fun main(args: Array<String>) { 
    if(args.size > 0 ) {
        println("Hello, ${args[0]}!")
    }
}
```

## 클래스와 프로퍼티
클래스를 선언하는 기본 문법을 알아보자.

자바에서는 필드가 여러개로 늘어나면 생성자 파라미터도 늘어나게되어 반복적인 작업이 필요하다.
```java
public class Person {
    private final String name;

    public Person(String name) {
        this.name = name;
    }
    
    public String getName() {
        return name;
    }
}
```

코틀린에서는 간결하게 표현 가능하다
```kotlin
class Person(val name: String)
```

### 프로퍼티
클래스의 개념의 목적은 데이터를 캡슐화하고 캡슐화한 데이터를 다루는 모든 코드를 한 주체 아래에 두는 것이다.

자바에서는 데이터를 필드<sup>field</sup>에 저장하며, 멤버 필드의 가시성은 보통 비공개(`private`)다.
클래스는 자신을 사용하는 클라이언트가 데이터에 접근할 수 있도록 **접근자 메소드**를 제공한다.
필드를 읽기위한 게터<sup>getter</sup>, 필드를 변경할 경우 <sup>setter</sup>를 사용한다.
자바에서 필드와 접근자를 한데 묶어 **프로퍼티**<sup>property</sup>라 부른다.

코틀린 프로퍼티는 자바의 필드와 접근자 메소드를 완전히 대신한다.
```kotlin
class Person(
    val name: String,       // 읽기 전용 프로퍼티로, 코틀린은(비공개)필드와 필드를 읽는 단순한 (공개) 게터를 만들어낸다.
    var isMarried: Boolean, // 쓸 수 있는 프로퍼티로, 코틀린은(비공개)필드와 필드를 읽는 단순한 (공개) 게터를 (공개) 세터를 만들어낸다.
)
```

```kotlin
val person = Person("Bob", true)
println(person.name) // Bob
println(person.isMarried) // true
```

### 커스텀 접근자
프로퍼티 접근자를 직접 작성하는 방법을 알아보자.
```kotlin
class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() {
            return height == width
        }
}
```
직사각형이 정사각형인지를 별도의 필드에 저장하지 않고, 사각형의 너비와 높이가 같은지를 그때 그때 검사하여 알 수 있다.

### 코틀린 소스코드 구조: 디렉터리와 패키지
```kotlin
package geometry.shapes

import java.util.Random


class Rectangle(val height: Int, val width: Int) {
    val isSquare: Boolean
        get() {
            return height == width
        }
}

fun createRandomRectangle(): Rectangle {
    val random = Random()
    return Rectangle(random.nextInt(), random.nextInt())
}
```


## 선택 표현과 처리: enum과 when

### enum 클래스 정의

```kotlin
enum class Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}
```

자바와 마찬가지로 enum은 단순히 값만 열거하는 클래스가 아니다.
enum 클래스안에도 프로퍼티나 메소드를 정의할 수 있다.
```kotlin
enum class Color (
    val r: Int, val g: Int, val b: Int
) {
    RED(255,0,0)
    ORANGE(255,165,0),
    YELLOW(255,255,0),
    GREEN(0,255,0),
    BLUE(0,0,255),
    INDIGO(75,0,130),
    VIOLET(238,130,238);  //여기에 세미콜론을 사용해야 한다

    fun rgb() = (r * 256 + g) * 256 + b
}

>>> println(Color.BLUE.rgb())
```