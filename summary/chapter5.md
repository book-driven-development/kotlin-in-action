# 람다로 프로그래밍

람다식 또는 람다는 기본적으로 다른함수에 넘길 수 있는 작은 코드 조각을 뜻한다.
람다를 쉽게 사용하면 공통 코드 구조를 라이브러리 함수로 뽑아낼 수 있다.

## Index

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->


## 람다 식과 멤버 참조

### 람다 소개: 코드 블록을 함수 인자로 넘기기
함수형 프로그래밍에서는 함수를 값처럼 다루는 접근방법을 택함으로써
클래스를 선언하고 그 클래스의 인스턴스를 함수에 넘기는 대신 함수형 언어에서는 함수를 직접 다른 함수에 전달할 수 있다.
람다식을 사용하면 코드는 더 간결해진다. 람다 식을 사용하면 함수를 선언할 필요가 없고 코드 블록을 직접 함수의 인자로 전달할 수 있다.

```kotlin
button.setOnClickListener(new OnClickListener()) {
    @Override
    public void onClick(View view){

    }
}
```

```kotlin
button.setOnClickListener { }
```
람다를 메소드가 하나뿐인 무명 객체 대신 사용할 수 있다.

### 람다와 컬랙션
```kotlin
data class Person(val name: String, val age: Int)

fun findTheOldest(people: List<Person>) {
    var maxAge = 0
    var theOldest: Person? = null
    for (person in people) {
        if (person.age > maxAge) {
            maxAge = person.age
            theOldest = person
        }
    }
    println(theOldest)
}
```

위 코드는 람다를 사용해 바꿀 수 있다.
```kotlin
val people = listOf(Person("Alice",29), Person("Bob",31))
people.maxBy { it.age } //나이 프로퍼티를 비교해서 값이 가장 큰 값 찾기
```

아래 코드도 동작은 같다.
```kotlin
people.maxBy(Person::age) // 멤버 참조를 사용해 컬렉션 검색하기
```

### 람다 식의 문법
람다는 값처럼 여기저기 전달할 수 있는 동작의 모음이다.
람다를 따로 선언해서 변수에 저장할 수도 있다.
하지만 함수에 인자로 넘기면서 바로 람다를 정의하는 경우가 대부분이다

코틀린 람다 식은 항상 중괄호로 둘러싸여 있다.
인자 목록 주변에 괄호가 없다는 사실을 꼭 기억하라.
화살표(->)가 인자목록과 람다 본문을 구분해준다.
```kotlin
val sum = { x:Int, y:Int -> x+y }
```

```kotlin
people.maxBy { p:Person -> p.age}
```

이름 붙인 인자를 사용해 람다로 넘기기도 가능
```kotlin
val people = listOf(Person("Alice", 29), Person("Bob", 31))
val names = people.joinToString(separator = " ",
                        transform = { p: Person -> p.name })
```

람다를 괄호 밖에 전달하기
```kotlin
people.joinToString(" ") { p: Person -> p.name}
```

```kotlin
people.maxBy { p:Person -> p.age } // 파라미터 타입을 명시
people.maxBy { p -> p.age } // 파라미터 타입을 생략(컴파일러가 추론)
```

디폴트 파라미터 이름 it 사용하기
```kotlin
people.maxBy { it.age }
```

람다를 변수에 저장할 땐,ㄴ 파라미터 타입을 추론할 문맥이 존재하지않는다.
따라서 파라미터 타입을 명시해야 한다.
```kotlin
val getAge = { p:Person -> p.age }
people.maxBy(getAge)
```

본문이 여러 줄로 이뤄진 경우 본문의 맨 마지막에 있는 식이 람다의 결과값이 된다.
```kotlin
val sum = { x:Int, y:Int -> 
    println("Computing sum of $x $y")
    x + y
}
```

### 현재 영역에 있는 변수 접근
자바 메소드 안에서 무명 내부 클래스를 정의할 때 메소드의 로컬 변수를 무명 내부 클래스에서 사용할 수 있다.
람다 안에서도 같은 일을 할 수 있다.

함수 파라미터를 람다 안에서 사용하기
```kotlin
fun printMessagesWithPrefix(messages: Collection<String>, prefix: String) {
    messages.forEach {
        println("$prefix $it")
    }
}

fun main(args: Array<String>) {
    val errors = listOf("403 Forbidden", "404 Not Found")
    printMessagesWithPrefix(errors, "Error:")
}
```

