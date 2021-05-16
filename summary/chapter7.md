# 연산자 오버로딩과 기타 관계

## Index

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->


## 산술 연산자 오버로딩
자바에서는 원시타입에 대해서만 산술 연산자를 사용할 수 있고,
추가로 String에 대해 `+` 연산자를 사용할 수 있다.
코틀린에서 어떻게 클래스에 대한 일반 산술 연산자를 사용하는지 살펴보자

### 이항 산술 연산 오버로딩  
연산자를 오버로딩 하는 함수 앞에는 `operator` 키워드가 반드시 있어야 한다.
`operator` 함수를 사용 함으로써 어떤 함수가 관례를 따르는 함수인지 명확히 할 수 있다.
`operator` 키워드를 추가해 plus 함수를 추가하고 나면 `+` 기호로 두 Point 객체를 더할 수 있다.
```kotlin
data class Point(val x: Int, val y: Int) {
    operator fun plus(other: Point): Point { //operator 연산자 사용
        return Point(x + other.x, y + other.y)
    }
}

fun main(args: Array<String>) {
    val p1 = Point(10, 20)
    val p2 = Point(30, 40)
    println(p1 + p2) // operator 연산자 사용
}
```

확장 함수에도 사용할 수 있다
```kotlin
operator fun Point.plus(other: Point): Point {
    return Point(x + other.x, y + other.y)
}
```

#### 오버로딩 가능한 이항 산술 연산자
직접 정의한 함수를 통해 구현하더라도 연산자 우선순위는 언제나 표준 숫자 타입에 대한
연산자 우선순위와 같다.
|식|함수 이름|
|--|--|
|a*b|times|
|a/b|div|
|a%b|mod(1.1부터 rem)|
|a+b|plus|
|a-b|minus|


#### 두 피연산자의 타입이 다른 연산자 정의하기
두 피연산자가 같은 타입일 필요는 없다.
```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.times(scale: Double): Point {
    return Point((x * scale).toInt(), (y * scale).toInt())
}

fun main(args: Array<String>) {
    val p = Point(10, 20)
    println(p * 1.5)
}
```

#### 결과 타입이 피연산자 타입과 다른 연산자 정의하기
```kotlin
operator fun Char.times(count: Int): String {
    return toString().repeat(count)
}

fun main(args: Array<String>) {
    println('a' * 3)
}
```

#### 비트 연산자
코틀린은 표준 숫타 타입에 대해 비트 연산자를 정의하지 않는다
대신 중위 연산자 표기법을 지원하는 일반함수를 사용해 비트연산을 수행한다.
커스텀 타입에서도 그와 비슷한 함수를 정의해 사용할 수 있다.

- `shl` - 왼쪽 시프트(자바 `<<`)
- `shr` - 오른쪽 시프트(부호 비트 유지, 자바`>>`)
- `ushr` - 오른쪽 시프트(0으로 부호 비트 설정, 자바`>>>`)
- `and` - 비트 곱(자바 `&`)
- `or` - 비트 합(자바 `|`)
- `xor` - 비트 배타 합(자바 `^`)
- `inv` - 비트 반전(자바 `-`)

### 복합 대입 연산자 오버로딩
plus와 같은 연산자를 오버로딩하면 코틀린은 `+` 연산자뿐 아니라 그와 관련있는 연산자인
`+=`도 자동으로 함께 지원한다. 이를 **복합 대입 연산자**라 부른다.
```kotlin
operator fun Point.plus(other: Point): Point {
    return Point(x + other.x, y + other.y)
}

fun main(args: Array<String>) {
    var point = Point(1, 2)
    point += Point(3, 4)
    println(point)
}
```

```kotlin
fun main(args: Array<String>) {
    val numbers = ArrayList<Int>()
    numbers += 42
    println(numbers[0])
}
>> 42
```

```kotlin
fun main(args: Array<String>) {
    val list = arrayListOf(1, 2)
    list += 3
    val newList = list + listOf(4, 5)
    println(list)
    println(newList)
}
>> [1,2,3]
>> [1,2,3,4,5]
```

