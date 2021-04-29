# 함수 정의와 호출

## Index
<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [함수 정의와 호출](#함수-정의와-호출)
  - [Index](#index)
  - [코틀린에서 컬렉션 만들기](#코틀린에서-컬렉션-만들기)
  - [함수를 호출하기 쉽게 만들기](#함수를-호출하기-쉽게-만들기)
    - [이름 붙인 인자](#이름-붙인-인자)
  - [디폴트 파라미터 값](#디폴트-파라미터-값)
    - [정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티](#정적인-유틸리티-클래스-없애기-최상위-함수와-프로퍼티)
      - [최상위 프로퍼티](#최상위-프로퍼티)
    - [메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티](#메소드를-다른-클래스에-추가-확장-함수와-확장-프로퍼티)
    - [임포트와 확장 함수](#임포트와-확장-함수)
    - [자바에서 확장 함수 호출](#자바에서-확장-함수-호출)
    - [확장 함수로 유틸리티 함수 정의](#확장-함수로-유틸리티-함수-정의)
    - [확장 함수는 오버라이드 할 수 없다](#확장-함수는-오버라이드-할-수-없다)
    - [확장 프로퍼티](#확장-프로퍼티)
  - [컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원](#컬렉션-처리-가변-길이-인자-중위-함수-호출-라이브러리-지원)
    - [가변 인자 함수: 인자의 개수가 달라질 수 있는 함수 정의](#가변-인자-함수-인자의-개수가-달라질-수-있는-함수-정의)
    - [값의 쌍 다루기: 중위 호출과 구조 분해 선언](#값의-쌍-다루기-중위-호출과-구조-분해-선언)
  - [문자열과 정규식 다루기](#문자열과-정규식-다루기)
    - [문자열 나누기](#문자열-나누기)
    - [정규식과 3중 따옴표로 묶은 문자열](#정규식과-3중-따옴표로-묶은-문자열)
    - [여러 줄 3중 따옴표 문자열](#여러-줄-3중-따옴표-문자열)
  - [요약](#요약)

<!-- /code_chunk_output -->


## 코틀린에서 컬렉션 만들기
```kotlin
val set = hashSetOf(1, 7, 53)
val list = arrayListOf(1, 7, 53)
val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```


## 함수를 호출하기 쉽게 만들기
```kotlin
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(list) // Call toString()
}
// [1,2,3]
```

joinToString 함수 활용
```kotlin
fun <T> joinToString(
        collection: Collection<T>,
        separator: String,
        prefix: String,
        postfix: String
): String {

    val result = StringBuilder(prefix)

    for ((index, element) in collection.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(joinToString(list, "; ", "(", ")"))
}
// (1; 2; 3)
```

### 이름 붙인 인자
코틀린으로 작성한 함수를 호출할 때는 함수에 전달하는 인자 중 일부(또는 전부)의 이름을 명시할 수 있다
```kotlin
val list = listOf(1, 2, 3)
println(list.joinToString(separator = "; ",prefix = "(", postfix = ")"))
```

## 디폴트 파라미터 값
함수에 디폴트 파라미터를 정해놓으면 
함수를 호출할 때 모든 인자를 쓸 수도 있고, 일부를 생략할 수도 있다.
```kotlin
fun <T> Collection<T>.joinToString(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = "",
): String {
    ...
}
```
```kotlin
joinToString(list, ", ", "", "")
// 1, 2, 3

joinToString(list)
// 1, 2, 3

joinToString(list, "; ")
// 1, 2, 3
```

### 정적인 유틸리티 클래스 없애기: 최상위 함수와 프로퍼티
객체지향언어인 자바에서는 모든 코드를 클래스의 메소드로 작성해야 한다.
하지만 실전에서는 어느 한 클래스에 포함시키기 어려운 코드들도 생긴다.
또는 그 연산을 객체의 인스턴스 API에 추가해서 너무 크게만들고 싶지 않은 경우도 있다.
코틀린에서는 이런 클래스를 생성하지 않고, 직접 소스파일의 최상위 수준, 모든 다른 클래스의 밖에 위치시키면 된다.

```kotlin
package strings

fun joinToString(...): String {...} 
```

#### 최상위 프로퍼티
함수와 마찬가지로 프로퍼티도 파일의 최상위 수준에 놓을 수 있다.
어떤 데이터를 클래스 밖에 위치시켜야 하는 경우는 흔하지 않지만 유용할 때가 있다.

```kotlin
var opCount = 0 //최상위 프로퍼티 선언

fun performOperation() {
    ipCount++  // 최상위 프로퍼티 값 변경
}
```

### 메소드를 다른 클래스에 추가: 확장 함수와 확장 프로퍼티
코틀린에서는 **확장 함수**를 사용하여, 어떤 클래스의 멤버 메소드인 것처럼 호출할 수 있지만
그 클래스의 밖에 선언된 함수이다.

```kotlin
package strings

fun String.lastChar(): Char = this.get(this.length - 1)
```

확장함수를 만들려면 추가하려는 함수 이름 앞에 그 함수가 확장할 클래스의 이름을 덧붙이기만 하면 된다.
클래스 이름을 **수신 객체 타입** 이라 부르며, 확장 함수가 호출되는 대상이 되는 값(객체)을 **수신 객체**라고 부른다.

### 임포트와 확장 함수
확장함수를 정의했다고 해도 자동으로 프로젝트 안의 모든 소스코드에서 그 함수를 사용할 순 없다.
다른 클래스나 함수와 마찬가지로 임포트 해야한다.

### 자바에서 확장 함수 호출
내부적으로 확장 함수는 수신 객체를 첫 번째 인자로 받는 정적 메소드다.
확장함수를 호출해도 다른 어댑터 객체나 실행 시점 부가 비용이 들지 않는다.

확장 함수를 StringUtil.kt 파일에 정의했다면
자바에서는 아래와 같이 호출한다
```java
char c = StringUtilKt.lastChar("Java);
```

### 확장 함수로 유틸리티 함수 정의
```kotlin
fun <T> Collection<T>.joinToString(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = ""
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun Collection<String>.join(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = ""
) = joinToString(separator, prefix, postfix)

fun main(args: Array<String>) {
    println(listOf("one", "two", "eight").join(" "))
}
```

### 확장 함수는 오버라이드 할 수 없다
```kotlin
open class View {
    open fun click() = println("View clicked")
}

class Button: View() {
    override fun click() = println("Button clicked")
}

fun main(args: Array<String>) {
    val view: View = Button()
    view.click()
}
// Button clicked
```
Button이 View의 하위 타입이기 때문에 View 타입 변수를 선언해도 Button 타입 변수를 그 변수에 대입할 수 있다.
View 타입 변수에 대해 click과 같은 일반 메소드를 호출했는데, click을 Button 클래스가 오버라이드 했다면
실제로 Button이 오버라이드한 click이 호출된다.

하지만 확장함수를 오버라이드 할 수 없다
```kotlin
open class View {
    open fun click() = println("View clicked")
}

class Button: View() {
    override fun click() = println("Button clicked")
}

fun View.showOff() = println("I'm a view!")
fun Button.showOff() = println("I'm a button!")

fun main(args: Array<String>) {
    val view: View = Button()
    view.showOff()
}
/// I'm a view!
```

### 확장 프로퍼티
확장 프로퍼티를 사용하면 기존 클래스 객체에 대한 프로퍼티 형식의 구문으로 사용할 수 있는 API를 추가할 수 있다.
프로퍼티라는 이름으로 불리긴 하지만 상태를 저장할 적절한 방법이 없기 때문에(기존 클래스 인스턴스 객체에 필드를 추가할 방법은 없다) 실제로 확장 프로퍼티는 아무런 상태도 가질 수 없다.

하지만 프로퍼티 문법으로 더 짧게 코드를 작성할 수 있다.

확장 프로퍼티 선언
```kotlin
val String.lastChar: Char
    get() = get(length - 1)

println("Kotlin".lastChar)
/// n
```

변경 가능한 확장 프로퍼티 선언
```kotlin
var StringBuilder.lastChar: Char
    get() = get(length - 1)
    set(value: Char) {
        this.setCharAt(length - 1, value)
    }

val sb = StringBuilder("Kotlin?")
sb.lastChar = "!"
println(sb)
/// Kotlin!
```


## 컬렉션 처리: 가변 길이 인자, 중위 함수 호출, 라이브러리 지원
- `vararg` 키워드를 사용하면 호출시 인자 개수가 달라질 수 있는 함수를 정의할 수 있다
- 중위 함수 호출 구문을 사용하면 인자가 하나뿐인 메소드를 간편하게 호출할 수 있다
- 구조 분해 선언을 사용하면 복합적인 값을 분해해서 여러번 수에 나눠 담을 수 있다

### 가변 인자 함수: 인자의 개수가 달라질 수 있는 함수 정의
```kotlin
fun listOf<T>(vararg values: T): List<T> (...)

fun main(args: Array<String>) {
    val list = listOf("args: ", *args)  // 스프레드 연산자가 배열의 내용을 펼쳐준다
    println(list)
}
```

### 값의 쌍 다루기: 중위 호출과 구조 분해 선언
```kotlin
val map = mapOf(1 to "one", 7 to "seven", 53 to "fifty-three")
```

아래 to 함수는 Pair의 인스턴스를 반환한다.
Pair는 코틀린 표준 라이브러리 클래스이다.
```kotlin
infix fun Any.to(other:Any) = Pair(this, other)

val (number, name) = 1 to "One"
```
이러한 기능을 구조 분해 선언이라고 부른다

## 문자열과 정규식 다루기

### 문자열 나누기
자바에서 `"12.345-6.A".split(".")` 이 메소드는 빈 배열을 반환한다.
split의 구분 문자열은 실제로 정규식이기 때문이다.
마침표(.)는 정규식으로 해석된다

코틀린에서는 자바의 split대신에 여러가지 다른 조합의 파라미터를 받는 split 확장함수를 제공함으로써 
혼동을 야기하는 메소드를 감춘다
```kotlin
println("12.345-6.A".split("\\.|-".toRegex())) // 명시적으로 정규식을 만든다
/// [12,345,6,A]
```

### 정규식과 3중 따옴표로 묶은 문자열
path에서  처음부터 마지막 슬래시 직전까지 부분 문자열은 파일이 들어있는 디렉터리 경로이다.
path에서 마지막 마침표 다음부터 끝가지 부분 문자열은 파일 확장자다.
파일 이름은 두 위치 사이에 있다. 
```kotlin
fun parsePath(path: String) {
    val directory = path.substringBeforeLast("/")
    val fullName = path.substringAfterLast("/")

    val fileName = fullName.substringBeforeLast(".")
    val extension = fullName.substringAfterLast(".")

    println("Dir: $directory, name: $fileName, ext: $extension")
}

fun main(args: Array<String>) {
    parsePath("/Users/yole/chapter.adoc")
}
```

코틀린에서는 정규식을 사용하지 않고도 문자열을 쉽게 파싱할 수 있다
정규식은 강력하긴 하지만 나중에 알아보기 어려운 경우도 많다

이떼 정규식 라이브러리를 사용하면 더 편하다
```kotlin
un parsePath(path: String) {
    val regex = """(.+)/(.+)\.(.+)""".toRegex()
    val matchResult = regex.matchEntire(path)
    if (matchResult != null) {
        val (directory, filename, extension) = matchResult.destructured
        println("Dir: $directory, name: $filename, ext: $extension")
    }
}

fun main(args: Array<String>) {
    parsePath("/Users/yole/chapter.adoc")
}
```

### 여러 줄 3중 따옴표 문자열
```kotlin
val kotlinLogo = """| //
                   .|//
                   .|// """
```


## 요약
- 코틀린은 자체 컬렉션 클래스를 정의하지 않지만 자바 클래스를 확장해서 더 풍부한 API를 제공한다
- 함수 파라미터의 디폴트 값을 정의하면 오버로딩한 함수를 정의할 필요성이 줄어든다. 이름붙인 인자를 사용하면 함수의 인자가 많을 때 함수 호출의 가독성을 향상시킬 수 있다
- 코틀린 파일에서 클래스 멤버가 아닌 최상위 함수와 프로퍼티를 직접 선언할 수 있다
- 확장 함수와 프로퍼티를 사용하면 외부 라이브러리에 정의된 클래스를 포함해 모든 클래스 API를 그 클래스의 소스코드를 바꿀 필요 없이 확장할 수 있다
- 중위 호출을 통해 인자가 하나밖에 없는 메소드나 확장 함수를 더 깔끔한 구문으로 호출할 수 있다
- 코틀린은 정규식과 일반 문자열을 처리할 때 유용한 다양한 문자열 처리함수를 제공한다
- 자바 문자열로 표현하려면 이스케이프가 필요한 문자열의 경우 3중 따옴표 문자열을 사용하면 더 깔끔하게 표현할 수 있다