람다 안에서 바깥 함수의 로컬 변수 변경하기
```kotlin
fun printProblemCounts(responses: Collection<String>) {
    var clientErrors = 0
    var serverErrors = 0
    responses.forEach {
        if (it.startsWith("4")) {
            clientErrors++
        } else if (it.startsWith("5")) {
            serverErrors++
        }
    }
    println("$clientErrors client errors, $serverErrors server errors")
}

fun main(args: Array<String>) {
    val responses = listOf("200 OK", "418 I'm a teapot",
                           "500 Internal Server Error")
    printProblemCounts(responses)
}
```

코틀린에서는 자바와 달리 람다에서 람다 밖 함수에 있는 파이널이 아닌 변수에 접근할 수 있고, 그 변수를 변경할 수도 있다.
람다 안에서 사용하는 외부 변수를 '람다가 포획한 변수'라고 부른다.
기본적으로 함수 안에 정의된 로컬 변수의 생명주기는 함수가 반환되면 끝난다.
하지만 어떤 함수가 자신의 로컬 변수를 포획한 람다를 반환하거나 다른 변수에 저장한다면 로컬 변수의 생명주기와 함수의 생명주기가 달라질 수 있다.
포획한 변수가 있는 람다를 저장해서 함수가 끝난 뒤에 실행해도 람다의 본문 코드는 여전히 포획한 변수를 읽거나 쓸 수 있다.

```kotlin
class Ref<T>(var value: T)

>>> val counter = Ref(0)
>>> val inc = { counter.value++ } //공식적으로는 변경불가능한 변수(val)을 포획하였지만 그 변수가 가리키는 객체의 필드 값을 바꿀 수 있다
```

### 멤버 참조
넘기려는 코드가 이미 함수로 선언된 경우는 어떻게 해야할까?
그 함수를 호출하는 람다를 만들 수도 있지만 이는 중복이다.
코틀린에서는 자바 8과 마찬가지로 함수를 값으로 바꿀 수 있다.
이때 이중콜론 `::`을 사용한다.
```kotlin
val getAge = Person::age
```
`::`을 사용하는 식을 멤버 참조라고 부른다
멤버 참조는 프로퍼티나 메소드를 단 하나만 호출하는 함수 값을 만들어준다.

`Person::age`는 아래의 람다 식을 더 간략하게 표현한 것이다
```kotlin
val getAge = { person: Person -> person.age }
```

멤버 참조는 그 멤버를 호출하는 람다와 같은 타입이다.
따라서 아래 예처럼 자유롭게 바꿔 쓸 수 있다.
```kotlin
people.maxBy(Person::age) 
people.maxBy { p -> p.age }
people.maxBy { it.age }
```

최상위에 선언된(다른 클래스의 멤버가 아닌) 함수나 프로퍼티도 참조할 수 있다

```kotlin
fun salute() = println("Salute!")
>>> run(::salute)
```

람다 인자가 여렷인 경우 다른 함수한테 작업을 위임하는 경우 람다를 정의하지 않고 직접 위임 함수에 대한 참조를 제공하면 편리하다.
```kotlin
val action = { person:Person, message: String -> // 이 람다는 sendEmail 함수에게 작업을 위임한다
    sendEmail(person,message)
}

val nextAction = ::sendEmail //람다 대신 멤버 참조를 쓸 수 있다
```

생성자 참조를 사용하면 클래스 생성작업을 연기하거나 저장해둘 수 있다
`::`뒤에 클래스 이름을 넣으면 생성자 참조를 만들 수 있다.
```kotlin
val createPerson = ::Person  //Person의 인스턴스를 만드는 동작을 값으로 저장한다
val p = createPerson("Alice", 29)
```

확장 함수도 멤버 함수와 똑같은 방식으로 참조 가능
```kotlin
fun Person.isAdult() = age >= 21
val predicate = Person::isAdult
```


## 컬렉션 함수형 API

### 필수적인 함수: filter와 map
```kotlin
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4)
    println(list.filter { it % 2 == 0 })
}
```

```kotlin
fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 29), Person("Bob", 31))
    println(people.filter { it.age > 30 })
}
```

```kotlin
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4)
    println(list.map { it * it })
}
```