### 단항 연산자 오버로딩
단항 연산자도 미리 정해진 이름의 함수를 선언하면서 `operator` 키워드로 선언한다
```kotlin
operator fun Point.unaryMinus(): Point {
    return Point(-x, -y)
}

fun main(args: Array<String>) {
    val p = Point(10, 20)
    println(-p)
}
```

```kotlin 
//증가연산자
operator fun BigDecimal.inc() = this + BigDecimal.ONE

fun main(args: Array<String>) {
    var bd = BigDecimal.ZERO
    println(bd++)
    println(++bd)
}
```

#### 오버로딩 할 수 있는 단항 산술 연산자
|식|함수이름|
|--|--|
|+a|unaryPlus|
|-a|unaryMinus|
|!a|not|
|++a, a++|inc|
|--a, a--|dec|


## 비교 연산자 오버로딩
산술 연산자와 마찬가지로 원시 타입 뿐 아니라 모든 객체에 대해 비교 연산을 수행할 수 있다.
코틀린에서는 == 비교연산자를 직접 사용하면 된다.

### 동등 연산자: equals
```kotlin
class Point(val x: Int, val y: Int) {
    override fun equals(obj: Any?): Boolean {
        if (obj === this) return true
        if (obj !is Point) return false
        return obj.x == x && obj.y == y
    }
}

fun main(args: Array<String>) {
    println(Point(10, 20) == Point(10, 20))
    println(Point(10, 20) != Point(5, 5))
    println(null == Point(1, 2))
}
>> true
>> true
>> false
```

**식별자비교** 연산자(`===`)를 사용해 equals의 파라미터가 수신 객체와 같은지 살펴본다
식별자 비교 연산자는 자바의 `==` 연산과 같다.
따라서 `===`는 자신의 두 피연산자가 서로 같은 객체를 가리키는지(원시타입인 경우 두 값이 같은지)를 비교한다

### 순서 연산자: compareTo
자바에서는 `Comparable` 인터페이스를 구현하며, `Comparable`에 들어있는 `compareTo` 메소드는 
한 객체와 다른 객체의 크기를 비교해 정수로 나타내 준다.

코틀린도 똑같이 `Comparable` 인터페이스를 지원한다.
코틀린의 비교 연산자 (`<`, `>`, `<=`, `>=`)는 compareTo 호출로 컴파일 된다.

```kotlin
class Person(
        val firstName: String, val lastName: String
) : Comparable<Person> {

    override fun compareTo(other: Person): Int {
        return compareValuesBy(this, other,
            Person::lastName, Person::firstName)
    }
}

fun main(args: Array<String>) {
    val p1 = Person("Alice", "Smith")
    val p2 = Person("Bob", "Johnson")
    println(p1 < p2)
}
>> false
```


## 컬렉션과 범위에 대해 쓸 수 있는 관례
컬렉션을 다룰 때 인덱스를 사용해 원소를 읽거나 쓰는 연산,
어떤 값이 컬렉션에 속해있는지 검사하는 연산을 많이 쓴다.

### 인덱스로 원소에 접근: get과 set
코틀린에서 맵의 원소에 접근할때나, 자바의 배열 원소에 접근할 때 `[ ]`를 사용한다.

```kotlin
data class Point(val x: Int, val y: Int)

operator fun Point.get(index: Int): Int {
    return when(index) {
        0 -> x
        1 -> y
        else ->
            throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

fun main(args: Array<String>) {
    val p = Point(10, 20)
    println(p[1]) //각 괄호로 원소에 접근
}
```

```kotlin
data class MutablePoint(var x: Int, var y: Int)

operator fun MutablePoint.set(index: Int, value: Int) {
    when(index) {
        0 -> x = value
        1 -> y = value
        else ->
            throw IndexOutOfBoundsException("Invalid coordinate $index")
    }
}

fun main(args: Array<String>) {
    val p = MutablePoint(10, 20)
    p[1] = 42 //각 괄호로 원소에 set
    println(p)
}
```

### in 관례
```kotlin
data class Point(val x: Int, val y: Int)

data class Rectangle(val upperLeft: Point, val lowerRight: Point)

operator fun Rectangle.contains(p: Point): Boolean {
    return p.x in upperLeft.x until lowerRight.x &&
           p.y in upperLeft.y until lowerRight.y
}

fun main(args: Array<String>) {
    val rect = Rectangle(Point(10, 20), Point(50, 50))
    println(Point(20, 30) in rect) //rect에 속하는지
    println(Point(5, 5) in rect)  //rect에 속하는지
}
>> true
>> false
```

