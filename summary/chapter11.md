# DSL 만들기
영역 특화 언어<sup>DSL, Domain-Specific Language<sup>를 사용하여 표현력이 좋고 코틀린다운 API를 설계하는 방법을 알아보자.

## API에서 DSL로
DSL은 간결한 구문을 제공하는 기능과 그런 구문을 확장해서 여러 메소드를 호출을 조함으로써 구조를 만들어내는 기능에 의존한다.

**코틀린의 간결한 구문**
|일반구문|간결한 구문|사용한 언어 특성|
|---|---|---|
|`StringUtil.capitalize`|`s.capitalize()`|확장함수|
|`1.to("one")`|`1 to "one"`|중위 호출|
|`set.add(2)`|`set+=2`|연산자 오버로딩|
|`map.get("key")`|`map["key"]`|get 메소드에 대한 관례|
|`file.use({f->f.read()})`|`file.use { it.read() }`|람다를 괄호 밖으로 빼내는 관례|
|`sb.append("yes")`<br>`sb.append("no")`|with (sb) {<br>    append("yes")<br>append("no")<br>}|수신 객체 지정 람다|

```kotlin
fun createSimpleTable() = createHTML().
    table {
        tr {
            td { +"cell"}
        }
    }
```

### 영역 특화 언어라는 개념
DSL이라는 개념은 오래된 개념이다.
범용 프로그래밍 언어를 기반으로 하여 필요하지 않은 기능을 없앤 **영역 특화 언어**를 DSL이라고 부른다.
우리에게 가장 익숙한 DSL은 SQL과 정규식이다.
이 두 언어는 데이터 베이스 조작과 문자열 조작이라는 특정 작업에 가장 적합하다.
그렇다고 해서 우리가 전체 애플리케이션을 정규식이나 SQL을 사용해 작성하는 경우는 없다. (정규식으로만 만들어진 애플리케이션이라니 끔찍하다…)
이러한 DSL은 범용 프로그래밍언어와 달리 `declarative`라는 점이 중요하다. 범용 프로그래밍 언어는 주로 `imperative`하다.
`declarative`의 장점은 결과를 기술하기만 하고 그 결과를 달성하기 위한 세부 실행은 언어를 해석하는 엔진에 맡겨버린다.
실행 엔진이 결과를 얻는 과정을 전체적으로 최적화하기 때문에 `declarative` 언어가 더 효율적인 경우가 종종 있다.
그러나 이러한 `declarative` 언어에도 한 가지 단점이 존재한다.
바로 DSL을 범용 언어로 만든 애플리케이션과 조합하기가 어렵다는 점이다.
DSL은 자체 문법이 있기 때문에 다른 언어의 프로그램 안에 직접 포함시킬 수 없다.
DSL로 작성한 프로그램을 다른 언어에서 호출하기 위해서는 DSL 프로그램을 별도의 파일이나 문자열 리터럴로 저장해야 한다.
하지만 이런 식으로 DSL을 저장하면 호스트 프로그램과 DSL의 상호작용을 컴파일 시점에 제대로 검증하거나 DSL 프로그램을 디버깅하거나 DSL 코드 작성을 돕는 IDE 기능을 제공하기 어려워지는 문제가 있다.
이런 문제를 극복하기 위해 internal DSL이라는 개념이 점점 유명해지고 있다.

