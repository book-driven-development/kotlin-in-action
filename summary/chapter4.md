# 클래스, 객체, 인터페이스

코틀린의 클래스와 인터페이스는 자바의 클래스, 인터페이스와 약간 다르다
예를 들어 프로퍼티에 선언이 들어갈 수 있다.
자바와 달리 코틀린 선언은 기본적으로 final이며 public이다.
복잡한 초기화 로직을 수행하는 경우를 대비한 문법도 있다
프로퍼티도 간결하게 표현하며, 필요하면 접근자를 정의할 수 있다.
코틀린은 번잡스러움을 피하기위해 유용한 메소드를 자동으로 만들어준다.

## Index

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [클래스, 객체, 인터페이스](#클래스-객체-인터페이스)
  - [Index](#index)
  - [클래스 계층 정의](#클래스-계층-정의)
    - [코틀린 인터페이스](#코틀린-인터페이스)
    - [open, final, abstract 변경자: 기본적으로 final](#open-final-abstract-변경자-기본적으로-final)
      - [코틀린의 상속 제어 변경자](#코틀린의-상속-제어-변경자)
    - [가시성 변경자: 기본적으로 공개](#가시성-변경자-기본적으로-공개)
    - [내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스](#내부-클래스와-중첩된-클래스-기본적으로-중첩-클래스)
    - [봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한](#봉인된-클래스-클래스-계층-정의-시-계층-확장-제한)
  - [뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언](#뻔하지-않은-생성자와-프로퍼티를-갖는-클래스-선언)
    - [클래스 초기화: 주 생성자와 초기화 블록](#클래스-초기화-주-생성자와-초기화-블록)
    - [부 생성자: 상위 클래스를 다른 방식으로 초기화](#부-생성자-상위-클래스를-다른-방식으로-초기화)
    - [인터페이스에 선언된 프로퍼티 구현](#인터페이스에-선언된-프로퍼티-구현)
    - [게터와 세터에서 뒷받침하는 필드에 접근](#게터와-세터에서-뒷받침하는-필드에-접근)
    - [접근자의 가시성 변경](#접근자의-가시성-변경)
  - [컴파일러가 생성한 메소드: 데이터 클래스와 클래스 위임](#컴파일러가-생성한-메소드-데이터-클래스와-클래스-위임)
    - [모든 클래스가 정의해야 하는 메소드](#모든-클래스가-정의해야-하는-메소드)
      - [문자열 표현: toString()](#문자열-표현-tostring)
      - [객체의 동등성: equals()](#객체의-동등성-equals)
      - [해시 컨테이너: hashCode()](#해시-컨테이너-hashcode)
    - [데이터 클래스: 모든 클래스가 정의해야 하는 메소드 자동 생성](#데이터-클래스-모든-클래스가-정의해야-하는-메소드-자동-생성)
      - [데이터 클래스와 불변경: copy() 메소드](#데이터-클래스와-불변경-copy-메소드)
    - [클래스 위임: by 키워드 사용](#클래스-위임-by-키워드-사용)
  - [object 키워드: 클래스 선언과 인스턴스 생성](#object-키워드-클래스-선언과-인스턴스-생성)
    - [객체 선언: 싱글턴을 쉽게 만들기](#객체-선언-싱글턴을-쉽게-만들기)
    - [동반 객체: 팩토리 메소드와 정적 멤버가 들어갈 장소](#동반-객체-팩토리-메소드와-정적-멤버가-들어갈-장소)
    - [동반 객체를 일반 객체처럼 사용](#동반-객체를-일반-객체처럼-사용)
      - [동반 객체에 이름 붙이기](#동반-객체에-이름-붙이기)
      - [동반 객체에 인터페이스 구현하기](#동반-객체에-인터페이스-구현하기)
      - [동반 객체 확장](#동반-객체-확장)
    - [객체 식: 무명 내부 클래스를 다른 방식으로 결정](#객체-식-무명-내부-클래스를-다른-방식으로-결정)
  - [요약](#요약)

<!-- /code_chunk_output -->


## 클래스 계층 정의

### 코틀린 인터페이스
- 코틀린 인터페이스 안에는 추상 메소드뿐 아니라 구현이 있는 메소드도 정의할 수 있다
- 코틀린 인터페이스에는 아무런 상태(필드)도 들어갈 수 없다
- 코틀린 인터페이스는 원하는만큼 마음대로 구현할 수 있지만, 클래스는 오직 하나만 확장 할 수 있다
- 코틀린 인터페이스 메소드는 디폴트 구현을 제공할 수 있다(java에서는 default 키워드 사용)
- 

```kotlin
interface Clickable {
    fun click()
}
```

```kotlin
class Button : Clickable {
    @override fun click() = println("I was clicked")
}
```
Clickable 인터페이스를 구현하는 구현 클래스는 `click()`에 대한 구현을 제공해야한다.

```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!") //default 구현
}
```
위의 `Clickable` 인터페이스는 구현하는 클래스는 click에 대한 구현을 제공해야하지만,
`showOff()` 메소드의 경우 새로운 동작을 정의할 수도 있고, 정의를 생략할 수도 있다

```kotlin
interface Focusable {
    fun setFocus(b: Boolean) =
        println("I ${if (b) "got" else "lost"} focus.")

    fun showOff() = println("I'm focusable!")
}
```

```kotlin
class Button : Clickable, Focusable {
    override fun click() = println("I was clicked")

    override fun showOff() {
        super<Clickable>.showOff()
        super<Focusable>.showOff()
    }
}
```
한 클래스에서 `Clickable`, `Focusable`을 함께 구현한다면
컴파일 오류가 발생한다. 구현을 대체할 오버라이딩 메소드를 직접 제공해야한다.

### open, final, abstract 변경자: 기본적으로 final
Effective Java(Addison-Wesley, 2008)에서는 "상속을 위한 설계와 문서를 갖추거나, 그럴 수 없다면 상속을 금지하라" 라는 조언을 한다.
이는 특별히 하위 클래스에서 오버라이드하게 의도된 클래스와 메소드가 아니라면 모두 final로 만들라는 뜻이다.
코틀린은 이를 따르며, 코틀린은 클래스와 메소드가 기본적으로 final 이다.
상속을 허용하려면 앞에 `open` 변경자를 붙힌다.

```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!")
}

open class RichButton : Clickable {

    fun disable() {} //final 함수, 하위 클래스가 오버라이드 할 수 없다

    open fun animate() {} // 하위 클래스에서 오버라이드 할 수 있다

    override fun click() {} // 오버라이드된 메소드는 기본적으로 열려있어, 하위클래스에서 오버라이드 할 수 있다.
}
```

오버라이드 메소드를 더이상 오버라이드 할 수 없도록 금지하려면 `final`을 명시한다 
```kotlin
open class RichButton : Clickable {
    final override fun click() {}
}
```
클래스의 기본 상속 상태를 `final`로 하게되면 얻는 이익은 스마트 캐스트가 가능하다는 점이다.
스마트캐스트는 타입 검사뒤에 변경될 수 없는 변수에만 적용 가능하다.
클래스 프로퍼티의 경우 이는 `val` 이면서 커스텀 접근자가 없는 경우에만 스마트캐스트를 사용할 수 있다.

```kotlin
abstract class Animated {
    abstract fun animate() // 구현이 없는 추상 함수

    open fun stopAnimating() {} //추상클래스에 속했더라고 비추상함수는 기본적으로 final이지만 원한다면 open으로 오버라이드를 허용할 수 있다.

    fun animatedTwice() //추상클래스에 속했더라고 비추상함수는 기본적으로 final
}
```
추상클래스는 항상 열려있으며, 추상 멤버앞에 open을 명시할 필요가 없다

#### 코틀린의 상속 제어 변경자  
|변경자 | 이 변경자가 붙는 멤버는.. | 설명 |
|---|---|---|
| final | 오버라이드 할 수 없음 | 클래스 멤버의 기본 변경자이다 | 
| open | 오버라이드 할 수 있음 | 반드시 open을 명시해야 오버라이드 할 수 있댜 |
| abstract | 반드시 오버라이드 해야 함 | 추상 클래스의 멤버에만 이 변경자를 붙일 수 있다. <br> 추상 멤버는 구현이 있으면 안된다 |
| override | 상위 클래스나 상위 인스턴스의 멤버를 오버라이드 하는 중 | 오버라이드 하는 멤버는 기본적으로 열려있다. 하위 클래스의 오버라이드를 금지하려면 final을 명시해야 한다 |

### 가시성 변경자: 기본적으로 공개
| 변경자 | 클래스 멤버 | 최상위 선언 |
| --- | --- | --- |
| public(기본 가시성임) | 모든 곳에서 볼 수 있다 | 모든 곳에서 볼 수 있다 | 
| internal | 같은 모듈 안에서만 볼 수 있다 | 같은 모듈 안에서만 볼 수 있다 |
| protected | 하위 클래스 안에서만 볼 수 있다 | (최상위 선언에 적용할 수 없음) | 
| private | 같은 클래스 안에서만 볼 수 있다 | 같은 파일 안에서만 볼 수 있다 | 


### 내부 클래스와 중첩된 클래스: 기본적으로 중첩 클래스
코틀린도 클래스 안에 다른 클래스를 선언할 수 있다.
도우미 클래스를 캡슐화 하거나 코드 정의를 사용하는곳 가까이 두고싶을 때 유용하다.

```kotlin
import java.io.Serializable

interface State: Serializable

interface View {
    fun getCurrentState(): State
    fun restoreState(state: State) {}
}

class Button : View {
    override fun getCurrentState(): State = ButtonState()

    override fun restoreState(state: State) { /*...*/ }

    class ButtonState : State { /*...*/ }
}
```
Button의 클래스의 상태를 저장하는 클래스는 Button 클래스 내부에 선언하면 편하다

코틀린 중첩 클래스에 아무런 변경자가 없으면, 자바의 static 중첩 클래스와 같다.
이를 내부 클래스로 변경해서 바깥쪽 클래스에 대한 참조를 포함하게 만들고 싶다면
`inner` 변경자를 붙여야 한다.

| 클래스 B 안에 정의된 클래스 A | 자바에서는 | 코틀린에서는 | 
| ---- | ---- | ---- |
| 중첩 클래스(바깥쪽 클래스에 대한 참조를 저장하지 않음) | static class A | class A | 
| 내부 클래스(바깥쪽 클래스에 대한 참조를 저장함) | class A | inner class A |

```kotlin
class Outer {
    inner class Inner {
        fun getOuterReference(): Outer = this@Outer
    }
}
```
내부 클래스 Inner 안에서 바깥쪽 클래스 Outer에 접근하려면 `this@Outer`라고 써야한다.

### 봉인된 클래스: 클래스 계층 정의 시 계층 확장 제한
`eval()` 메소드의 `when` 식에서 Num과 Sum이 아닌 경우를 처리하는 `else` 분기를 덧붙히도록 강제한다.
```kotlin
interface Expr
class Num(val value: Int) : Expr
class Sum(val left: Expr, val right: Expr) : Expr

fun eval(e: Expr): Int =
    when (e) {
        is Num -> e.value
        is Sum -> eval(e.right) + eval(e.left)
        else ->
            throw IllegalArgumentException("Unknown expression")
    }
```
불필요한 else문이 들어가고 의미있는 값이 없으므로 에러를 던진다.
이는 `sealed` 클래스로 해결할 수 있다.
상위 클래스에 `sealed` 변경자를 붙이면 그 상위 클래스를 상속한 하위 클래스 정의를 제한할 수 있다.
`sealed` 클래스의 하위 클래스를 정의할 때는 반드시 상위 클래스 안에 중첩시켜야 한다.

```kotlin
sealed class Expr {
    class Num(val value: Int) : Expr()
    class Sum(val left: Expr, val right: Expr) : Expr()
}

fun eval(e: Expr): Int =
    when (e) {
        is Expr.Num -> e.value
        is Expr.Sum -> eval(e.right) + eval(e.left)
    }
```
when식이 모든 하위 클래스를 검사하므로 별도의 else 분기가 없어도 된다.
sealed 로 표기된 클래스는 자동으로 open이다.
sealed 클래스에 속한 값에 대해 디폴트 분기를 사용하지 않고 when식을 사용하면
나중에 sealed 클래스의 상속 계층에 새로운 하위 클래스를 추가해도 when식이 컴파일 되지 않다 when식을 고쳐야 한다는 것을 쉽게 알 수 있다.  


## 뻔하지 않은 생성자와 프로퍼티를 갖는 클래스 선언
코틀린은 주 생성자와 부 생성자를 구분한다.
또한 코틀린에서는 초기화 블록을 통해 초기화를 추가할 수 있다.

### 클래스 초기화: 주 생성자와 초기화 블록
클래스 이름 뒤에 오는 괄호로 둘러싸인 코드를 **주생성자**라고 부른다.
주 생성자는 파라미터를 지정하고, 그 생성자 파라미터에 의해 초기화 되는 프로퍼티를 정의하는 두 가지 목적에 쓰인다.
```kotlin
class User(val nickname: String,
           val isSubscribed: Boolean = true)
```

```kotlin
class User constructor(_nickname:String) { //파라미터가 하나만 있는 주 생성자
    val nickname: String
    init {
        nickname = _nickname
    }
}
```
`constructor` 는 주생성자나 부생성자 정의를 시작할 때 사용한다.
init 키워드는 초기화 블록을 시작한다.
초기화 블록에는 클래스의 객체가 만들어질 떄(인스턴스화 될 때) 실행될 초기화 코드가 들어간다.
초기화 블록은 주 생성자와 함께 사용된다.
주 생성자는 제한적이기 때문에 별도의 코드를 포함할 수 없으므로 초기화 블록이 필요하다.
필요하다면 클래스 안에 여러 초기화 블록을 선언할 수 있다.

```kotlin
open class User(val nickname:String) {...}
class TwitterUser(nickname:String) : User(nickname) {...} 
```
클래스에 기반 클래스가 있다면 주 생성자에 기반 클래스의 생성자를 호출해야할 필요가 있다.
기반 클래스를 초기화 하려면 기반클래스 이름 뒤에 괄호를 치고 생성자 인자를 넘긴다.

### 부 생성자: 상위 클래스를 다른 방식으로 초기화
코틀린에서는 생성자가 여럿 있는 경우가 자바보다 훨씬 적다.
자바에서는 오버로드한 생선자가 필요한 상황 중 상당수는 코틀린의 디폴트 파라미터 값과 이름붙은 인자 문법을 사용해 해결할 수 있기 때문이다.

```kotlin
open class View {
    constructor(context: Context){ ... } //부생성자

    constructor(context: Context, attr: AttributeSet) {... } //부생성자
}
```
부 생성자는 `constructor` 키워드로 시작한다.
이 클래스를 확장하면서 똑같이 부 생성자를 정의할 수 있다.
```kotlin
class MyButton : View {
    constructor(context: Context): super(context)

    constructor(context: Context, attr: AttributeSet): super(context, attr)
}
```

### 인터페이스에 선언된 프로퍼티 구현
인터페이스에 추상 프로퍼티 선언을 넣을 수 있다.
```kotlin
interface user {
    val nickname: String
}
```
이는 User 인터페이스가 구현하는 클래스가 nickname에 값을 얻을 수 있는 방법을 정의해야 한다는 것이다.
인터페이스에 있는 nickname필드는 아무 정보도 들어있지 않다.

```kotlin
fun getFacebookName(accountId: Int) = "fb:$accountId"

interface User {
    val nickname: String
}
class PrivateUser(override val nickname: String) : User

class SubscribingUser(val email: String) : User {
    override val nickname: String
        get() = email.substringBefore("@")
}

class FacebookUser(val accountId: Int) : User {
    override val nickname = getFacebookName(accountId)
}
```
SubscribingUser와 FacebookUser의 nickname구현 차이에 주의해야 한다.
비슷해보이지만 SubscribingUser의 nickname은 매번 호출될 때마다 substringBefore을 호출하여 계산하는 커스텀 게터를 사용하고,
FacebookUser의 nickname은 객체 초기화시 계산한 데이터를 뒷받침하는 필드에 저장했다가 불러오는 방식이다.

### 게터와 세터에서 뒷받침하는 필드에 접근
값을 저장하는 동시에 로직을 실행할 수 있게 하기 위해서는 접근자 안에서 프로퍼티를 뒷받침하는 필드에 접근할 수 있어야한다.
```kotlin
class User(val name: String) {
    var address: String = "unspecified"
        set(value: String) {
            println("""
                Address was changed for $name:
                "$field" -> "$value".""".trimIndent())
            field = value
        }
}
```
코틀린 프로퍼티의 값을 바꿀 때는 `user.address = "new value"` 처럼 필드 설정 구문을 사용하며,
이 구문은 내부적으로는 address의 세터를 호출한다.
위 예제에서는 커스텀 세터를 정의해 추가 로직을 실행한다.

접근자의 본문에서는 `field` 라는 특별한 식별자를 통해 뒷받침하는 필드에 접근할 수 있다.
게터에서는 field의 값을 읽을 수만 있고, 세터에서는 `field` 값을 읽거나 쓸 수 있다.
변경 가능 프로퍼티의 게터와 세터중 한 쪽 만 직접 정의해도 된다는 점을 기억하라.


### 접근자의 가시성 변경
set 앞에 가시성 변경자를 추가해서 접근자의 가시성을 변경할 수 있다.
```kotlin
class LengthCounter {
    var counter: Int = 0
        private set

    fun addWord(word: String) {
        counter += word.length
    }
}
```
위 예제에서는 set앞에 가시성 변경자를 추가해서 접근자의 가시성을 변경할 수 없다.


## 컴파일러가 생성한 메소드: 데이터 클래스와 클래스 위임
자바에서는 클래스가 `equals()`, `hashCode()`, `toString()`등의 메소드를 구현해야 한다.
보통 비슷한 방식으로 기계적으로 IDE등의 도움을 받아 구현한다.
코틀린에서는 이런 기계적인 메소드들을 생성하는 작업을 보이지않는곳에서 해준다.

### 모든 클래스가 정의해야 하는 메소드

#### 문자열 표현: toString()
```kotlin
class Client(val name: String, val postalCode: Int) {
    override fun toString() = "Client(name=$name, postalCode=$postalCode)"
}
```
코틀린 class에서는 toString 메소드를 기본으로 제공하지만 `Client@5e9f23b4`와 같이 유용하지 않다.
toString을 오버라이드 하여 추가로 많은 정보를 표시할 수 있다.

#### 객체의 동등성: equals()
서로 다른 두 객체가 내부에 동일한 데이터를 포함하는 경우에 동등한 객체로 간주해야할 필요가 있을때
```kotlin
class Client(val name: String, val postalCode: Int)

fun main(args: Array<String>) {
    val client1 = Client("Alice", 342562)
    val client2 = Client("Alice", 342562)
    println(client1 == client2) //동등 연산에 ==을 사용, equals가 호출됨
}
>> false 
```
두 객체는 동등하지 않다.
`equals()` 메소드를 오버라이드 해야 한다.

> 동등연산에 `==`를 사용함
> 자바에서는 `==`는 Primitive타입과 Reference타입을 비교할 때 사용한다. 
> Primitive타입의 경우 `==`는 두 피연산자의 값이 같은지 비교한다.
> 반면 Reference타입의 경우 `==`는 두 피연산자의 주소가 같은지 비교한다
> 따라서 자바에서는 두 객체의 동등성을 비교하려면 equals를 사용해야 한다
>
> 코틀린에서는 `==` 연산자가 내부적으로 equals를 호출해 객체를 비교한다
> equals를 오버라이드하면 `==`를 사용하여 안전하게 클래스 인스턴스를 비교할 수 있다.
> 참조 비교를 위해서는 `===` 연산자를 사용한다.

```kotlin
class Client(val name: String, val postalCode: Int) {
    override fun equals(other: Any?): Boolean {
        if (other == null || other !is Client)
            return false
        return name == other.name &&
               postalCode == other.postalCode
    }
    override fun toString() = "Client(name=$name, postalCode=$postalCode)"
}

fun main(args: Array<String>) {
    val processed = hashSetOf(Client("Alice", 342562))
    println(processed.contains(Client("Alice", 342562)))
}
```

#### 해시 컨테이너: hashCode()
자바에서는 equals를 오버라이드 할 때 반드시 hashCode도 함께 오버라이드 해야 한다.
```kotlin
val processed = hashSetOf(Client("오현석",4122))
println(processed.contains(Client("오현석",4122)))
>>false
```
위 처럼 결과가 나오는것은 `hashCode` 메소드를 정의하지 않았기 때문이다.
JVM 언어에서는 `hashCode`가 지켜야 하는 "`equals()`가 `true`를 반환하는 두 객체는 반드시 같은 `hashCode()`를 반환해야 한다" 라는 제약이 있다.
HashSet은 원소를 비교할 때 비용을 줄이기 위해 먼저 객체의 해시코드를 비교하고 해시코드가 같은 경우에만 실제 값을 비교한다.

```kotlin
class Client(val name:String, val postalCode: Int) {

    override fun hashCode(): Int = name.hashCode() * 31 + postalCode
}
```

### 데이터 클래스: 모든 클래스가 정의해야 하는 메소드 자동 생성
위와 같이 객체 비교등을 위해서 `equals()`, `hashCode()`등을 오버라이드 해주어야 한다.
코틀린에서는 `data` 라는 변경자를 클래스 앞에 붙이게 되면 이런 메소드들을 컴파일러가 자동으로 만들어준다.
이런 클래스를 데이터 클래스라 부른다.

데이터 클래스가 제공하는 메소드 기능
- 인스턴스간 비교를 위한 equals
- HashMap과 같은 해시 기반 컨테이너에서 키로 사용할 수 있는 hashCode
- 클래스의 각 필드를 선언 순서대로 표시하는 문자열을 만들어주는 toString

equals와 hashCode는 주 생성자에 나열된 모든 프로퍼티를 고려해 만들어진다.

```kotlin
data class Client(val name: String, val postalCode: Int)
```

#### 데이터 클래스와 불변경: copy() 메소드
데이터 블래스의 프로퍼티가 꼭 `val`일 필요는 없다.
원한다면 `var` 프로퍼티를 써도 된다. 
하지만 모든 프로퍼티를 읽기 전용으로 만들어서 데이터 클래스를 불변 클래스로 만들기를 권장한다.
HashMap등의 컨테이너에서 데이터 클래스 객체를 담는 경우 불변성이 필수적이다.

이런 데이터 클래스 인스턴스를 불변 객체로 활용할 수 있도록 코틀린 컴파일러는 copy메소드를 제공한다.
`copy()` 메소드는 객체를 복사 하면서 일부 프로퍼티를 바꿀 수 있게 해주는 메소드이다.
객체를 메모리상에서 직접 바꾸는 대신 복사본을 만드는 편이 낫다.
복사본은 원본과 다른 생명주기를 가지며, 복사를 하면서 일부 프로퍼티 값을 바꾸거나 복사본을 제거해도
프로그램에서 원본을 참조하는 다른부분에 전혀 영향을 끼치지 않는다.

copy() 메소드의 사용
```kotlin
fun main(args: Array<String>) {
    val bob = Client("Bob", 973293)
    println(bob.copy(postalCode = 382555))
}
```

### 클래스 위임: by 키워드 사용
종종 상속을 허용하지 않는 클래스에 새로운 동작을 추가해야할 때가 있다.
이럴때 일반적으로 데코레이터 패턴을 사용한다.
이 패턴의 핵심은 상속을 허용하지 않는 클래스 대신 사용할 수 있는 새로운 클래스를 만들되 기존 클래스와 같은 인터페이스를 데코레이터가 제공하게 만들고, 기존 클래스를 데코레이터 내부 필드로 유지하는 것이다.
이때 새로 정의해야 하는 기능은 데코레이터 메소드에 새로 정의하고 기존 기능이 그대로 필요한 부분은 데코레이터의 메소드가 기존 클래스의 메소드에게 요청을 전달한다.

이런 위임 언어가 제공하는 일급시민 기능으로 지원한다는 점이 코틀린의 장접이다.
인터페이스를 구현할 때 `by` 키워드를 통해 그 인터페이스에 대한 구현을 다른 객체에 위임중이라는 사실을 명시할 수 있다.

```kotlin
import java.util.HashSet

class CountingSet<T>(
        val innerSet: MutableCollection<T> = HashSet<T>()
) : MutableCollection<T> by innerSet { // MutableCollection의 구현을 innerSet에게 위임한다

    var objectsAdded = 0

    override fun add(element: T): Boolean { // 이메소드는 위임하지 않고 새로 구현한다
        objectsAdded++
        return innerSet.add(element)
    }

    override fun addAll(c: Collection<T>): Boolean { // 이메소드는 위임하지 않고 새로 구현한다
        objectsAdded += c.size
        return innerSet.addAll(c)
    }
}
```


## object 키워드: 클래스 선언과 인스턴스 생성
코틀린에서는 `object` 키워드를 다양항 상황에서 사용하지만 
모든 경우 클래스를 정의하면서 동시에 인스턴스를 생성한다는 공톹점이 있다

- 객체 선언<sup>object declaration</sup>은 싱글턴을 정의하는 방법중 하나다
- 동반 객체<sup>companion object</sup>는 메소드는 아니지만 어떤 클래스와 관련있는 메소드와 팩토리메소드를 담을 때 쓰인다. 동방 객체 메소드에 접근할 때는 동반 객체가 포함된 클래스 이름을 사용할 수 있따
- 객체 식은 자바의 무명 내부 클래스<sup>anonymous inner class</sup>대신 쓰인다.

### 객체 선언: 싱글턴을 쉽게 만들기
코틀린은 **객체 선언** 기능을 통해 싱글턴을 언어에서 기본 지원한다.
객체 선언은 클래스 선언과 그 클래스에 속한 단일 인스턴스의 선언을 합친 선언이다.
```kotlin
object Payroll {
    val allEmployees = arrayListOf<Person>()

    fun calculateSalary() {
        for (person in allEmployees) {
            ...
        }
    }
}
```

객체 선언도 클래스나 인터페이스를 상속할 수 있다.
```kotlin
object CaseInsensitiveFileComparator : Comparator<File> {
    override fun compare(file1: File, file2: File): Int {
        return file1.path.compareTo(file2.path,
                ignoreCase = true)
    }
}
```

클래스 안에서 객체를 선언할 수도 있다.
그런 객체도 인스턴스는 단 하나 뿐이다.
(바깥클래스의 인스턴스마다 중첩 객체 선언에 해당하는 인스턴스가 하나씩 따로 생기는 것이 아니다)
```kotlin
data class Person(val name: String) {
    object NameComparator : Comparator<Person> {
        override fun compare(p1: Person, p2: Person): Int =
            p1.name.compareTo(p2.name)
    }
}
```

### 동반 객체: 팩토리 메소드와 정적 멤버가 들어갈 장소
코틀린 클래스 안에는 정적인 멤버가 없다.
코틀린언어는 자바 static 키워드를 지원하지 않는다.

클래스 안에 정의된 객체 중 하나에 companion 이라는 특별한 표시를 붙이면 그 클래스의 동반 객체로 만들 수 있다.
동반 객체의 프로퍼티나 메소드에 접근하려면 그 동반 객체가 정의된 클래스 이름을 사용한다.
이때 객체의 이름을 따로 지정할 필요가 없다
```kotlin
class A {
    companion object {
        fun bar() {
            println("Companion object called")
        }
    }
}
```

동반 객체는 private 생성자를 호출하기 좋은 위치이다.
동반 객체는 자신을 둘러싼 클래스의 모든 private 멤버에 접근할 수 있다.
따라서 동반 객체는 바깥쪽 클래스의 private 생성자도 호출할 수 있다.
따라서 동반 객체는 팩토리 패턴을 구현하기 가장 적합한 위치이다.

```kotlin
class User private constructor(val nickname: String) {
    companion object {
        fun newSubscribingUser(email: String) =
            User(email.substringBefore("@"))

        fun newFacebookUser(accountId: Int) =
            User(getFacebookName(accountId))
    }
}
```

### 동반 객체를 일반 객체처럼 사용
동반 객체는 클래스 안에 정의된 일반 객체다.
동반 객체에 이름을 붙이거나 동반 객체가 인터페이스를 상속하거나, 동반 객체 안에 확장 함수와 프로퍼티를 정의할 수 있다.

#### 동반 객체에 이름 붙이기
```kotlin
class Person(val name: String) {
    companion object Loader { //동반 객체에 이름 붙이기
        fun fromJSON(jsonText: String): Person = ...
    }
}
```

#### 동반 객체에 인터페이스 구현하기
```kotlin
interface JSONFactory<T> {
    fun fromJSON(jsonText: String): T
}

class Person(val name: String) {
    companion object : JSONFactory<Person> {
        override fun fromJSON(jsonText: String): Person =...
    }
}
```

#### 동반 객체 확장
클래스에 동반 객체가 있으면 그 객체 안에 함수를 정의함으로써 클래스에 대헤 호출할 수 있는 확장 함수를 만들 수 있다.
```kotlin
// 비즈니스 로직 모듈
class person(val firstName: String, val lastName: String) {
    companion object {

    }
}

// 클라이언트/서버 통신 모듈
fun Person.Companion.fromJson(json: String): Person { // 확장 함수 선언

}
val p = Person.fromJSON(json)
```
마치 동반 객체 안에서 fromJson 함수를 정의한 것 처럼 fromJSON을 호출할 수 있다

### 객체 식: 무명 내부 클래스를 다른 방식으로 결정
무명 객체<sup>anonymous object</sup>를 정의할 때도 object 키워드를 쓴다.
무명 객체는 자바의 무명 내부 클래스를 대신한다.
```kotlin
window.addMouseListener(
    object : MouseAdapter() {
        override fun mouseClicked(e:MouseEvent) {

        }

        override fun mouseEntered(e:MouseEvent) {

        }
    }
)
```


## 요약
- 코틀린의 인터페이스는 자바 인터페이스와 비슷하지만 디폴트 구현을 포함할 수 있고, 프로퍼티도 포함할 수 있다  
- 모든 코틀린 선언은 기본적으로 final이며 public 이다  
- 선언이 final이 되지 않게 만들려면 앞에 open을 붙여야 한다  
- internal 선언은 같은 모듈 안에서만 볼 수 있다  
- 중첩 클래스는 기본적으로 내부 클래스가 아니다. 바깥쪽 클래스에 대한 참조를 중첩 클래스 안에 포함시키려면 inner 키워드를 중첩 클래스 선언 앞에 붙여서 내부 클래스로 만들어야 한다  
- sealed 클래스를 상속하는 클래스를 정의하려면 반드시 부모 클래스 정의 안에 중첩 클래스로 정의해야 한다  
- 초기화 블록 부 생성자를 활용해 클래스 인스턴스를 더 유연하게 초기화 할 수 있다  
- field 식별자를 통해 프로퍼티 접근자(게터와 세터) 안에서 프로퍼티의 데이터를 저장하는 데 쓰이는 뒷받침하는 필드를 참조할 수 있다  
- 데이터 클래스를 사용하면 컴파일러가 equals, hashCode, toString, copy등의 메소드를 자동으로 생성해준다  
- 클래스 위임을 사용하면 위임 패턴을 구현할 때 필요한 수많은 성가신 준비 코드를 줄일 수 있다  
- 객체 선언을 사용하면 코틀린답게 싱글턴 클래스를 정의할 수 있다  
- 동반 객체는 자바의 정적 메소드와 필드 정의를 대신한다  
- 동반 객체도 다른 객체와 마찬가지로 인터페이스를 구현할 수 있다. 외부에서 동반 객체에 대한 확장함수와 프로퍼티를 정의할 수 있다  
- 코틀린의 객체 식은 자바의 무명 내부 클래스를 대신한다. 하지만 코틀린 객체식은 여러 인스턴스를 구현하거나 객체가 포함된 영여에 있는 변수 값을 변경할 수 있는 등 자바 무명 내부 클래스보다 더 많은 기능을 제공한다  