### rangeTo 관례
범위를 만드려면 `...` 구문을 사용한다.
예를들어 `1..10`은 1부터 10까지 모든수가 들어있는 범위를 가리킨다.
```kotlin
fun main(args: Array<String>) {
    val n = 9
    println(0..(n + 1))
    (0..n).forEach { print(it) }
}
```

```kotlin
// 날짜의 범위 다루기
val newYear = LocalDate.ofYearDay(2017, 1)
val daysOff = newYear.minusDays(1)..newYear
for (dayOff in daysOff) { println(dayOff) }
```

### for 루프를 위한 iterator 관례

클래스 안에 직접 iterator 메소드를 구현할 수도 있다.
```kotlin
operator fun ClosedRange<LocalDate>.iterator(): Iterator<LocalDate> =
        object : Iterator<LocalDate> {
            var current = start

            override fun hasNext() =
                current <= endInclusive

            override fun next() = current.apply {
                current = plusDays(1)
            }
        }
```


## 구조 분해 선언과 component 함수
구조 분해를 사용하면 복합적인 값을 분해해서 여러 다른 변수를 한번에 초기화 할 수 있다.

```kotlin
val (a, b) = p

 ↓↓↓↓↓↓↓↓↓↓↓

val a = p.component1()
val b = p.component2()
```

data 클래스의 주 생성자에 들어있는 프로퍼티는 컴파일러가 자동으로 
componentN을 만들어 준다

```kotlin
class point(val x: Int, val y: Int) {
    operator fun component1() = x
    operator fun component2() = y
}
```

구조 분해 선언은 여러 값을 반환할 때 유용하다.
여러 값을 한꺼번에 반환해야 하는 함수가 있다면 반환해야 하는 모든 값이 들어갈 데이터 클래스를 정의하고
함수의 반환 타입을 그 데이터 클래스로 바꾼다.
구조 분해 선언 구문을 사용하면 이런 함수가 반환하는 값을 쉽게 풀어서 여러 변수에 넣을 수 있다.
```kotlin
data class NameComponents(val name: String,
                          val extension: String)

fun splitFilename(fullName: String): NameComponents {
    val result = fullName.split('.', limit = 2)
    return NameComponents(result[0], result[1])
}

fun main(args: Array<String>) {
    val (name, ext) = splitFilename("example.kt")
    println(name)
    println(ext)
}
```

무한히 componentN을 선언할 수는 없으므로
코틀린 표준 라이브러리에서는 맨 앞의 다섯 원소에 대한 componentN을 제공한다

### 구조 분해 선언과 루프
함수 본문 내의 선언문뿐 아니라 변수 선언이 들어갈 수 있는 장소라면 어디든 구조분해 선언을 사용할 수 있다

```kotlin
fun printEntries(map: Map<String, String>) {
    for ((key, value) in map) { // 루프 변수에 구조 분해 선언 사용
        println("$key -> $value")
    }
}

fun main(args: Array<String>) {
    val map = mapOf("Oracle" to "Java", "JetBrains" to "Kotlin")
    printEntries(map)
}
```


## 프로퍼티 접근자 로직 재활용: 위임 프로퍼티
코틀린이 제공하는 관례에 의존하는 특성중 독특하면서 강력한 기능인 **위임 프로퍼티**에 대하여 알아본다
위임 프로퍼티를 사용하면 값을 뒷받침 하는 필드에 단순히 저장하는 것보다 더 복잡한 방식으로 작동하는 프로퍼티를 쉽게 구현할 수 있다.
또 그 과정에서 접근자 로직을 매번 재구현 할 필요도 없다.

### 위임 프로퍼티 소개

```kotlin
class Foo {
    private val delegate = Delegate() // 컴파일러가 생성한 도무이 프로퍼티
    var p: Type                       // p 프로퍼티를 위해 컴파일러가 생성한 접근자는 `delegate`의 getValue와 setValue 메소드를 호출한다   
    set(value: Type) = delegate.setValue(..., value)
    get() = delegate.getValue(...)
}
```