```kotlin
data class Person(val name: String, val age: Int)

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 29), Person("Bob", 31))
    println(people.map { it.name })
}
```

```kotlin
fun main(args: Array<String>) {
    val numbers = mapOf(0 to "zero", 1 to "one")
    println(numbers.mapValues { it.value.toUpperCase() })
}
```

### all, any, count, find: 컬렉션에 술어 적용
```kotlin
data class Person(val name: String, val age: Int)

val canBeInClub27 = { p: Person -> p.age <= 27 }

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 27), Person("Bob", 31))
    println(people.all(canBeInClub27))
}
```

```kotlin
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3)
    println(!list.all { it == 3 })
    println(list.any { it != 3 })
}
```

```kotlin
data class Person(val name: String, val age: Int)

val canBeInClub27 = { p: Person -> p.age <= 27 }

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 27), Person("Bob", 31))
    println(people.find(canBeInClub27))
}
```

### groupBy: 리스트를 여러 그룹으로 이뤄진 맵으로 변경
```kotlin
data class Person(val name: String, val age: Int)

fun main(args: Array<String>) {
    val people = listOf(Person("Alice", 31),
            Person("Bob", 29), Person("Carol", 31))
    println(people.groupBy { it.age })
}
// {29=[Person(name=Bob, age=29)],
// 31=[Person(name=Alice, age=31), Person(name=Carol, age=31)]}
```

```kotlin
fun main(args: Array<String>) {
    val list = listOf("a", "ab", "b")
    println(list.groupBy(String::first))
}
// {a=[a,ab], b=[b]}
```

### flatMap과 flatten: 중첩된 컬렉션 안의 원소 러치
flatMap 함수는 먼저 인자로 주어진 람다를 컬렉션의 모든 객체에 적용하고(또는 매핑하기)
람다를 적용한 결과 얻어지는 여러 리스트를 한 리스트로 한데 모은다.

```kotlin
fun main(args: Array<String>) {
    val strings = listOf("abc", "def")
    println(strings.flatMap { it.toList() })
}
// [a,b,c,d,e,f]
```

```kotlin
fun main(args: Array<String>) {
    val books = listOf(Book("Thursday Next", listOf("Jasper Fforde")),
                       Book("Mort", listOf("Terry Pratchett")),
                       Book("Good Omens", listOf("Terry Pratchett",
                                                 "Neil Gaiman")))
    println(books.flatMap { it.authors }.toSet())
}
// [Jasper Fforde, Terry Pratchett, Neil Gaiman]
```


## 지연 계산(lazy) 컬렉션 연산
map이나 filter같은 몇가지 컬렉션 함수는 결과 컬렉션을 즉시 생성한다.
이는 컬렉션 함수를 연쇄하면 매 단계마다 계산 중간 결과를 새로운 컬렉션에 임시로 담는다는 말이다.
시퀀스<sup>sequence</sup>를 사요하면 중간 임시 컬렉션을 사용하지 않고도 컬렉션 연산을 연쇄할 수 있다.

### 시퀀스 연산 실행: 중간 연산과 최종 연산
시퀀스에 대한 연산은 **중간연산**과 **최종연산**으로 나뉜다.
중간연산은 항상 지연 계산된다.

```kotlin
    listOf(1, 2, 3, 4).asSequence() 
            .map { print("map($it) "); it * it }
            .filter { print("filter($it) "); it % 2 == 0 }
```
위 코드를 실행하였을 때 아무 내용도 출력되지 않는다.
map과 filter 변환이 늦춰져서 결과를 얻을 필요가 있을때 적용된다는뜻이다

```kotlin
    listOf(1, 2, 3, 4).asSequence()   //원본 컬렉션을 시퀀스로 변경
            .map { print("map($it) "); it * it }
            .filter { print("filter($it) "); it % 2 == 0 }
            .toList()
```
최종연산을 호출하면 연기되었던 모든 계산이 수행된다.

### 시퀀스 만들기
`asSequence()`를 호출하지않고 `generateSequence()` 함수를 사용할 수도 있다.
```kotlin
val naturalNumbers = generateSequence(0) { it + 1 }
val numbersTo100 = naturalNumbers.takeWhile { it <= 100 }
println(numbersTo100.sum())
```

