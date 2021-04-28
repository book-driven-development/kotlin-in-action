# 코틀린 기초

## Index

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [코틀린 기초](#코틀린-기초)
  - [Index](#index)
  - [기본 요소: 함수와 변수](#기본-요소-함수와-변수)
    - [Hello World](#hello-world)
  - [함수](#함수)
      - [식이 본문인 함수](#식이-본문인-함수)
    - [변수](#변수)
      - [변경 가능한 변수와 변경 불가능 한 변수](#변경-가능한-변수와-변경-불가능-한-변수)
    - [더 쉽게 문자열 형식 지정: 문자열 템플릿](#더-쉽게-문자열-형식-지정-문자열-템플릿)
  - [클래스와 프로퍼티](#클래스와-프로퍼티)
    - [프로퍼티](#프로퍼티)
    - [커스텀 접근자](#커스텀-접근자)
    - [코틀린 소스코드 구조: 디렉터리와 패키지](#코틀린-소스코드-구조-디렉터리와-패키지)
  - [선택 표현과 처리: enum과 when](#선택-표현과-처리-enum과-when)
    - [enum 클래스 정의](#enum-클래스-정의)
    - [when으로 enum 클래스 다루기](#when으로-enum-클래스-다루기)
    - [when과 임의의 객체를 함께 사용](#when과-임의의-객체를-함께-사용)
    - [인자 없는 when 사용](#인자-없는-when-사용)
    - [스마트 캐스트: 타입 검사와 타입 캐스트를 조합](#스마트-캐스트-타입-검사와-타입-캐스트를-조합)
    - [리펙토링: if를 when으로 변경](#리펙토링-if를-when으로-변경)
    - [if와 when의 분기에서 블록 사용](#if와-when의-분기에서-블록-사용)
  - [대상을 이터레이션: while과 for 루프](#대상을-이터레이션-while과-for-루프)
    - [while 루프](#while-루프)
    - [수에 대한 이터레이션: 범위와 수열](#수에-대한-이터레이션-범위와-수열)
    - [맵에 대한 이터레이션](#맵에-대한-이터레이션)
    - [in으로 컬렉션이나 범위의 원소 검사](#in으로-컬렉션이나-범위의-원소-검사)
  - [코틀린 예외처리](#코틀린-예외처리)
    - [try, catch, finally](#try-catch-finally)
    - [try를 식으로 사용](#try를-식으로-사용)
  - [요약](#요약)

<!-- /code_chunk_output -->


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

### when으로 enum 클래스 다루기
자바에서 `switch`에 해당하는것이 
코틀린에서 `when` 이다.

if와 마찬가지로 when도 값을 만들어내는 식이다.
따라서 식이 본문인 함수에 when으 바로 사용할 수 있다.
```kotlin
fun getMnemonic(color: Color) = 
    when(color) {
        Color.RED -> "Richard"
        Color.ORANGE -> "Of"
        Color.YELLOW -> "York"
        Color.GREEN -> "Gave"
        Color.BLUE -> "Battle"
        Color.INDIGO -> "In"
        Color.VIOLET -> "Vain"
    }

>>> println(getMnemonic(Color.BLUE))
Battle
```
자바와 달리 `switch`문과 달리 `break`를 넣지 않아도 된다.

한 분기에서 여러 값 사용하기
```kotlin
fun getWarmth(color: Color) = when(color) {
    Color.RED, Color.ORANGE, Color.YELLOW -> "warm"
    Color.GREEN -> "neutral"
    Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
}

>>> println(getWarmth(Color.ORANGE))
warm
```

```kotlin
import ch02.colors.Color   // 다른 패키지에서 정의한 Color 클래스를 임포트한다.
import ch02.colors.Color.* // 짧은 이름으로 사용하기 위해 enum 상수를 모두 임포트 한다.

fun getWarmth(color: Color) = when(color) {
    RED, ORANGE, YELLOW -> "warm"
    GREEN -> "neutral"
    BLUE, INDIGO, VIOLET -> "cold"
}
```

### when과 임의의 객체를 함께 사용
자바는 분기조건에 상수(enum 상수나 숫자 리터럴)만을 사용할 수 있는 바면
코틀린의 when 분기조건은 임의의 객체를 허용한다.

```kotlin 
fun mix(cl: Color, c2: Color) = 
    when(setOf(c1,c2)) {
        setOf(RED,YELLOW) -> ORANGE
        setOf(YELLOW,BLUE) -> GREEN
        setOf(BLUE,VIOLET) -> INDIGO
        else -> throw Exception("Dirty color")
    }

>>> println(mix(BLUE,YELLOW))
GREEN
```
또한 when의 분기조건에 식을 넣을 수 있기 때문에 많은 경우 코드를 더 간결하고 아름답게 작성할 수 있다.

### 인자 없는 when 사용
```kotlin
fun mixOptimized(c1: Color, c2: Color) = 
    when {
        (c1 == RED && c2 == YELLOW) || (c1 == YELLOW && c2 == RED) -> ORANGE
        (c1 == YELLOW && c2 == BLUE) || (c1 == BLUE && c2 == YELLOW) -> GREEN
        (c1 == BLUE && c2 == VIOLET) || (c1 == VIOLET && c2 == BLUE) -> INDIGO
        else -> throw Exception("Dirty color")
    }

>>> printn(mixOptimized(BLUE,YELLOW))
GREEN
```

### 스마트 캐스트: 타입 검사와 타입 캐스트를 조합

```kotlin
interface Expr
class Num(val value: Int) : Expr 
class Sum(val left: Expr, val right: Expr) : Expr
```
Sum은 Expr의 왼쪽과 오른쪽 인자에 대한 참조를 left와 right 프로퍼티로 저장한다.
left와 right는 각각 Num이나 Sum일 수 있다. 
(1 + 2) + 4 라는 식은 Sum(Sum(Num(1), Num(2)), Num(4))라는 구조의 객체가 생긴다.

Expr 인터페이스에는 두 가지 구현 클래스가 존재한다.
- 어떤 식이 수라면 그 값을 반환한다
- 어떤 식이 합계라면 좌항과 우항을 계산한 다음 그 두 값을 합한 값을 반환한다

```kotlin
fun eval(e: Expr) : Int {
    if (e is Num) {
        val n = e as Num
        return n.value
    }

    if(e is Sum) {
        return eval(e.right) + eval(e.left)
    }
    throw IllegalArgumentException("Unknown expression")
}
>>> println(eval(Sum(Sum(Num(1), Num(2)), Num(4))))
7
```

코틀린에서 `is`를 사용해 변수 타입을 검사한다.
어떤 변수가 원하는 타입인지 일단 is로 검사하고 나면 
변수를 원하는 타입을 캐스팅 하지않아도 변수가 그 타입으로 선언된 것처럼 사용할 수 있다.
이를 **스마트 캐스트**라고 부른다.

원하는 타입으로 명시적으로 타입 캐스팅을 하기 위해서는 
`as`를 사용한다
```kotlin
val n = e as Num
```

### 리펙토링: if를 when으로 변경
if 식을 본문으로 사용해 간단하게 만들기
```kotlin
fun eval(e: Expr) : Int =
    if (e is Num) {
        val n = e as Num
        return n.value
    } else if(e is Sum) {
        return eval(e.right) + eval(e.left)
    } else {
        throw IllegalArgumentException("Unknown expression")
    }
```

when으로 다듬기
```kotlin
fun eval(e: Expr) : Int =
    when(e){
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        else -> throw IllegalArgumentException("Unknown expression")
    }
```

### if와 when의 분기에서 블록 사용
```kotlin
fun evalWithLogging(e: Expr) : Int =
    when(e){
        is Num -> {
            println("num: ${e.value}")
        }
        is Sum -> {
            val left = evalWithLogging(e.left)
            val right = evalWithLogging(e.right)
            println("sum: $left + $right")
            left + right
        }
        else -> throw IllegalArgumentException("Unknown expression")
    }
```

## 대상을 이터레이션: while과 for 루프

### while 루프
코틀린에는 while과 do-while 루프가 있다. 자바와 다르지 않다.
```kotlin
while(조건) {
    /* ... */
}

do {
    /* ... */
} while(조건)
```

### 수에 대한 이터레이션: 범위와 수열
코틀린에서는 범위(range)를 사용한다.
범위는 기본적으로 두 값으로 이뤄진 구간이다. 보통은 그 두 값은 정수 등의 숫자타입의 값이며, `..`연산자로 시작 값과 끝 값을 연결해서 범위를 만든다.

```kotlin
val oneToTen = 1..10
```

코틀린의 범위는 폐구간(닫힌구간) 또는 양끝을 포함하는 구간이다.
```kotlin
fun fizzBuzz(i: Int) = when {
    i % 15 == 0 -> "FizzBuzz "
    i % 3 == 0 -> "Fizz "
    i % 5 == 0 -> "Buzz "
    else -> "$i "
}

>>> for(i in 1..100) {
      print(fizzBuzz(i))
    }
1 2 Fizz 4 Buzz Fizz 7 ...
```

역방향 수열을 만들고 그 감소하는 값은 2일때
```kotlin 
for(i in 100 downTo 1 step 2) {
    println(fizzBuzz(i))
}
```

### 맵에 대한 이터레이션
```kotlin
val binaryReps = TreeMap<Char,String>()
fun main(args: Array<String>) {
    val binaryReps = TreeMap<Char, String>()

    for (c in 'A'..'F') {
        val binary = Integer.toBinaryString(c.toInt())
        binaryReps[c] = binary
    }

    for ((letter, binary) in binaryReps) {
        println("$letter = $binary")
    }
}
```
`..` 연산자를 숫자 타입 뿐 아니라 문자 타입에도 적용할 수 있다.

맵에 사용한 구조 분해를 맵이아닌 컬렉션에도 사용할 수 있다
```kotlin
val list = arrayListOf("10","11","1001")
for((index,element) int list.withIndex()){
    println("$index: $element")
}
```

### in으로 컬렉션이나 범위의 원소 검사

```kotlin
fun isLetter(c: Char) = c in 'a'..'z' || c in 'A'..'Z'
fun isNotDigit(c: Char) = c !in '0'..'9'

fun main(args: Array<String>) {
    println(isLetter('q'))
    println(isNotDigit('x'))
}
```

when에서 `in`과 `!in` 사용
```kotlin
fun recognize(c: Char) = when (c) {
    in '0'..'9' -> "It's a digit!"
    in 'a'..'z', in 'A'..'Z' -> "It's a letter!"
    else -> "I don't know…​"
}

fun main(args: Array<String>) {
    println(recognize('8'))
}
```

범위는 문자에만 국한되지 않는다.
비교 가능한 클래스라면 그 클래스의 인스턴스 객체를 사용해 범위를 만들 수 있다.
```kotlin
println("Kotlin" in "Java".."Scala")
// true
```

```kotlin
println("Kotlin" in setOf("Java","Scala"))
// false
```


## 코틀린 예외처리
코틀린에서 예외 발생
```kotlin
if(percentage !in 0..100) {
    throw IllegalArgumentException()
}
```

### try, catch, finally
```kotlin
fun readNumber(reader: BufferedReader): Int? {
    try {
        val line = reader.readLine()
        return Integer.parseInt(line)
    }
    catch (e: NumberFormatException) {
        return null
    }
    finally {
        reader.close()
    }
}

fun main(args: Array<String>) {
    val reader = BufferedReader(StringReader("239"))
    println(readNumber(reader))
}
```
자바에서는 `throws IOException`과 같이 명시적으로 함수 뒤에 선언하여 처리한다
하지만 코틀리린에서는 이를 위한 특별한 문법을 제공하지 않는다.

### try를 식으로 사용
```kotlin

fun readNumber(reader: BufferedReader) {
    val number = try {
        Integer.parseInt(reader.readLine())
    } catch (e: NumberFormatException) {
        return
    }

    println(number)
}

fun main(args: Array<String>) {
    val reader = BufferedReader(StringReader("not a number"))
    readNumber(reader)
}
```


## 요약
- 함수를 정의할 때 `fun` 키워드를 사용한다. `val`과 `var`는 각각 읽기 전용 변수와 변경 가능한 변수를 선언할 대 쓰인다
- 문자열 템플릿을 사용하면 간결하게 표현 가능하다. 변수 이름앞에 `$`를 붙이거나, 식을 `${식}` 처럼 `${}`으로 둘러싸면 변수나 식의 값을 문자열안에 넣을 수 있따
- 코틀린에서는 값 객체 클래스를 아주 간결하게 표현할 수 있다
- 다른 언어에도 있는 if는 코틀린에서 식이며, 값을 만들어 낸다
- 코틀린 `when`은 자바의 `switch`와 비슷하지만 더 강력하다
- 어떤 변수의 타입을 검사하고 나면 굳이 그 변수를 캐스팅 하지 않아도 검사한 타입의 변수처럼 사용할 수 있다. 컴파일러가 스마트캐스트 해준다
- `for`,`while`,`do-while` 루프는 자바가 제공하는 기능과 비슷하다. 코틀린의 for은 자바보다 편리하다(맵을 이터레이션 하는등등)
- `1..5`와 같은 식은 범위를 만들어 낸다. for 루프에 대해 같은 추상화를 제공한다. 범위안에 들어있는지 아닌지 검사하려면 `in`,`!in`을 사용한다
- 코틀린 예외처리는 자바와 비슷하다. 다만 코틀린에서는 함수가 던질 수 있는 예외를 선언하지 않아도 된다