```kotlin
class Delegate {
    operator fun getValue(...) {  }
    operator fun setValue(...) {  } 
}

class Foo {
    var p: Type by Delegate() // by 키워드는 프로퍼티와 위임 객체를 연결한다
}
```

### 위임 프로퍼티 사용: by lazy()를 사용한 초기화 지연
**지연 초기화**는 객체 일부분을 초기화하지 않고 남겨뒀다가 실제로 그 부분의 값이 필요할 경우 초기화 할때 흔히 사용ㅇ한다.
```kotlin
class Email { /*...*/ }
fun loadEmails(person: Person): List<Email> {
    println("Load emails for ${person.name}")
    return listOf(/*...*/)
}

class Person(val name: String) {
    private var _emails: List<Email>? = null 

    val emails: List<Email>
       get() {
           if (_emails == null) {
               _emails = loadEmails(this) //최초 접근시 이메일 가져온다
           }
           return _emails!! // 저장해 둔 데이터가 있으면 그 데이터 반환
       }
}

fun main(args: Array<String>) {
    val p = Person("Alice")
    p.emails // 최초로 emails를 읽을때 단 한번만 이메일을 가져온다
    p.emails
}
```

여기서 **뒷받침하는 프로퍼티** 라는 기법을 사용한다.
`_emails` 라는 프로퍼티는 값을 저장하고 다른 프로퍼티인 `emails`는 `_emails`라는 프로퍼티에 대한 읽기 연산을 제공한다
`_emails`는 널이 될 수 있는 반면 `emails`는 널이 될 수 없는 타입이다.

위 코드는 코틀린의 지연 초기화 위임 프로퍼티를 통해 쉽게 구현 가능하다
```kotlin
class Person(val name: String) {
    val emails by lazy { loadEmails(this)}
}
```

### 위임 프로퍼티 구현
1)
```kotlin
PropertyChangeSupport 클래스는 리스너 목록을 관리하고 이벤트가 들어오면 모든 목록의 리스너에게 이벤트를 통지한다
open class PropertyChangeAware {
    protected val changeSupport = PropertyChangeSupport(this)

    fun addPropertyChangeListener(listener: PropertyChangeListener) {
        changeSupport.addPropertyChangeListener(listener)
    }

    fun removePropertyChangeListener(listener: PropertyChangeListener) {
        changeSupport.removePropertyChangeListener(listener)
    }
}

class Person(
        val name: String, age: Int, salary: Int
) : PropertyChangeAware() {

    var age: Int = age
        set(newValue) {
            val oldValue = field  //뒷받침하는 필드에 접근할 때 field식별자 사용
            field = newValue
            changeSupport.firePropertyChange(  // 프로퍼티 변경을 리스너에게 통지
                    "age", oldValue, newValue)
        }

    var salary: Int = salary
        set(newValue) {
            val oldValue = field
            field = newValue
            changeSupport.firePropertyChange(
                    "salary", oldValue, newValue)
        }
}

fun main(args: Array<String>) {
    val p = Person("Dmitry", 34, 2000)
    p.addPropertyChangeListener(
        PropertyChangeListener { event ->
            println("Property ${event.propertyName} changed " +
                    "from ${event.oldValue} to ${event.newValue}")
        }
    )
    p.age = 35
    p.salary = 2100
}
```


2) 도우미 클래스를 통해 프로퍼티 변경 통지 구현하기
```kotlin

open class PropertyChangeAware {
    protected val changeSupport = PropertyChangeSupport(this)

    fun addPropertyChangeListener(listener: PropertyChangeListener) {
        changeSupport.addPropertyChangeListener(listener)
    }

    fun removePropertyChangeListener(listener: PropertyChangeListener) {
        changeSupport.removePropertyChangeListener(listener)
    }
}

class ObservableProperty(
    val propName: String, var propValue: Int,
    val changeSupport: PropertyChangeSupport
) {
    fun getValue(): Int = propValue
    fun setValue(newValue: Int) {
        val oldValue = propValue
        propValue = newValue
        changeSupport.firePropertyChange(propName, oldValue, newValue)
    }
}

class Person(
    val name: String, age: Int, salary: Int
) : PropertyChangeAware() {

    val _age = ObservableProperty("age", age, changeSupport)
    var age: Int
        get() = _age.getValue()
        set(value) { _age.setValue(value) }

    val _salary = ObservableProperty("salary", salary, changeSupport)
    var salary: Int
        get() = _salary.getValue()
        set(value) { _salary.setValue(value) }
}

fun main(args: Array<String>) {
    val p = Person("Dmitry", 34, 2000)
    p.addPropertyChangeListener(
        PropertyChangeListener { event ->
            println("Property ${event.propertyName} changed " +
                    "from ${event.oldValue} to ${event.newValue}")
        }
    )
    p.age = 35
    p.salary = 2100
}
```