### 내부 DSL 
독립적인 문법 구조를 가진 외부 DSL과 반대로 내부 DSL은 범용 언어로 작성된 프로그래밍의 일부이며 범용 언어와 동일한 문법을 사용한다.
따라서 내부 DSL은 다른 언어가 아니라 DSL의 핵심 장점을 유지하면서 주 언어를 특별한 방법으로 사용하는 것이다.
```sql
SELECT Country.name, COUNT(Customer.id)
    FROM Country
    JOIN Customer
        ON Country.id = Customer.country_id
GROUP BY Country.name
ORDER BY COUNT(Customer.id) DESC
    LIMIT 1
```
질의 언어아 주 애플리케이션 언어 사이에 상호작용할 수 있는 방법을 제공해야 하기 때문에 SQL로 코드를 작성하는 것이 편하지 않을 수 있다.
코틀린으로 작성된 데이터베이스 프레임워크인 Exposed 프레임워크가 제공하는 DSL을 사용하여 같은 질의를 구현하면 아래와 같다.
```kotlin
(Country join Customer)
    .slice(Country.name, Count(Customer.id))
    .selectAll()
    .groupBy(Country.name)
    .orderBy(Count(Customer.id),isAsc = false)
    .limit(1)
```
두 번째 코드는 SQL 질의가 반환하는 결과 집합을 코틀린 객체로 변환하기 위해 특별히 해줄 것이 없다.
쿼리를 실행한 결과가 네이티브 코틀린 객체이기 때문이다.
두 번째 코드를 internal DSL이라고 부른다.
 
### DSL의 구조
코틀린 DSL에서는 보통 람다를 중첩시키거나 메소드 호출을 연쇄시키는 방식으로 구조를 만든다.
질의를 실행하기 위해 필요한 메소드들을 조합해야하며, 그렇게 메소드를 조합해서 만든 질의는 질의에 필요한 인자를 메소드 호출 하나에 모두 넘기는 것 보다 훨씬 더 가독성이 높다.

```kotlin
//kotlintest
str should startWith("kot")
```

```kotlin
// JUnit
assertTrue(str.startsWith("kot"))
```
일반 JUnit API를 사용해 테스트를 작성하면 더 읽기 복잡하다.
이에 비해 중위 호출을 사용한 코틀린 코드가 훨씬 더 직관적이고 이해하기 쉬운 것을 확인할 수 있다.


## 구조화된 API 구축: DSL에서 수신 객체 지정 DSL 사용
수신 객체 지정 람다는 구조화된 API를 만들 때 도움이 되는 강력한 코틀린 기능이다.
구조가 있다는 것은 일반적인 API와 DSL을 구분하는 아주 중요한 특성이다.

### 수신 객체 지정 람다와 확장함수 타입
이제 buildString 함수를 통해 코틀린이 수신 객체 지정 람다를 어떻게 구현하는지 살펴보자.
buildString은 한 StringBuilder 객체에 여러 내용을 추가할 수 있다.
람다를 인자로 받는 buildString()을 정의해보자.
```kotlin
fun buildString(
    builderAction: (StringBuilder) -> Unit
) : String {
    val sb = StringBuilder()
    builderAction(sb)
    return sb.toString()
}

val s = buildString {
    it.append("Hello, ")
    it.append("World!")
}

println(s)
// Hello, World!
```
이 코드는 이해하기 쉽지만 사용하기 편리하지는 않다.
람다 본문에서 매번 it을 사용해 StringBuilder를 참조해야하기 때문이다.
이번에는 수신 객체 지정 람다를 사용하여 it이라는 이름을 사용하지 않는 람다를 인자로 넘겨보자.

```kotlin
fun buildString(
    builderAction: StringBuilder.() -> Unit
) : String {
    val sb = StringBuilder()
    sb.builderAction()
    return sb.toString()
}

val s = buildString {
    this.append("Hello, ")
    append("World!")
}

println(s)
// Hello, World!
```
이전 코드와 현재 코드를 비교해보자.
우선 builderAction의 타입이 달라졌다.
이는 확장 함수 타입을 사용했다.
확장 함수 타입 선언은 람다의 파라미터 목록에 있는 수신 객체 타입을 파라미터 목록을 여는 괄호 앞으로 빼 놓으면서 중간에 마침표를 붙인 형태다.
```kotlin
확장 함수 타입 선언 과정

(StringBuilder) -> StringBuilder() -> StringBuilder.()
```
확장 함수 타입을 함수의 매개변수 타입으로 지정함으로써 수신 객체 지정 람다를 인자로 받을 수 있게 되었다.

그러면 왜 굳이 확장 함수 타입일까?

