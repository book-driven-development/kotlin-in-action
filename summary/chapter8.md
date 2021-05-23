# 고차 함수: 파라미터와 반환 값으로 람다 사용

## Index

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [고차 함수: 파라미터와 반환 값으로 람다 사용](#고차-함수-파라미터와-반환-값으로-람다-사용)
  - [Index](#index)
  - [고차 함수 정의](#고차-함수-정의)
    - [함수 타입](#함수-타입)
    - [인자로 받은 함수 호출](#인자로-받은-함수-호출)
    - [자바에서 코틀린 함수 타입 사용](#자바에서-코틀린-함수-타입-사용)
    - [디폴트 값을 지정한 함수 타입 파라미터나 널이 될 수 있는 함수 타입 파라미터](#디폴트-값을-지정한-함수-타입-파라미터나-널이-될-수-있는-함수-타입-파라미터)
    - [함수에서 함수를 반환](#함수에서-함수를-반환)
    - [람다를 활용한 중복 제거](#람다를-활용한-중복-제거)
  - [인라인 함수: 람다의 부가 비용 없애기](#인라인-함수-람다의-부가-비용-없애기)
    - [인라이닝이 작동하는 방식](#인라이닝이-작동하는-방식)
    - [인라인 함수의 한계](#인라인-함수의-한계)
    - [컬렉션 연란 인라이닝](#컬렉션-연란-인라이닝)
    - [함수를 인라인으로 선언해야 하는 경우](#함수를-인라인으로-선언해야-하는-경우)
    - [자원 관리를 위해 인라인된 람다 사용](#자원-관리를-위해-인라인된-람다-사용)
  - [고차 함수 안에서 흐름 제어](#고차-함수-안에서-흐름-제어)
    - [람다 안의 return문: 람다를 둘러싼 함수로부터 반환](#람다-안의-return문-람다를-둘러싼-함수로부터-반환)
    - [람다로부터 반환: 레이블을 사용한 return](#람다로부터-반환-레이블을-사용한-return)
    - [무명 함수: 기본적으로 로컬 return](#무명-함수-기본적으로-로컬-return)
  - [요약](#요약)

<!-- /code_chunk_output -->


## 고차 함수 정의
고차 함수는 다른 함수를 인자로 받거나 함수를 반환하는 함수다.
코틀린에서 고차함수는 람다나 함수 참조를 인자로 넘길 수 있거나 람다나 함수 참조를 반환하는 함수다.
함수를 인자로 받는 동시에 함수를 반환하는 함수도 고차함수다
ex) `list.filter { x > 0}`

### 함수 타입
람다를 인자로 받는 함수를 정의하려면 먼저 람다 인자의 타입을 어덯게 선언할 수 있는지 알아야 한다.

람다를 로컬 변수에 대입
```kotlin
val sum = { x: Int, y: Int -> x + y} 
val action = println(42)
```
```kotlin
val sum: (Int, Int) -> Int = { x, y -> x + y}  // Int 파라미터를 2개 받아서 int 값을 반환하느 는 함수
val action: () -> Unit = println(42) // 아무 인자도 받지 않고 아무 값도 반환하지 않는 함수
```
```kotlin
var canReturnNull: (Int, Int) -> Int? = { x, y => null }
```
```kotlin
var funOrNull: ((Int, Int) -> Int)? =null //널이 될 수 있는 함수 타입 변수 정의
```
```kotlin
fun performRequest(
       url: String,
       callback: (code: Int, content: String) -> Unit // 함수 타입의 각 파라미터에 이름을 붙인다.
) {
    /*...*/
}

fun main(args: Array<String>) {
    val url = "http://kotl.in"
    performRequest(url) { code, content -> /*...*/ } // API에서 제공하는 이름을 람다에 사용할 수 있다
    performRequest(url) { code, page -> /*...*/ } // 원하는 다름 이용 사용 가능
}
```

### 인자로 받은 함수 호출
```kotlin
fun twoAndThree(operation: (Int, Int) -> Int) { // 함수 타입인 파라미터 선언
    val result = operation(2, 3) // 함수 타입 파라미터 호출
    println("The result is $result")
}


fun main(args: Array<String>) {
    twoAndThree { a, b -> a + b }
    twoAndThree { a, b -> a * b }
}
>> The result is 5
>> The result is 6
```
```kotlin
fun String.filter(predicate: (Char) -> Boolean): String {
    val sb = StringBuilder()
    for (index in 0 until length) {
        val element = get(index)
        if (predicate(element)) sb.append(element) // "predicate" 파라미터로 전달받은 함수를 호출
    }
    return sb.toString()
}

fun main(args: Array<String>) {
    println("ab1c".filter { it in 'a'..'z' }) // 람다를 "predicate" 파라미터로 전달
}
```

### 자바에서 코틀린 함수 타입 사용
컴파일된 코드 안에서 함수 타입은 일반 인터페이스로 바뀐다.
즉 함수 타입의 변수는 FunctionN 인터페이스를 구현하는 객체를 저장한다.
코틀린 표준 라이브러리는 함수 인자의 개수에 따라 Function0<R>(인자가 없는 함수), Function1<P1,R>(인자가 하나인 함수) 등의 인터페잏스를 제공한다.
각 인터페이스에는 invoke 메소드 정의가 하나 들어 있다.
invoke를 호출하면 함수를 실행할 수 있다.

```kotlin
/** 코틀린 선언 ** /
fun processTheAnswer(f: (Int) -> Int) {
    println(f(42))
}

/** 자바8 이후 사용 **/
processTheAnswer(number -> number + 1)

/** 자바8 이전 사용 **/
processTheAnswer(
    new Function1<Integer, Integer>() {
        @override
        public Integer invoke(Integer number) {
            System.out.println(number);
            return number + 1;
        }
    }
)
```
함수 타입인 변수는 인자 개수에 따라 적당한 FunctionN인터페이스를 구현하는 클래스의 인스턴스를 저장하며, 그 클래스의 invoke 메소드 본문에는 람다의 본문이 들어간다

자바 8 이전의 자바에서는 필요한 FunctionN 인터페이스의 invoke 메소드를 구현하는 무명클래스를 넘기면 된다.

### 디폴트 값을 지정한 함수 타입 파라미터나 널이 될 수 있는 함수 타입 파라미터
파라미터를 함수 타입으로 선언할 때도 디폴트 값을 정할 수 있다.
```kotlin
fun <T> Collection<T>.joinToString(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = "",
        transform: (T) -> String = { it.toString } //함수 타입 파라미터를 선언하면서 람다를 디폴트 값으로 지정
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        val str = transform?.invoke(element)
            ?: element.toString()
        result.append(str)
    }

    result.append(postfix)
    return result.toString()
}
```

```kotlin
fun <T> Collection<T>.joinToString(
        separator: String = ", ",
        prefix: String = "",
        postfix: String = "",
        transform: ((T) -> String)? = null // null이 될 수 있는 함수 타입의 파라미터 선언
): String {
    val result = StringBuilder(prefix)

    for ((index, element) in this.withIndex()) {
        if (index > 0) result.append(separator)
        val str = transform?.invoke(element)
            ?: element.toString()
        result.append(str)
    }

    result.append(postfix)
    return result.toString()
}
```

### 함수에서 함수를 반환
적절한 로직을 선택해서 함수로 반환하는 함수를 정의해 사용할 수 있다.
```kotlin
enum class Delivery { STANDARD, EXPEDITED }

class Order(val itemCount: Int)

fun getShippingCostCalculator(
        delivery: Delivery): (Order) -> Double {
    if (delivery == Delivery.EXPEDITED) {
        return { order -> 6 + 2.1 * order.itemCount }
    }

    return { order -> 1.2 * order.itemCount }
}

fun main(args: Array<String>) {
    val calculator =
        getShippingCostCalculator(Delivery.EXPEDITED)
    println("Shipping costs ${calculator(Order(3))}")
}
```
```kotlin
data class Person(
        val firstName: String,
        val lastName: String,
        val phoneNumber: String?
)

class ContactListFilters {
    var prefix: String = ""
    var onlyWithPhoneNumber: Boolean = false

    fun getPredicate(): (Person) -> Boolean { //함수를 반환하는 함수 정의
        val startsWithPrefix = { p: Person ->
            p.firstName.startsWith(prefix) || p.lastName.startsWith(prefix)
        }
        if (!onlyWithPhoneNumber) {
            return startsWithPrefix // 함수 타입의 변수 반환
        }
        return { startsWithPrefix(it)
                    && it.phoneNumber != null } // 람다 반환
    }
}

fun main(args: Array<String>) {
    val contacts = listOf(Person("Dmitry", "Jemerov", "123-4567"),
                          Person("Svetlana", "Isakova", null))
    val contactListFilters = ContactListFilters()
    with (contactListFilters) {
        prefix = "Dm"
        onlyWithPhoneNumber = true
    }
    println(contacts.filter(
        contactListFilters.getPredicate()))
}
```

### 람다를 활용한 중복 제거
중복을 람다를 사용하면 간결하고 쉽게 제거할 수 있다
아래 예시는 웹 사이트 방문 기록을 분석하는 예이다.

사이트 방문 데이터를 하드 코딩한 필터를 사용해 분석하기
```kotlin
data class SiteVisit(
    val path: String,
    val duration: Double,
    val os: OS
)

enum class OS { WINDOWS, LINUX, MAC, IOS, ANDROID }

val log = listOf(
    SiteVisit("/", 34.0, OS.WINDOWS),
    SiteVisit("/", 22.0, OS.MAC),
    SiteVisit("/login", 12.0, OS.WINDOWS),
    SiteVisit("/signup", 8.0, OS.IOS),
    SiteVisit("/", 16.3, OS.ANDROID)
)

val averageWindowsDuration = log
    .filter { it.os == OS.WINDOWS }
    .map(SiteVisit::duration)
    .average()

fun main(args: Array<String>) {
    println(averageWindowsDuration)
}
```

일반 함수를 통해 중복 제거하기
```kotlin
fun List<SiteVisit>.averageDurationFor(os: OS) =
        filter { it.os == os }.map(SiteVisit::duration).average()
```

복잡하게 하드코딩한 필터를 사용해 방문 데이터 분석하기
```kotlin
val averageMobileDuration = log
    .filter { it.os in setOf(OS.IOS, OS.ANDROID) }
    .map(SiteVisit::duration)
    .average()
```

고차 함수를 사용해 중복 제거하기
```kotlin
fun List<SiteVisit>.averageDurationFor(predicate: (SiteVisit) -> Boolean) =
        filter(predicate).map(SiteVisit::duration).average()
```

## 인라인 함수: 람다의 부가 비용 없애기
람다를 활용한 코드의 성능은 어떨까?
코틀린이 보통 람다를 무명 클래스로 컴파일하지만 그렇다고 람다 식을 사용할 때마다 새로운 클래스가 만들어 지지 않는다.
람다가 변수를 포획하면 람다가 생성되는 시점마다 새로운 무명 클래스 객체가 생긴다.
람다가 변수를 포획하는 경우 실행 시점에 무명 클래스 생성에 따른 부가 비용이 든다.
이런 부가비용을 줄이려면 inline 코드를 사용한다

### 인라이닝이 작동하는 방식
어떤 함수를 `inline`으로 선언하면 그 함수의 본문이 인라인 된다.
다른말로 함수를 호출하는 코드를 함수를 호출하는 바이트 코드 대신에 함수 본문을 번역한 바이트 코드로 컴파일 된다.

```kotlin
// 인라인 함수 정의하기
inline fun <T> synchronized(lock: Lock, action: () -> T) : T {
    lock.lock()
    try {
        return action()
    } finally {
        lock.unlock()
    }
}
```
synchronized를 `inline`으로 선언했으므로 synchronized를 호출하는 코드는 모두 자바의 synchronized문과 같아진다.

```kotlin
fun foo(l: Lock) {
    println("Before sync")

    synchronized(l) {
        println("Action")
    }
    println("After sync")
}
```
synchronized 함수의 본문 뿐 아니라 synchronized에 전달된 람다의 본문도 함께 인라이닝 된다는 점에 유의해야한다.
람다의 본문에 의해 만들어지는 바이트코드는 그 람다를 호출하는 코드정의의 일부분으로 간주되기 때문에 코틀린 컴파일러는 그 람다를 함수 인터페이스를 구현하는 무명 클래스로 감싸지 않는다.

### 인라인 함수의 한계
인라이닝을 하는 방식으로 인해 람다를 사용하는 모든 함수를 인라이닝 할 수는 없다.
함수가 인라이닝 될 때 그 함수에 인자로 전달된 람다 식의 본문은 결과 코드에 직접 들어갈 수 있다.
하지만 이렇게 람다가 본문에 직접 펼쳐지기 때문에 함수가 파라미터로 전달받은 람다를 본문에 사용하는 방식이 한정될 수밖에 없다; 함수 본문에서 파라미터로 받은 람다를 호출한다면 그 호출을 쉽게 람다 본문으로 바꿀 수 있다.
하지만 파라미터로 받은 람다를 다른 변수에 저장하고 나중에 그 변수를 사용한다면 람다를 표현하는 객체가 어딘가는 존재해야하기 때문에 람다를 인라이닝 할 수 없다.

일반적으로 인라인 함수의 본문에서 람다 식을 바로 호출하거나 람다 식을 인자로 전달받아 바로 호출하는 경우 그 람다를 인라이닝 할 수 있다.

```kotlin
fun <T,R> Sequence<T>.map(transform: (T) -> R): Sequence<R> {
    return TransformingSequence(this, transform)
}
```
이 map 함수는 transform 파라미터로 전달받은 함수 값을 호출하지 않는 대신
TransformingSequence 생성자에 그 함수 값을 넘긴다
TransformingSequence 생성자는 전달받은 람다를 프로퍼티로 저장한다.
이런 기능을 지원하려면 transform 인자를 일반적인(인라이닝하지않는) 함수 표현으로 만들 수 밖에 없다.
즉 여기서는 transform을 함수 인터페이스를 구현하는 무명 클래스 인스턴스로 만들어야 한다.

둘 이상의 람다를 인자로 받는 함수에서 일부 람다만 인라이닝하고 싶을 때도 있다.
예를들어 어떤 람다에 너무 많은 코드가 들어가거나 어떤 람다에 인라이닝을 하면 안되는 코드가 들어갈 수 있다면,
인라이닝 하면 안되는 람다 파라미터는 `noinline` 변경자를 파라미터 이름 앞에 붙여서 인라이닝을 금지할 수 있다.

```kotlin
inline fun (inlined: ()-> Unit, noinline notInlined: () -> Unit) {
    ...
}
```

### 컬렉션 연란 인라이닝
코틀린 filter 함수는 인라인 함수다
따라서 filter 함수의 바이트코드는 그 함수에 전달된 람다 본문의 바이트 코드와 함게 filter를 호출한 위치에 들어간다.
그 결과 filter를 써서 생긴 바이트코드와 뒤 예제에서 생긴 바이트코드는 거의 같다.
코틀린다운 연산을 컬렉션에 안전하게 사용할 수 있고, 코틀린이 제공하는 함수 인라이닝을 믿고 신경쓰지 않아도 된다.

람다를 사용해 컬렉션 거르기
```kotlin
data class Person(val name: String, val age: Int)

val people = listOf(Person("Alice", 29), Person("Bob", 31))

fun main(args: Array<String>) {
    println(people.filter { it.age < 30 })
}
```

filter와 map은 인라인 함수이다.
두 함수의 본문은 인라이닝 되며, 추가 객체나 클래스 생성은 없다.
람다식과 멤버참조를 사용한다.
```kotlin
data class Person(val name: String, val age: Int)

val people = listOf(Person("Alice", 29), Person("Bob", 31))

fun main(args: Array<String>) {
    println(people.filter { it.age > 30 }
                  .map(Person::name))
}
```

### 함수를 인라인으로 선언해야 하는 경우
람다를 인자로 받는 함수를 인라이닝 할 경우 이익이 많다

**장점**
-  인라이닝을 통해 없앨 수 있는 부가 비용이 상당하다
    - 함수 호출 줄임
    - 람다 표현 클래스와 람다 인스턴스에 해당하는 객체 생성 필요 없음
- JVM은 함수 호출과 람다를 인라이닝 인라이닝해 줄 정도로 똑똑하지 못하다
- 인라이닝을 사용하면 일반 람다에서는 사용할 수 없는 몇가지 기능을 사용할 수 있다

**주의할 점**
- 인라이닝은 코드크기에 주의해야한다
- 인라인 함수가 큰 경우, 함수의 본문에 해당하는 바이트코드를 모든 호출지점에 복사해놓고 나면 크기가 매우 커질 수 있다

### 자원 관리를 위해 인라인된 람다 사용
람다로 중복을 없앨 수 있는 일반적인 패턴 중 한 가지는 어떤 작업을 하기 전에 자원을 획득하고
작업을 마친 후 자원을 해제하는 자원관리다.
자원은 파일, 락, 데이터베이스 트랜잭션 등 여러 대상을 가리킬 수 있다.

use 함수를 자원관리에 사용하기
```kotlin
fun readFirstLineFromFile(path: String): String {
    BufferedReader(FileReader(path)).use { br ->
        return br.readLine()
    }
}
```
use 함수는 닫을 수 있는(closable) 자원에 대한 확장함수며, 람다를 인자로 받는다.
use는 람다를 호출한 다음 자원을 닫아준다.
정상 종료는 물론 예외가 발생하여도 확실히 자원을 닫는다
use는 인라인 함수이며, 성능에는 영향이 없다.

## 고차 함수 안에서 흐름 제어
루프와 같은 코드를 람다로 바꾸다보면 곧 return 문제에 부딪칠것이다.
람다안에서 return을 사용하면 어떻게 되는지 알아보자

### 람다 안의 return문: 람다를 둘러싼 함수로부터 반환
일반 루프의 안에서 return을 사용하면 lookForAlice 함수에서 반환된다
```kotlin
data class Person(val name: String, val age: Int)

val people = listOf(Person("Alice", 29), Person("Bob", 31))

fun lookForAlice(people: List<Person>) {
    for (person in people) {
        if (person.name == "Alice") {
            println("Found!")
            return
        }
    }
    println("Alice is not found")
}

fun main(args: Array<String>) {
    lookForAlice(people)
}
```

루프를 forEach로 바꾸어도 동일하다.
```kotlin
fun lookForAlice(people: List<Person>) {
    people.forEach {
        if (it.name == "Alice") {
            println("Found!")
            return //lookForAlice 함수에서 반환
        }
    }
    println("Alice is not found") // Alice가 없을 때만 문구 호출
}
```
람다 안에서  return을 사용하면 람다로부터만 반환되는 것이 아니라 
그 람다를 호출하는 함수가 실행을 끝내고 반환된다.
자신을 둘러싸고 있는 블록보다 더 바깥에 있는 다른 블록을 반환하게 만드는 return문을 **넌 로컬 return** 이라부른다.


### 람다로부터 반환: 레이블을 사용한 return
람다 식에서도 로컬 return 을 사용할 수 있다
람다 안에서 로컬 return은 for루프의 break와 비슷한 역할을 한다.
로컬 return dms 람다의 실행을 끝내고 람다를 호출한 코드의 실행을 계속 이어간다.
```kotlin
fun lookForAlice(people: List<Person>) {
    people.forEach label@{ //레이블 붙이기
        if (it.name == "Alice") return@label 
    }
    println("Alice might be somewhere") // 항상 이줄이 출력된다
}
```

함수 이름을 return 레이블로 사용하는 것도 가능하다.
```kotlin
fun lookForAlice(people: List<Person>) {
    people.forEach {
        if (it.name == "Alice") return@forEach
    }
    println("Alice might be somewhere") // 항상 이줄이 출력된다
}
```

### 무명 함수: 기본적으로 로컬 return
무명함수안에서 레이블이 붙지 않는 return 식은 무명 함수 자체를 반환시킬 뿐 
무명함수를 둘러싼 다른 함수를 반환시키지는 않는다  
```kotlin
fun lookForAlice(people: List<Person>) {
    people.forEach(fun (person) {
        if (person.name == "Alice") return
        println("${person.name} is not Alice")
    })
}
```

## 요약
- 함수 타입을 사용해 함수에 대한 참조를 담는 변수나 파라미터나 반환 값을 만들 수 있다
- 고차 함수는 다른 함수를 인자로 받거나 함수를 반환한다. 함수의 파라미터 타입이나 반환 타입으로 함수 타입을 사용하면 고차함수를 선언할 수 있다.
- 인라인 함수를 컴파일할 때 컴파일러는 그 함수의 본문과 그 함수에게 전달된 람다의 본문을 컴파일한 바이트 코드를 모든 함수 호출지점에 삽입해준다. 이렇게 만들어지는 바이트코드는 람다를 활용한 이라인 함수 코드를 풀어서 직접 쓴 경우와 비교할 때 아무 부가 비용이 들 지 않는다
- 고차 함수를 사용하면 컴포넌트를 이루는 각 부분의 코드를 더 잘 재사용 할 수 있다. 또 고차함수를 활용해 강력한 제네릭 라이브러리를 만들 수 있다
- 인라인 함수에서는 람다 안에 있는 return 문이 바깥쪽 함수를 반환시키는 **넌 로컬 return**을 사용할 수 있다
- 무명 함수는 람다 식을 대신할 수 있으며 return식을 처리하는 규칙이 일반 람다식과 다르다. 본문 여러곳에서 return 해야하는 코드 블럭을 만들어야 한다면 람다 대신 무명함수를 쓸 수 있다.