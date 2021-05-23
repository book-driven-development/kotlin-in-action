# 제네릭스

## Index

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [제네릭스](#제네릭스)
  - [Index](#index)
  - [제네릭 타입 파라미터](#제네릭-타입-파라미터)
    - [제네릭 함수와 프로퍼티](#제네릭-함수와-프로퍼티)
    - [제네릭 클래스 선언](#제네릭-클래스-선언)
    - [타입 파라미터 제약](#타입-파라미터-제약)
    - [타입 파라미터를 널이 될 수 없는 타입으로 한정](#타입-파라미터를-널이-될-수-없는-타입으로-한정)
  - [실행 시 제네릭스 동작: 소거된 타입 파라미터와 실체화된 타입 파라미터](#실행-시-제네릭스-동작-소거된-타입-파라미터와-실체화된-타입-파라미터)
    - [실행 시점의 제네릭: 타입 검사와 캐스트](#실행-시점의-제네릭-타입-검사와-캐스트)
      - [타입 소거의 한계](#타입-소거의-한계)
      - [스타 프로젝션](#스타-프로젝션)
    - [실체화한 타입 파라미터를 사용한 함수 선언](#실체화한-타입-파라미터를-사용한-함수-선언)
    - [실체화한 타입 파라미터로 클래스 참조 대신](#실체화한-타입-파라미터로-클래스-참조-대신)
    - [실체화한 타입 파라미터의 제약](#실체화한-타입-파라미터의-제약)
  - [변성: 제네릭과 하위 타입](#변성-제네릭과-하위-타입)
    - [변성이 있는 이유: 인자를 함수에 넘기기](#변성이-있는-이유-인자를-함수에-넘기기)
    - [클래스, 타입, 하위 타입](#클래스-타입-하위-타입)
    - [공변성: 하위 타입 관계를 유지](#공변성-하위-타입-관계를-유지)
      - [공변적인 클래스 정의](#공변적인-클래스-정의)
      - [무공변 클래스 정의](#무공변-클래스-정의)
    - [반공변성: 뒤집힌 하위 타입 관계](#반공변성-뒤집힌-하위-타입-관계)
    - [사용 지점 변성: 타입이 언급되는 지점에서 변성 지정](#사용-지점-변성-타입이-언급되는-지점에서-변성-지정)
    - [스타 프로젝션: 타입 인자 대신 * 사용](#스타-프로젝션-타입-인자-대신-사용)
      - [스타프로젝션 특징](#스타프로젝션-특징)
      - [검증기](#검증기)
  - [요약](#요약)

<!-- /code_chunk_output -->


## 제네릭 타입 파라미터
제네릭스를 사용하면 타입 파라미터를 받는 타입을 정의할 수 있다.
제네릭타입의 인스턴스를 만드려면 타입 파라미터를 구체적인 **타입인자**로 치환해야 한다.

예를들어 Map클래스는 키 타입과 값 타입을 타입파라미터로 받으므로 `Map<K,V>`가 된다.
이런 제네릭 클래스에 `Map<String,Person>`처럼 구체적인 타입을 타입인자로 넘기면 타입을 인스턴스화 할 수 있다

### 제네릭 함수와 프로퍼티
제네릭 함수를 호출할 때 반드시 구체적 타입으로 타입 인자를 넘겨야 한다.
컬렉션을 다루는 라이브러리 함수는 대부분 제네릭 함수다.
```kotlin
fun <T> List<T>.slice(indices: IntRange): List<T>
```

제네릭 함수 호출
```kotlin
fun main(args: Array<String>) {
    val letters = ('a'..'z').toList()
    println(letters.slice<Char>(0..2))
    println(letters.slice(10..13))
}
```

> **확장 프로퍼티만 제네릭하게 만들 수 있다**
> 일반(확장이 아닌) 프로퍼티는 타입 파라미터를 가질 수 없다. 클래스 프로퍼티에 여러 타입의 값을 저장할 수는 없으므로
제네릭한 일반 프로퍼티는 말이 되지 않는다. 일반 프로퍼티를 제네릭하게 정의하면 컴파일러가 다음과 같은 오류를 표시한다.
> `val <T> x =T =TODO()`
> // Error: type parameter of a property must be used in its receiver type

### 제네릭 클래스 선언
자바와 마찬가지로 코틀린에서도 타입 파라미터를 넣은 꺽쇠 기호 (`< >`)를 클래스(인터페이스) 이름 뒤에 붙이면 클래스(인터페이스를 제네릭하게 만들 수 있다. 타입 파라미터를 이름 뒤에 붙이고 나면 클래스 본문 안에서 타입 파라미터를 다른 일반타입처럼 사용할 수 있다.

```kotlin
interface List<T> {
    operator fun get(index: Int): T
}
```
```kotlin
class StringList: List<String> {
    @override fun get(index: Int): String = ...
}

class ArrayList<T>: List<T> {
    @override fun get(index: Int): T = ...
}
```

### 타입 파라미터 제약
**타입 파라미터 제약**은 클래스나 함수에 사용할 수 있는 타입 인자를 제한하는 기능이다.
어떤 제네릭 타입의 타입 파라미터에 대한 **상한**<sup>upper bound</sup>으로 지정하면 그 제네릭 타입을 인스턴스화 할 때 사용하는 타입 인자는 반드시 그 상한 타입이거나 그 상한 타입의 하위 타입이여야 한다.

```kotlin
fun <T: Number> List<T>.sum(): T
```

```kotlin
fun <T> ensureTrailingPeriod(seq: T)
        where T : CharSequence, T : Appendable {
    if (!seq.endsWith('.')) {
        seq.append('.')
    }
}

fun main(args: Array<String>) {
    val helloWorld = StringBuilder("Hello World")
    ensureTrailingPeriod(helloWorld)
    println(helloWorld)
}
>> Hello World.
```

### 타입 파라미터를 널이 될 수 없는 타입으로 한정
제네릭 클래스나 함수를 정의하고 그 타입을 인스턴스화할 때는 널이 될 수 있는 타입을 포함하는 어떤 타입으로 타입 인자를 지정해도 타입 파라미터를 치환할 수 있다.

```kotlin
class Processor<T> {
    fun process(value: T) {
        value?.hashCode() //"value"는 널이 될 수 있으므로 안전한 호출 사용
    }
}
```

항상 널이 될 수 없는 타입만 타입인자로 받게 만들려면 타입 인자에 제약을 가해야 한다
```kotlin
class class Processor<T: Any> {
    fun process(value: T) {
        value.hashCode() // "value"는 null이 될 수 없다
    }
}
```


## 실행 시 제네릭스 동작: 소거된 타입 파라미터와 실체화된 타입 파라미터
JVM의 제네릭스는 보통 **타입 소거**를 사용해 구현된다.
이는 실행 시점에 제네릭 클래스의 인스턴스에 타입 인자 정보가 들어있지 않다는 뜻이다.
코틀린에서 타입 소거가 실용적인 면에서 어떤 영향을 끼치는지, 함수를 inline으로 선언함으로써 이런 제약을 우회할 수 있는지 알아보자.

### 실행 시점의 제네릭: 타입 검사와 캐스트
자바와 마찬가지로 코틀린 제네릭 타입 인자 정보는 런타입에 지워진다.
이는 제네릭 클래스 인스턴스가 그 인스턴스를 생헝할 때 쓰인 타입 인자에 대한 정보를 유지하지 않는다는 뜻이다.

```kotlin
val list1: List<String> = listOf("a", "b")
val list2: List<Int> = listOf(1,2,3)
```
컴파일러는 두 리스트를 서로 다른 타입으로 인식하지만 실행 시점에 그 둘은 완전히 같은 타입의 객체다.
실행 시점에 list1이나 list2가 문자열이나 정수의 리스트로 선언되었다는 사실을 알 수 없다.
각 객체는 단지 *List* 일 뿐이다.

#### 타입 소거의 한계
타입 인자를 따로 저장하지 않기 때문에 실행 시점에 타입 인자를 검사할 수 없다.
예를들어 어떤 리스트가 문자열로 이뤄진 리스트인지 다른 객체로 이뤄진 리스트인지를 실행 시점에 검사할 수 없다.
일반적으로 말하자면 `is` 검사에서 타입 인자로 지정한 타입을 검사할 수 없다.
```kotlin
if( value is List<String>) { ... }
```
실행 시점에 어떤 값이 List인지 여부는 알 수 있지만
그 리스트가 String의 List인지 혹은 다른 어떤 타입의 List인지는 알 수 가 없는 것이다.

#### 스타 프로젝션
코틀린에서는 타입 인자를 명시하지 않고 제네릭 타입을 사용할 수 없다.
이때 **스타 프로젝션**을 사용하면 된다.

```kotlin
if ( value is List<*>) { ... }
```

타입 파라미터가 2개 이상이라면 모든 타입 파라미터에 *를 포함시켜야 한다
`as`나 `as?` 캐스팅에도 여전히 제네릭 타입을 사용할 수 있다.
하지만 실행 시점에는 제네릭 타입의 타입 인자를 알 수 없으므로 캐스팅은 항상 성공한다.
그런 타입 캐스팅을 사용하면 컴파일러가 "unchecked cast(검사할 수 없는 캐스팅)" 이라는 경고를 표시하니
유의하자.

```kotlin
fun printSum(c: Collection<*>) {
    val intList = c as? List<Int> // Unchecked cast: List<*> to List<Int> 경고 발생
            ?: throw IllegalArgumentException("List is expected")
    println(intList.sum())
}
```

잘못된 타입의 원소가 들어있는 리스트를 전달하였을 때 실행시점에 
ClassCastException이 발생한다.

```kotlin
fun main(args: Array<String>) {
    printSum(setOf(1, 2, 3)) // 집함은 리스트가 아니므로 예외 발생 
    printSum(setOf("a", "b", "c"))  // as? 캐스팅은 성공하지만 나중에 다른 ClassCastException등의 에러 발생
}
```

코틀린 컴파일러는 컴파일 시점에 타입 정보가 주어진 경우에는 is 검사를 수행하게 허용할 수 있을 정도로 똑똑하다
```kotlin
fun printSum(c: Collection<Int>) {
    if (c is List<Int>) { //이 검사는 올바르다
        println(c.sum())
    }
}
```

### 실체화한 타입 파라미터를 사용한 함수 선언
코틀린 제네릭 타입의 타입 인자 정보는 실행 시점에 지워진다.
따라서 제네릭 클래스의 인스턴스가 있어도 그 인스턴스를 만들 때 사용한 타입 인자를 알아낼 수 없다.
하지만 이런 제약을 피할 수 있는 경우가 하나 있다.
인라인 함수의 타입 파라미터는 실체화되므로 실행 시점에 인라인 함수의 타입 인자를 알 수 있다.

실체화한 타입 파라미터를 사용하는 함수 정의하기
```kotlin
inline fun <reified T> isA(value: Any) = value is T //이제는 이 코드가 컴파일 된다.

fun main(args: Array<String>) {
    println(isA<String>("abc"))
    println(isA<String>(123))
}
```

실체화한 타입 파라미터를 사용하는 예시, 표준 라이브러리 함수 `filterIsInstance`
```kotlin
fun main(args: Array<String>) {
    val items = listOf("one", 2, "three")
    println(items.filterIsInstance<String>())
}
```

```kotlin
표준 라이브러리 함수 filterIsInstance를 간단하게 정리한 버전
inline fun <reified T> Iterable.filterIsInstance(): List<T> {
    val destination = mutableListOf<T>() 
    for (element in this) {
        if( element is T ) {
            destination.add(element)
        }
    }
    return destination
}
```

> **인라인 함수에서만 실체화한 타입 인자를 쓸 수 있는 이유**
> 컴파일러는 인라인 함수의 본문을 구현한 바이트코드를 그 함수가 호출되는 모든 지점에 삽입한다
> 컴파일러는 실체화한 타입 인자를 사용해 인라인 함수를 호출하는 각 부분의 정확한 타입 인자를 알 수 있다
> 따라서 컴파일러는 타입 인자로 쓰인 구체적인 클래스를 참조하는 바이트코드를 생성해 삽입할 수 있다
> 타입 파라미터가 아니라 구체적인 타입을 사용하므로 만들어진 바이트코드는 실행 시점에 벌어지는 타입 소거 영향을 받지 않는다

### 실체화한 타입 파라미터로 클래스 참조 대신
`Java.lang.Class` 타입 인자를 파라미터로 받는 API에 대한 코틀린 어댑터를 구축하는 경우
실체화한 타입 파라미터를 자주 사용한다.
실체화한 타입 파라미터를 활용해 API를 쉽게 호출할 수 있게 만드는 방법을 알아보자

ServiceLoader를 사용해 서비스를 읽어들이는 코드
```kotlin
val serviceImpl = ServiceLoader.load(Service::Class.java)
```
loadService 함수 
```kotlin
inline fun <reified T> loadService() { // 타입파라미터 T 실체화
    return ServiceLoader.load(T::class.java) // T::class로 타입파라미터의 클래스 가져온다
}
```

안드로이드 startActivity 쉽게 만들기
```kotlin
inline fun <reified T: Activity> Context.startActivity() {
    val intent = Intent(this, T::class.java)
    startActivity(intent)
}
```
```kotlin
startActivity<DetailActivity>()
```

### 실체화한 타입 파라미터의 제약
실체화한 타입 파라미터를 사용할 수 있는 경우
- 타입 검사와 캐스팅(`is`, `!is`, `as`, `as?`)
- 10장에서 설명할 코틀린 리플렉션 API(`::class`)
- 코틀린 타입에 대응하는 java.lang.Class 얻기 (`::class.java`)
- 다른 함수를 호출할 때 타입 인자로 사용

실체화한 타입 파라미터는 유용하지만 몇 가지 제약이 있다.
실체화한 타입 파라미터를 사용할 수 없는 경우
- 타입 파라미터 클래스의 인스턴스 생성하기
- 타입 파라미터 클래스의 동반 객체 매소드 호출하기
- 실체화한 타입 파라미터를 요구하는 함수를 호출하면서 실체화하지 않은 타입 파라미터로 받은 타입을 타입 인자로 넘기기
- 클래스, 프로퍼티, 인라인 함수가 아닌 함수의 타입 파라미터를 reified로 지정하기


## 변성: 제네릭과 하위 타입
**변성**개념은 `List<String>`와 `List<Any>`와 같이 기저타입이 같고 타입 인자가 다른 여러타입이
서로 어떤 관계가 있는지 설명하는 개념이다.
변성을 잘 활용하면 사용에 불편하지않으면서 타입 안정성을 보장하는 API를 만들 수 있다

### 변성이 있는 이유: 인자를 함수에 넘기기
List<Any> 타입의 파라미터를 받는 함수에 List<String>을 넘기면 안전할까?

클래스는 Any를 확장하므로 Any타입값을 파라미터로 받는 함수에 String을 넘기는것은 절대로 안전하다.
하지만 Any와 String이 List 인터페이스의 타입 인자로 들어가는 경우 완전 안전을 보장할 수 없다.

```kotlin
fun printContents(list: List<Any>) {
    println(list.joinToString())
}

fun main(args: Array<String>) {
    printContents(listOf("abc", "bac"))
}
```
위 경우 문자열 리스트도 잘 동작한다.
각원소를 Any로 취급하며, 모든 문자열은 Any 타입이기 때문이다.

```kotlin
fun addAnswer(list:MutableList<Any>) {
    list.add(42)
}

fun main(args: Array<String>) {
   val strings = mutableListOf("abc", "bac")
   addAnswer(strings) //이 줄이 컴파일 되면
   println(strings.maxBy { it.length}) // 해당 실행시점에 에러 발생
}
```
위 예제는 MutableList<Any>가 필요한 곳에 MutableList<String>을 넘기면 안된다는 사실을 보여준다

### 클래스, 타입, 하위 타입
타입 사이의 관계를 논하기 위해 **하위 타입**, **상위 타입** 이라는 개념을 잘 알아야 한다.
- 하위 타입 
    - 간단한 경우 하위 클래스(자식클래스)와 근본적으로 같다 (Number(상위타입), Int(하위 타입))
    - 어떤 타입 A가 필요한 모든 장소에 B타입을 넣어도 아무 문제가 없다면 타입 B는 A의 하위 타입이다
- 상위 타입
    - A타입이 B타입의 하위타입이라면 B는 A의 상위 타입이다

또한, 널이 될 수 있는 타입 A는 널이퇼수 있는 타입 A?의 하위타입이지만 A?는 A의 하위타입이 아니다
```kotlin
val s: String = "abc"
val t: Stirng? = s //가능
```

제네릭 타입을 인스턴스화 할 때 타입 인자로 서로 다른 타입이 들어가면 인스턴스 타입 사이의 하위 타입 관계가 성립하지 않으면 그 제네릭 타입을 **무공변**이라고 한다.

A가 B의 하위타입이면 `List<A>`는 `List<B>`의 하위타입이다.
이런 클래스나 인터페이스를 **공변적**이라 말한다.


### 공변성: 하위 타입 관계를 유지

#### 공변적인 클래스 정의
A가 B의 하위 타입일 때 `Producer<A>`가 `Producer<B>`의 하위타입이라면 Producer는 공변적이다.
이를 하위 타입 관계가 유지된다고 말한다.

예를들어 Cat은 Animal의 하위 타입이기 때문에 `Producer<Cat>`은 `Producer<Animal>`의 하위타입이다.
```kotlin
interface Producer<out T> { //클래스가 T에 대해 공변적이라고 선언한다.
    fun producer(): T
}
```
클래스의 타입 파라미터를 공변적으로 만들면 함수 정의에 사용한 파라미터 타입과 타입 인자의 타입이 정확하게 일치하지 않도라도 그 클래스의 인스턴스를 함수 인자나 반환 값으로 사용할 수 있다.


#### 무공변 클래스 정의
```kotlin
open class Animal {
    fun feed() { ... } 
}

class Herd<T: Animal>{ // 타입 파라미터를 무공변성으로 지정
    val size: Int get() = ...
    operator fun get(i: Int): T { ... }
}

fun feedAll(animals: Herd<Animal>) {
    for( i in 0 until animals.size) {
        animals[i].feed()
    }
}
```
무공변적 컬렉션 역할을 하는 클래스 사용하기
```kotlin
class Cat: Animal() {
    fun cleanLitter() { ... }
}

fun takeCareOfCats(cats: Herd<Cat>) {
    for( i in until cats.size) {
        cats[i].cleanLitter()
        // feedAll(cats)
        // Error: inferred type is Herd<Cat>, but Herd<Animals> was expected 타입 불일치 오류 발생
    }
}
```
Herd 클래스의 T 타입 파라미터에 대해 아무런 변성도 지정하지 않았기 때문에 
고양이 무리는 동물 무리의 하위 클래스가 아니다.

공변적 컬렉션 역할을 하는 클래스 사용하기
```kotlin
class Herd<out T: Animal> { // T는 이제 공변적이다
    val size: Int get() = ...
    operator fun get(i: Int): T { ... }
} 

fun takeCareOfCats(cats: Herd<Cat>) {
    for( i in until cats.size) {
        cats[i].cleanLitter()
        feedAll(cats) //캐스팅 할 필요 없다
    } 
}
```
타입의 안전성을 보장하기 위해 공변적 파라미터는 항상 아웃(out)위치에 있어야 한다
이는 T타입의 값을 생산할 수는 있지만 T타입의 값을 소비할 수는 없음을 의미한다

클래스 멤버를 선언할 때 타입 파라미터를 사용할 수 있는 지점은 모두 인(in)과 아웃(out) 위치로 나뉜다
T라는 타입 파라미터를 선언하고 T를 사용하는 함수가 멤버로 있는 클래스가 있을때, 
T가 함수 반환 타입에 쓰인다면 T는 아웃 위치에 있다
그 함수는 T타입의 값을 생산<sup>produce</sup>한다.
T가 함수 파라미터 타입에 쓰인다면 T는 인위치에 있다. 그런 함수를 T타입의 값을 소비<sup>consume</sup>한다.

```
class Herd<out T: Animal> { 
    val size: Int get() = ...
    operator fun get(i: Int): T { ... } // T를 반환타입(out)으로만 사용한다
} 
```
Cat이 Animal의 하위 타입이기 때문에 `Herd<Animal>`은 get을 호출하는 모든 코드는 get이 Cat을 반환해도 아무 문제 없이 작동한다.


- 공변성: 하위 타입 관계가 유지된다(`Producer<Cat>`은 `Producer<Animal>`의 하위타입이다)
- 사용 제한: T를 아웃 위치에서만 사용할 수 있다

코틀린의 `List<T>`는 읽기 전용이다. 따라서 그 안에는 T타입의 원소를 반환하는 get 메소드는 있지만
리스트에 타입 T를 추가하거나 리스트에 있는 기존 값을 변경하는 메소드는 없다.
따라서 List는 T에 공변적이다.
```kotlin
interface List<out T>: Collection<T> {
    operator fun get(index: Int): T 
    // 읽기전용이므로 out 위치에서만 쓰인다
}
```
```kotlin
interface List<out T>: Collection<T> {
    fun subList(fromIndex: Int, toIndex: Int): List<T> 
    // 여기세어도 T는 out 위치다.
}
```

`MutableList<T>`의 경우 타입파라미터 T에 대해 공변적으로 선언할 수 없다.
```kotlin
interface MutableList<T> :List<T>, MutableCollection<T> { // T에 대해 공변적이지 않다
    @override fun add(element: T): Boolean  
    // T가 in위치에 쓰이기 때문
}
```

변성은 코드에서 위험할 여지가 있는 메소드를 호출할 수 없게 만듦으로써
제네릭타입의 인스턴스 역할을 하는 클래스 인스턴스를 잘못 사용하는 일이 없게 방지하는 역할을 한다.
생성자는 나중에 호출할 수 있는 메소드가 아니다. 따라서 생성자는 위험할 여지가 없다.
하지만 생성자에 `val`이나 `var`키워드를 적는다면 게터나 세터를 정의하는것과 같으므로
읽기 전용 프로퍼티는 아웃위치, 변경가능 프로퍼티는 아웃과 인위치 모두에 해당한다.


또한 아래 예시는 loadAnimal 프로퍼티가 인위치에 있기 때문에 T를 아웃 위치에 표시할 수 없다.
```kotlin
class Herd<T: Animal>(var leadAnimal: T, vararg animals: T) { ... }
```

### 반공변성: 뒤집힌 하위 타입 관계
**반공변성**은 공변성을 거울에 비친 상이라고 할 수 있다.
반공변 클스 하위 타입 관계는 공변 클래스의 경우와 반대다.

```kotlin
interface Comparator<in T> {
    fun compare(e1: T, e2: T): Int { ... } //T를 인 위치에 사용한다
}
```
이 인터페이스의 메소드는 T타입을 소비하기만 하므로 T는 인위치에서만 쓰인다.
따라서 T앞에는 `in` 키워드를 붙여야 한다.

타입 B가 타입 A의 하위 타입인 경우 `Consumer<A>`가 `Consumer<B>`의 하위 타입인 관계가 성립되면
제네릭 클래스 `Consumer<T>`는 타입인자 T에 대해 반공변이다.

예를들어, `Consumer<Animal>`은 `Consumer<Cat>`의 하위타입일때 반공변이다.


공변성 타입 `Producer<T>`에서는 타입 인자의 하위 타입 관계가 제네릭타입에서도 유지되지만,
반공변성타입 `Consumer<T>`에서는 타입 인자의 하위 타입 관계가 제네릭 타입으로 오면서 뒤집힌다.
![image](https://user-images.githubusercontent.com/39984656/119256723-121de080-bbfd-11eb-9e85-b81c9fd528aa.png)

|공변성|반공변성|무공변성|
|---|---|---
|`Producer<out T>`|`Consumer<in T>`|`MutableList<T>`|
|타입 인자의 하위 타입 관계가 제네릭 타입에서도 유지된다| 타입인자의 하위 타입 관계가 제네릭 타입에서 뒤집힌다|하위 타입 관계가 성립하지 않는다|
|`Producer<Cat>`은 `Producer<Animal>`의 하위타입이다|`Consumer<Animal>`은 `Consumer<Cat>`의 하위타입이다||
|T를 아웃 위치에서만 사용할 수 있다| T를 인위치에서만 사용할 수 있다|T를 아무위치에서나 사용할 수 있다|

### 사용 지점 변성: 타입이 언급되는 지점에서 변성 지정
- 코틀린
    - **선언 지점 변성**
    - 클래스를 선언하면서 변성을 지정하면 그 클래스를 사용하는 모든 장소에서 변성 지정자가 영향을 끼침
    - 사용 지점 변성으로도 사용 가능하다
- 자바
    - **사용 지점 변성**
    - 타입 파라미터가 있는 타입을 사용할 때마다 해당 타입 파라미터를 하위 타입이나 상위타입중 어떤 타입으로 대치할 수 있는지 명시

### 스타 프로젝션: 타입 인자 대신 * 사용
제네릭 타입 인자 정보가 없음을 표시하기 위해 **스타 프로젝션**을 사용한다.

#### 스타프로젝션 특징
- `MutableList<*>`는 `MutableList<Any?>`와 같지 않다
    - `MutableList<Any?>`는 모든 타입 원소를 담을 수 있다는 뜻
    - `MutableList<*>`는 어던 정해진 구체적인 타입 원소만을 담는 리스트지만 그 원소 타입을 정확히 모름
- 제네릭 타입 파라미터가 어떤 타입인지 굳이 알 필요가 없을 때 사ㅛㅇ

#### 검증기
```kotlin

interface FieldValidator<in T> {
    fun validate(input: T): Boolean
}

object DefaultStringValidator : FieldValidator<String> {
    override fun validate(input: String) = input.isNotEmpty()
}

object DefaultIntValidator : FieldValidator<Int> {
    override fun validate(input: Int) = input >= 0
}

object Validators {
    private val validators =
            mutableMapOf<KClass<*>, FieldValidator<*>>()

    fun <T: Any> registerValidator(
            kClass: KClass<T>, fieldValidator: FieldValidator<T>) {
        validators[kClass] = fieldValidator
    }

    @Suppress("UNCHECKED_CAST")
    operator fun <T: Any> get(kClass: KClass<T>): FieldValidator<T> =
        validators[kClass] as? FieldValidator<T>
                ?: throw IllegalArgumentException(
                "No validator for ${kClass.simpleName}")
}

fun main(args: Array<String>) {
    Validators.registerValidator(String::class, DefaultStringValidator)
    Validators.registerValidator(Int::class, DefaultIntValidator)
    println(Validators[String::class].validate("Kotlin"))
    println(Validators[Int::class].validate(42))
}
```

## 요약
- 코틀린 제네릭스는 자바와 아주 비슷하다. 제네릭 함수와 클래스를 자바와 비슷하게 선언할 수 있다
- 자바와 마찬가지로 제네릭 타입의 타입 인자는 컴파일 시점에만 존재한다
- 타입 인자가 실행 시점에 지워지므로 타입 인자가 있는 타입을 `is`연산을 이용해 검사할 수 없다
- 인라인 함수의 타입 매개변수를 `reified`로 표시해서 실체화하면 실행 시점에 그 타입을 is로 검사하거나 `java.lang.Class`를 얻을 수 있다
- 변성은 기저 클래스가 같고 타입 파라미터가 다른 두 제네릭 타입 사이의 상위/하위 타입관계가 타입 인자 사이의 상위/하위 타입 관계에 어떤 영향을 받는지 명시하는 방법이다
- 제네릭 클래스의 타입 파라미터가 아웃 위치에서만 사용되는 경우(생산자) 그 타입 파라미터를 `out` 으로 표시해서 공변적으로 만들 수 있다
- 공변적인 경우와 반대로 제네릭 클래스의 타입 파라미터가 인 위치에서만 사용되는 경우(소비자) 그 타입 파라미터를 `in` 으로 표시해서 반공변적으로 만들 수 있다
- 코틀린 읽기 전용 List 인터페이스는 공변적이다. 따라서 `List<String>`은 `List<Any>`의 하위 타입이다
- 함수 인터페이스는 첫 번째 타입 파라미터에 대해서는 반공변적이고, 두번째 타입 파라미터에 대해서는 공변적이다. 그래서 (Animal)->Int는 (Cat)->Number의 하위타입이다
- 코틀린에서는 제네릭 클래스의 공변성을 전체적으로 지정하거나 구체적인 사용 위치에서 지정할 수 있다
- 제네릭 클래스의 타입 인자가 어떤 타입인지 정보가 없거나 타입 인자가 어떤 타입인지 중요하지 않을 때 스타 프로젝션 구문을 사용할 수 있다