확장 함수의 본문에서는 확장 대상 클래스에 정의된 메소드를 마치 그 클래스 내부에서 호출하듯이 사용할 수 있었다.
확장 함수나 수신 객체 지정 람다에서는 모두 함수(람다)를 호출할 때 수신 객체를 지정해야만 하고, 함수(람다) 본문 안에서는 그 수신 객체를 특별한 수식자 없이 사용할 수 있다.
일반 람다를 사용할 때는 StringBuilder 인스턴스를 builderAction(sb)처럼 StringBuilder를 매개변수로 전달해야하지만,
수신 객체 지정 람다를 사용할 때는 sb.builderAction()으로 전달한다.

즉, sb.builderAction()에서 builderAction은 StringBuilder 클래스 안에 정의된 함수가 아니며 StringBuilder 인스턴스인 sb는 확장 함수를 호출할 때와 동일한 구문으로 호출할 수 있는 함수 타입의 인자일 뿐이다.

표준 라이브러리의 buildString 구현은 위의 구현보다 훨씬 짧은데, builderAction을 명시적으로 호출하는 대신 builderAction을 apply 함수에게 인자로 넘긴다.

이렇게 하면 builderString을 단 한줄로 구현할 수 있다.

```kotlin
fun buildString(builderAction: StringBuilder.() -> Unit): String = StringBuilder().apply(builderAction).toString()
```

우리는 이전에 with와 apply에 대해서도 살펴보았다.
with와 apply 모두 수신 객체로 확장 함수 타입의 람다를 호출한다.
```kotlin
inline fun <T> T.apply(block: T.() -> Unit): T {
    block()
    return this
}

inline fun<T,R> with(receiver: T, block: T.() -> R) : R {
    receiver.block()
}
```

## invoke 관례를 사용한 더 유연한 블록 중첩
invoke convention을 사용하면 객체를 함수처럼 호출할 수 있다.
이미 함수 타입의 객체(Function1 등)을 함수처럼 호출하는 경우를 살펴보았다.
마찬가지로 invoke convention을 사용하면 함수처럼 호출할 수 있는 객체를 만드는 클래스를 정의할 수 있다.
하지만 이 기능이 일상적으로 사용하라고 만든 기능은 아니라는 점에 유의하자.
invoke convention을 남용하면 1()과 같이 이해하기 어려운 코드가 생길 수 있다.
그러나 DSL에서는 invoke convention이 아주 유용한 경우가 자주 있다.

### invoke 관례: 함수처럼 호출할 수 있는 객체
우리는 이미 코틀린의 convention에 대하여 학습하였다.
가령 foo라는 변수가 있고 foo[bar]라는 식을 사용하면 이는 foo.get(bar)로 변환된다.
이때 get은 Foo라는 클래스 안에 정의된 함수이거나 확장함수여야 한다.
invoke convention 역시 같은 역할을 수행한다.
다만 get과는 다르게 괄호()를 사용한다.
operator 변경자가 붙은 invoke 메소드 정의가 들어있는 클래스의 객체를 함수처럼 호출할 수 있다.

```kotlin
class Greeter(val greeting: String) {
    operator fun invoke(name: String) {
        println("$greeting, $name!")
    }
}

val bavarianGreeter = Greeter("Servus")
bavarianGreeter("Dmitry")
// output
// Servus, Dmitry!
```
bavarianGreeter 객체가 마치 함수처럼 호출되는 것을 확인할 수 있다.
이 때 bavarianGreeter(“Dmitry”)는 내부적으로 bavarianGreeter.invoke(“Dmitry”)로 컴파일된다.
즉 이 코드에는 그 어떠한 신비로운 점도 없다.
이러한 invoke convention은 DSL에서 굉장히 유용하게 사용될 수 있다.