3) ObservableProperty를 프로퍼티 위임에 사용할 수 있도록 바꾼 모습

```kotlin
class ObservableProperty(
    var propValue: Int, val changeSupport: PropertyChangeSupport
) {
    operator fun getValue(p: Person, prop: KProperty<*>): Int = propValue

    operator fun setValue(p: Person, prop: KProperty<*>, newValue: Int) {
        val oldValue = propValue
        propValue = newValue
        changeSupport.firePropertyChange(prop.name, oldValue, newValue)
    }
}
```

4) 위임 프로퍼티를 통해 프로퍼티 변경 통지 받기
```kotlin
class Person(
    val name: String, age: Int, salary: Int
) : PropertyChangeAware() {

    var age: Int by ObservableProperty(age, changeSupport)
    var salary: Int by ObservableProperty(salary, changeSupport)
}
```

### 위임 프로퍼티 컴파일 규칙
```kotlin
val x = c.prop --->  val x <delegate>.getValue(c <property>)

c.prop = x     --->  <delegate>.setValue(c, <property>, x)
```

### 프로퍼티 값을 맵에 저장
자신읠 프로퍼티를 동적으로 정의할 수 있는 객체를 만들 때 위임 프로퍼티를 활용하는 경우가 있다
이런 객체를 **확장 가능한 객체**라고 부르기도 한다.
```kotlin
class Person {
    private val _attributes = hashMapOf<String, String>()

    fun setAttribute(attrName: String, value: String) {
        _attributes[attrName] = value
    }

    val name: String by _attributes // 위임 프로퍼티로 맵을 사용한다
}

fun main(args: Array<String>) {
    val p = Person()
    val data = mapOf("name" to "Dmitry", "company" to "JetBrains")
    for ((attrName, value) in data)
       p.setAttribute(attrName, value)
    println(p.name)
}
```


## 요약
- 코틀린에서 정해진 이름의 함수를 오버라이딩함으로써 표준 수학 연산자를 오버로딩 할 수 있다. 직접 새로 만들 수는 없다
- 비교 연산자는 `equals`와 `compareTo` 메소드로 변환된다
- 클래스에 `get`, `set`, `contains`라는 함수를 정의하면 그 클래스의 인스턴스에 대해 `[]`와 `in`을 사용할 수 있고, 그 객체를 코틀린 컬렉션 객체와 비슷하게 다룰 수 있다
- 미리 정해진 관례를 따라 `rangeTo`, `iterator` 함수를 정의하면 범위를 만들거나 컬렉션과 배열의 원소를 이터레이션 할 수 있다
- 구조 분해 선언을 통해 한 객체의 상태를 분해해서 여러 변수에 대입할 수 있다. 데이터 클래스에 대한 구조분해는 거저 사용할 수 있지만, 커스텀 클래스의 인스턴스에서 사용하려면 `componentN` 함수를 정의해야 한다
- 위임 프로퍼티를 통해 프로퍼티 값을 저장하거나 초기화하거나 일거나 변경할 때 사용하는 로직을 재활용 할 수 있다
- 표준 라이브러리 함수인 `lazy`를 통해 지연 초기화 프로퍼티를 쉽게 구현할 수 있다
- Delegates.observable함수를 사용하면 프로퍼티 변경을 관찰할 수 있는 관찰자를 쉽게 추가할 수 있다
- 맵을 위임객체로 사용하는 위임 프로퍼티를 통해 다양한 속성을 제공하는 객체를 유연하게 다룰 수 있다