상위 디렉터리의 시퀀스를 생성하고 사용하기
```kotlin
fun File.isInsideHiddenDirectory() =
        generateSequence(this) { it.parentFile }.any { it.isHidden }

fun main(args: Array<String>) {
    val file = File("/Users/svtk/.HiddenDir/a.txt")
    println(file.isInsideHiddenDirectory())
}
```


## 자바 함수형 인터페이스 활용
```kotlin
button.setOnClickListener { v-> ....} 
```
위와 같이 무명 클래스 대신 람다를 넘길 수 있는 이유는
OnClickListener에 추상 메소드가 단 하나만 있기 때문이다
```java
public interface OnClickListsner {
    void onClick(View v);
}
```
이런 인터페이스 들을 **함수형 인터페이스** 또는 **SAM**<sup>single abstract method</sup> **인터페이스** 라 한다.

### 자바 메소드에 람다를 인자로 전달
함수형 인터페이스를 인자로 원하는 자바 메소드에 코틀린 람다를 전달할 수 있다.

```java
void postponeComputation(int delay, Runnable computation);
```

```kotlin
postponeComputation(1000, object Runnable {
    override fun run(){
        println(42)
    }
})
```

```kotlin
postponeComputation(1000) { println(42) }
```

### SAM 생성자: 람다를 함수형 인터페이스로 명시적으로 변경
함수형 인터페이스의 인스턴스를 반환하는 메소드가 있다면 람다를 직접 반환할 수 없고,
반환하고픈 람다를 SAM 생성자로 감싸야 한다.

SAM 생성자를 사용해 빈 값 반환하기
```kotlin
fun createAllDoneRunnable(): Runnable {
    return Runnable { println("All done!") }
}

fun main(args: Array<String>) {
    createAllDoneRunnable().run()
}
```

SAM 생성자를 사용해 listener 인스턴스 재사용하기
```kotlin
val listsner = OnClickListsner { v->
    val text = when(view.id) {
        R.id.button1 -> "First button"
        R.id.button2 -> "Second button"
        else -> "Unknown button"
    }
    toast(text)
} 
```


## 수신 지정 객체 람다: with와 apply

### with 함수
어떤 객체의 이름을 반복하지않고 다양한 연산을 수행하기 위해서는 with을 사용할 수 있다.
```kotlin
fun alphabet() = with(StringBuilder()) {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
    toString()
}
```

### apply 핰수
apply 함수는 거의 with과 같다
유일한 차이란 apply는 항상 자신에게 전달된 객체를 반환한다는 점이다.

```kotlin
fun alphabet() = buildString {
    for (letter in 'A'..'Z') {
        append(letter)
    }
    append("\nNow I know the alphabet!")
}
```


## 요약
- 람다를 사용하면 코드 조각을 다른 함수에게 인자로 넘길 수 있다
- 코틀린에서는 람다가 함수 인자인 경우 괄호 밖으로 람다를 빼낼 수 있고, 람다의 인자가 단 하나뿐인 경우 인자의 이름을 지정하지 않고 it이라는 디폴트 이름으로 부를 수 있다
- 람다 안에 있는 코드는 그 람다가 들어있는 바깥 함수의 변수를 읽거나 쓸 수 있다
- 메소드 생선자 프로퍼티 이름 앞에 `::`을 붙이면 각각에 대한 참조를 만들 수 있다. 그런 참조를 람다 대신 다른 함수에게 넘길 수 있다
- filter, map, all, any 등의 함수를 활용하면 컬렉션에 대한 대부분의 연산을 직접 원소를 이터레이션 하지 않고 수행할 수 있다
- 시퀀스를 사용하면 중간 결과를 담는 컬렉션을 생성하지 않고도 컬렉션에 대한 여러 연산을 조합할 수 있다
- 함수형 인터페이스를 인자로 받는 자바 함수를 호출할 경우 람다를 함수형 인터페이스 인자 대신 넘길 수 있다
- 수신 객체 지정 람다를 사용하면 람다 안에서 미리 정해둔 수신 객체의 메소드를 직접 호출할 수 있다
- 표준 라이브러리의 with 함수를 사용하면 어떤 객체에 대한 참조를 반복해서 언급하지 않으면서 그 객체의 메소드를 호출할 수 있다.
apply를 사용하면 어던 객체라도 빌더 스타일의 API를 사용해 생성하고 초기화 할 수 있다