### invoke 관례와 함수형 타입
invoke convention에 배웠으므로 우리는 일반적인 람다 호출 방식(람다 뒤에 괄호를 붙이는 방식)이 실제로는 invoke convention을 사용하는 것에 지나지 않음을 충분히 알 수 있다.
```kotlin
{ println("Hello") }()
```
인라인하는 람다를 제외한 모든 람다는 함수형 인터페이스(Function1 등)을 구현하는 클래스로 컴파일된다. 각 함수형 인터페이스 안에는 그 인터페이스 이름이 가리키는 개수만큼 파라미터를 받는 invoke 메소드가 들어있다.

람다를 함수처럼 호출할 수 있다는 사실을 알면 어떤 점이 좋을까?

우선 복잡한 람다를 여러 메소드로 분리하면서도 여전히 분리 전의 람다처럼 외부에서 호출할 수 있는 객체를 만들 수 있다.
그리고 함수 타입 파라미터를 받는 함수에게 그 객체를 전달할 수 있다.

### DSL의 invoke 관례: 그레이들에서 의존관계 정의
모듈 의존 관계를 정의하는 Gradle DSL 예제를 보자.
```kotlin
dependencies {
    compile("junit:junit:4.11")
}
```
이 코드처럼 중첩된 블록 구조를 허용하는 한편 넓게 펼쳐진 형태의 함수 호출 구조도 함께 제공하는 API를 만들어보자.

```kotlin
dependencies.compile("junit:junit:4.11") // 첫 번째

dependencies { // 두 번째
    compile("junit:junit:4.11")
}
```
첫 번째의 경우 dependencies 변수에 대해 compile 메소드를 호출한다.
두 번째의 경우 dependencies 안에 람다를 받는 invoke 메소드를 정의하면 두 번째 방식의 호출을 사용할 수 있다.

invoke를 사용하는 경우 호출 구문을 완전히 풀어쓰면 dependencies.invoke({…})이다.
dependencies 객체는 DependencyHandler 클래스의 인스턴스이다.

DependencyHandler 안에는 compile과 invoke 메소드 정의가 들어있다.
invoke 메소드는 수신 객체 지정 람다를 파라미터로 받는데, 이 람다의 수신 객체는 다시 DependencyHandler.
이 람다 안에서 어떤 일이 벌어지는지에 대해서는 이미 잘 알고 있다.

DependencyHandler가 묵시적 수신 객체이므로 람다 안에서 compile과 같은 DependencyHandler의 메소드를 직접 호출할 수 있다.

```kotlin
class DependencyHandler{
    fun compile(coordinate: String){
        println("Added dependency on $coordinate")
    }
    operator fun invoke (
        body: DependencyHandler.() -> Unit) {
    body()
    }
}
```
이러한 invoke convention으로 인해 DSL API의 유연성이 커졌다.


## 실전 코틀린 DSL

### 중위 호출 연쇄: 테스트 프레임워크 should
우리는 앞서 kotlintest에 대해서 살펴보았다.
이 kotlintest DSL에서 중위 호출을 어떻게 활용하는지 살펴보자.
```kotlin
s should startWith("kot")
```
s에 들어간 값이 kot로 시작하지 않으면 이 단언문은 실패한다.
이 코드는 마치 The s string should start with this constant처럼 읽힌다.
이 목적을 달성하기 위해 should 함수 선언 앞에 infix 변경자를 붙여야 한다.
```kotlin
infix fun <T> T.should(matcher: Matcher<T>) = matcher.test(this)
```
should 함수는 Matcher의 인스턴스를 요구한다.
Matcher는 값에 대한 단언문을 표현하는 제네릭 인터페이스이다.
startWith는 Matcher를 구현하며, 어떤 문자열이 주어진 문자열로 시작하는지 검사한다.
```kotlin
interface Matcher<T> {
    fun test(value: T)
}

class startWith(val prefix: String) : Matcher<String> {
    override fun test(value: String) {
        if(!value.startsWith(prefix))
            throw AssertionError("String $value does not start with $prefix")
    }
}
```
평범한 프로그램이라면 startWith 클래스의 첫 글자가 대문자여야 하지만 DSL에서는 그런 일반적인 명명 규칙을 벗어나야할 때가 있다.
약간 더 기교를 부리면 더 많은 잡음을 제거할 수도 있다.
코틀린테스트 DSL은 그런 기교도 함께 사용한다.

**"kotlin" should start with "kot"**

위 문장은 전혀 코틀린 문장처럼 보이지 않는다.
이 문장이 어떻게 작동하는지 이해하기위해서는 중위 호출을 일반 메소드 호출로 바꿔봐야한다.

```kotlin
"kotlin".should(start).with("kot")  
```  
여기서는 should와 with가 연쇄적으로 중위 호출을 한다는 사실을 알 수 있다.
should는 start라는 싱글턴 객체를 파라미터 타입으로 사용하는 특별한 오버로딩이 있다.
이 오버로딩한 should 함수는 중간 래퍼 객체를 돌려주는데, 이 래퍼 객체안에는 중위 호출 가능한 with 메소드가 포함된다.

```kotlin
object start

infix fun String.should(x: start): StartWrapper = StartWrapper(this)

class StartWrapper(val value: String){
    infix fun with(prefix: String) =
    if(!value.startsWith(prefix))
        throw AssertionError("String $value does not start with $prefix")
    else
        Unit
}
```

여기서 start라는 객체를 파라미터 타입으로 사용하는 이유는, 데이터를 넘기기 위함이 아니라 DSL의 문법을 정의하기 위해 사용되었다.

코틀린테스트 라이브러리는 다른 Matcher도 지원하며, 그 Matcher들은 모두 일반 영어 문장처럼 보이는 단언문을 구성한다.

```kotlin
"kotlin" should end with "in"
"kotlin" should have with "otl"
```
이는 모두 end와 have라는 싱글턴 인스턴스를 취하는 오버로딩 버전이다.
이들은 싱글턴 종류에 따라 각각 EndWrapper, HaveWrapper 인스턴스를 반환한다.
이는 DSL에서도 상대적으로 어려운 방법이다.
그래서 이 기법을 사용한 결과는 꽤나 멋지기 때문에 이런 DSL이 내부에서 어떻게 작동하는지 한 번쯤 분석해볼만 하다.


### 원시 타입에 대한 확장 함수 정의: 날짜 처리
```kotlin
val yesterday = 1.days.ago
val tomorrow = 1.days.fromNow
```
자바 8의 `java.time` API와 코틀린을 사용해 이 API를 구현해보자.

```kotlin
import java.time.Period
import java.time.LocalDate

val Int.days: Period
    get() = Period.ofDays(this)

val Period.ago: LocalDate
    get() = LocalDate.now() - this

val Period.fromNow: LocalDate
    get() = LocalDate.now() + this

println(1.days.ago)
// 2020-05-15
println(1.days.fromNow)
// 2020-05-17
```
코틀린에서는 아무 타입이나 확장 함수의 수신 객체 타입이 될 수 있다.
days 프로퍼티는 Period 타입의 값을 반환한다.
Period는 두 날짜 사이의 간격을 나타내는 JDK 8에 추가된 타입이다.
ago를 지원하기 위해서 다른 확장 프로퍼티를 더 정의해야했다.
이번에는 Period 클래스에 대한 확장 프로퍼티가 필요하다. (번역서에 확장 “함수”가 필요하다고 적혀있는데 오타로 보인다. 511페이지 중간쯤)
그 프로퍼티 타입은 LocalDate로 날짜를 표현한다.
ago 프로퍼티에 사용한 -는 코틀린이 제공하는 확장 함수가 아니다.
LocalDate라는 JDK 클래스에는 코틀린의 - 연산자 convention과 일치하는 인자가 하나뿐인 minus 메소드가 들어있다.

### 멤버 확장 함수: SQL을 위한 내부 DSL
DSL 설계에 확장 함수가 중요한 역할을 하는 모습을 살펴보았다.
이번 절에서는 예전에 언급했던 기법을 더 자세히 살펴본다.
그 기법이란 클래스 안에 확장 함수와 확장 프로퍼티를 선언하는 것이다.
이러한 멤버나 함수를 멤버 확장이라고 부른다.
멤버 확장을 사용하는 몇 가지 예제를 살펴보자.
익스포즈드 프레임워크에서 제공한 SQL을 위한 internal DSL에서 가져온 예제이다.
익스포즈드 프레임워크에서 SQL로 테이블을 다루기 위해서는 Table 클래스를 확장한 객체로 대상 테이블을 정의해야 한다.
```kotlin
object Country: Table() {
    val id = integer("id").autoIncrement().primaryKey()
    val name = varchar("name", 50)
}
```

이 선언은 데이터베이스 테이블과 대응한다.
이 테이블을 만드려면 SchemaUtils.create(Country) 메소드를 호출한다.
Countr 객체에 속한 프로퍼티들의 타입을 살펴보면 각 칼럼에 맞는 타입 인자가 지정된 Column 타입을 볼 수 있다.
id는 Column<Int> 타입이고, name은 Column<String> 타입이다.
익스포즈드 프레임워크의 Table 클래스는 방금 살펴본 두 타입을 포함해 데이터베이스 테이블에 정의할 수 있는 모든 타입을 정의한다.

```kotlin
class Table{
    fun integer(name: String): Column<Int>
    fun varchar(name: String, length: Int): Column<String>
}
```
integer와 varchar 메소드는 각각 순서대로 정수와 문자열을 저장하기 위한 칼럼을 새로 만든다.
이제 각 칼럼의 속성을 지정하기 위해 멤버 확장을 사용해보자.

```kotlin
val id = integer("id").autoIncrement().primaryKey()
```
autoIncrement나 primaryKey 같은 메소드를 사용해 각 칼럼의 속성을 지정한다.
```kotlin
class Table {
    fun <T> Column<T>.primaryKey(): Column<T>
    fun Column<Int>.autoIncrement(): Column<Int>
    ...
}
```
이 두 함수는 Table 클래스 함수의 멤버이다.
멤버 확장으로 정의하는 이유는 메소드가 적용되는 범위를 제한하기 위함이다.
왜냐하면 테이블 밖에서는 이런 메소드를 찾을 수 없어야 하기 때문이다.
SELECT 질의에 다른 멤버 확장 함수를 살펴보자.
Customer와 Country라는 두 테이블이 있다고 가정하고,
각 Customer의 레코드마다 그 고객이 어떤 나라에서 왔는지 나타내는 Country 레코드에 대한 외래키가 존재한다.
다음 코드는 미국에 사는 모든 고객의 이름을 출력한다.

```kotlin
val result = (Country join Customer)
    .select{ Country.name eq "USA" }
result.forEach { println(it[Customer.name])}
```

select 메소드는 Table에 대해 호출되거나 두 테이블을 조인한 결과에 대해 호출될 것이다.
select의 인자는 데이터를 선택할 때 사용할 조건을 기술하는 람다이다.
그러면 eq는 어디서 왔을까?
우리는 eq가 “USA”를 인자로 받는 함수인데 중위 표기법으로 식을 적었다는 사실과 eq도 또 다른 멤버 확장임을 추측할 수 있다.
그 추측은 정답이다.

```kotlin
fun Table.select(where: SqlExpressionBuilder.() -> Op<Boolean>) : Query

object SqlExpressionBuilder {
    infix fun<T> Column<T>.eq(t: T) : Op<Boolean>
    ...
}
```

SqlExpressionBuilder는 조건을 표현할 수 있는 여러 방식을 정의한다.
select의 파라미터 타입이 바로 SqlExpressionBuilder를 수신 객체로 하는 수신 객체 지정 람다다.
따라서 select에 전달되는 람다 본문에서는 SqlExpressionBuilder에 정의가 들어있는 모든 확장함수를 사용할 수 있고
eq 역시 그 중 하나이다.
멤버 확장을 사용해서 각 함수를 사용할 수 있는 맥락을 제어할 수 있다.