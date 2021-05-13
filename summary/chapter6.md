# 코틀린 타입 시스템

## Index

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->


## 널 가능성
코틀린을 비롯한 최신 언어에서 null에 대한 접근 방법은 가능한 NPE 문제를 실행시점에서
컴파일 시점으로 옮기는 것이다.

### 널이 될 수 있는 타입
널이 될 수 있는 타입은 변수 뒤에 ?를 붙힌다.
```kotlin
fun strLenSafe(s: String?): Int =
    if (s != null) s.length else 0
```

### 타입의 의미
코틀린은 널이 될 수 있는 타입은 이런 문제에 대한 종합적인 해법을 제공한다.
널이 될 수 있는 타입과 널이 될 수 없는 타입을 구분하면 각 타입의 값에 대해 어떤 연산이 가능할 지 명확히 이해할 수 있고, 실행 시점에 예외를 발생시킬 수 있는 연산을 판단할 수 있다.

### 안전한 호출 연산자: ?.
코틀린이 제공하는 가장 유용한 도구 중 하나가 안전한 호출 연산자인 `?.` 이다.
`?.` 은 null 검사와 메소드 호출을 한 번의 연산으로 수행한다

```kotlin
fun printAllCaps(s: String?) {
    val allCaps: String? = s?.toUpperCase() // if (s != null) s.toUpperCase() else null
    println(allCaps)
}
```

안전한 호출 연쇄
```kotlin
class Address(val streetAddress: String, val zipCode: Int,
              val city: String, val country: String)

class Company(val name: String, val address: Address?)

class Person(val name: String, val company: Company?)

fun Person.countryName(): String {
   val country = this.company?.address?.country
   return if (country != null) country else "Unknown"
}
```

### 엘비스 연산자: ?:
null 대신 사용할 디폴트 값을 지정할 때 편리하게 사용할 수 있는 연산자를 제공한다
이 연산자를 엘비스 연산자로 한다. `?:`
```kotlin
fun strLenSafe(s: String?): Int = s?.length ?: 0
```

```kotlin
val address = person.company?.address
      ?: throw IllegalArgumentException("No address")
```

### 안전한 캐스트: as?
자바의 타입 캐스트와 마찬가지로 대상 값을 as로 지정한 타임으로 바꿀 수 없으면
ClassCastException이 발생한다.

코틀린은 `as?`연산자를 사용하여 어떤 값을 지정한 타입으로 캐스트 한다.
`as?`는 값을 대상 타입으로 변환할 수 없으면 null을 반환한다.

```kotlin
class Person(val firstName: String, val lastName: String) {
   override fun equals(o: Any?): Boolean {
      val otherPerson = o as? Person ?: return false

      return otherPerson.firstName == firstName &&
             otherPerson.lastName == lastName
   }

   override fun hashCode(): Int =
      firstName.hashCode() * 37 + lastName.hashCode()
}
```

### 널 아님 단언: !!
**널 아님 단언**은 코틀린에서 널이 될 수 있는 타입의 값을 다룰 때 사용할 수 있는 도구중 가장 단순하면서도
가장 무딘 도구다. `!!`를 사용하여 어떤 값이든 널이 될 수 없는 타입으로 강제로 바꿀 수 있다.
해당 값이 null일때 NPE가 발생한다.

```kotlin
fun ignoreNulls(s: String?) {
    val sNotNull: String = s!!
    println(sNotNull.length)
}
```

### let 함수
let 함수를 사용허면 널이 될 수 있는 식을 쉽게 다를 수 있다.
```kotlin
fun sendEmailTo(email: String) {
    println("Sending email to $email")
}

fun main(args: Array<String>) {
    var email: String? = "yole@example.com"
    email?.let { sendEmailTo(it) }
    email = null
    email?.let { sendEmailTo(it) }
}
```

### 나중에 초기화 할 프로퍼티

널 아님 단언을 사용해 널이 될 수 있는 프로퍼티 접근하기
```kotlin
class MyTest {
    private var myService: MyService? = null

    @Before fun setUp() {
        myService = MyService()
    }

    @Test fun testAction() {
        Assert.assertEquals("foo",
            myService!!.performAction())
    }
}
```

나중에 초기화 하는 프로퍼티 사용하기
```kotlin
class MyTest {
    private lateinit var myService: MyService

    @Before fun setUp() {
        myService = MyService()
    }

    @Test fun testAction() {
        Assert.assertEquals("foo",
            myService.performAction())
    }
}
```

### 널이 될 수 있는 타입 확장
isNullOrBlank는 널이 될 수 있는 타입의 확장 함수이다.
```kotlin
fun verifyUserInput(input: String?) {
    if (input.isNullOrBlank()) { // 안전한 호출을 하지 않아도 된다.
        println("Please fill in the required fields")
    }
}
```

### 타입 파라미터의 널 가능성
널이 될 수 있는 타입 파라미터
```kotlin
fun <T> printHashCode(t: T){
    println(t?.hashCode()) // t가 null이 될  수 있다
}
```
```kotlin
printHashCode(null) // "T"의 타입은 "Any?"로 추론된다.
```

타입 파라미터에 대해 널이 될 수 없는 상한을 사용하기
```kotlin
fun <T : Any> printHashCode(t: T){ //"T"는 이제 널이 될 수 없다
    println(t?.hashCode()) 
}
```

### 널 가능성과 자바
- 자바코드에서 어노테이셔으로 표시된 널 가능성 정보가 있으면 코틀린도 그 정보를 활용한다


## 코틀린의 원시 타입
`Int`, `Boolean`, `Any`등의 원시 타입에 대해 살펴본다

### 원시 타입: Int, Boolean 등
자바는 원시타입과 참조타입을 구붆나다
원시타입의 변수에는 그 값이 직접 들어가지만
참조타입의 변수에는 메모리상의 객체 위치가 들어간다

코틀린은 원시 타입과 래퍼 타입을 구분하지 않으므로 항상 같은 타입을 사용한다.
```kotlin
fun showProgress(progress: Int) {
    val percent = progress.coerceIn(0, 100)
    println("We're ${percent}% done!")
}
```
더 나아가 코틀린에서는 숫자 타입 등 원시타입값에 대해 메소드를 호출할 수 있다

#### 자바 원시 타입에 해당하는 코틀린의 원시타입
- 정수 타입  Byte, Short, Int, Long
- 부동소수점 수 타입 : Float, Double
- 문자 타입 Char
- 불리언 타입 Boolean

### 널이 될 수 있는 원시 타입: Int?, Boolean? 등
null 참조를 자바의 참조 타입의 변수에만 대입할 수 있기 때문에 널이 될 수 있는 코틀린 타입은 
자바 원시 타입으로 표시할 수 없다

```kotlin
data class Person(val name: String,
                  val age: Int? = null) {

    fun isOlderThan(other: Person): Boolean? {
        if (age == null || other.age == null)
            return null
        return age > other.age
    }
}
```

### 숫자 변환
코틀린은 한 타입의 숫자를 다른 타입의 숫자로 자동 변환하지 않는다.
결과 타입이 허용하는 숫자의 범위가 원래 넓은 경우 조차도 자동변환은 불가능 하다.

코틀린은 모든 원시타입에 대한 변환함수를 제공한다.
`toByte()`, `toShort()`, `toChar()`

코틀린은 개발자의 혼란을 피하기 위해 타입 변환을 명시하기로 결정했다.
특히 박스 타입을 비교하는 경우 문제가 많다
```kotlin
val x = 1  
val list = listOf(1L,2L,3L) 
x in list // 묵시적 타입 변환으로 인해 flase
```
```kotlin
 val x = 1
println(x.toLong() in listOf(1L, 2L, 3L)) 
```

>> **원시 타입 리터럴**
>> 코틀린은 소스코드에서 단순한 10진수 외에 아래와 같은 숫자 리터럴을 허용한다
>> L 접미사가 붙은 Long 타입 리터럴: 123L
>> 표준 부동소수점 표기법을 사용한 Double 타입 리터럴: 0.12, 2.0, 1.2e10
>> f나 F접미사가 붙은 Float 타입 리터럴: 123.4f, 456F, 1e3f
>> 0x나 0X접두사가 붙은 16진 리터럴: 0xCAFBBABE
>> 0b나 0B 접두사가 붙은 2진 리터럴: 0b0000101

### Any, Any?: 최상위 타입
자바에서 Object 클래스가 계층의 최상위 타입이듯
코틀린에서는 Any타입이 모든 널이 될수 없는 타입의 조상이다
코틀린에서 널을 포함하는 모든 값을 대힙할 변수를 선언하려면 Any? 타입을 사용한다

### Unit타입: 코틀린의 void
코틀린 Unit타입은 자바 void와 같은 기능을 한다

### Noting 타입: 이 함수는 결코 정상적으로 끝나지 않는다
코틀린에는 결코 성공적으로 값을 돌려주는 일이 없으므로 '반환값'이라는 개념 자체가 의미없는 함수가 일부 존재한다.

에를들어 테스트 라이브러리들은 fail이라는 함수를 제공하는 경우가 많다 fail은 특별한 메세지가 들어있는 예외를 던져서
현재 테스트를 실패시킨다. 다른 예로 무한 루프를 도는 함수도 결코 값을 반환하며, 정상적으로 끝나지 않는다.
이런 경우를 표현하기위해 코틀린에서는 `Nothing`이라는 특별한 반환타입이 있다.

```kotlin
fun fail(message: String): Nothing {
    throw IllegalStateException(message)
}
```

`Nothing` 타입은 아무 값도 포함하지 않는다. 따라서 Nothing은 함수의 반환타입이나
반환 타입으로 쓰일 타입 파라미터로만 쓸 수 있다.
그 외 다른 용도로 사용하는 경우 Nothing 타입의 변수를 선언하더라도 그 변수에 아무 값도 저장할 수 없으므로
아무의미도 없다.


## 컬렉션과 배열

### 널 가능성과 컬렉션
`List<Int?>`는 Int?타입의 값을 저장할 수 있다.
```kotlin
fun readNumbers(reader: BufferedReader): List<Int?> {
    val result = ArrayList<Int?>()
    for (line in reader.lineSequence()) {
        try {
            val number = line.toInt()
            result.add(number)
        }
        catch(e: NumberFormatException) {
            result.add(null)
        }
    }
    return result
}
```
리스트 안의 각 원소 값이 널이 될 수 있다. -> 리스트자체는 항상 널이 아니다 -> `List<Int?>`
전체 리스트가 널이될 수 있다 -> 리스트를 가리키는 변수에는 널이들어갈 수 있지만 리스트 안에는 널이 아닌 값만 들어간다 -> `List<Int>?`

### 읽기 전용과 변경가능한 컬렉션
코틀린에서는 컬렉션 안의 데이터에 접근하는 인터페이스와 컬렉션 안의 데이터를 변경하는 인터페이스를 분리하였다.
컬렉션의 데이터를 수정하려면 MutableCollection을 확장하는 컬렉션을 사용한다.
MutableCollection이 아닌 Collection 타입의 인자를 받는다면 그 함수는 컬렉션을 변경하지 않고 읽기만 한다
```kotlin
fun <T> copyElements(source: Collection<T>,
                     target: MutableCollection<T>) {
    for (item in source) {
        target.add(item)
    }
}

fun main(args: Array<String>) {
    val source: Collection<Int> = arrayListOf(3, 5, 7)
    val target: MutableCollection<Int> = arrayListOf(1)
    copyElements(source, target)
    println(target)
}
```

### 코틀린 컬렉션과 자바
컬렉션 생성 함수
|컬렉션 타입| 읽기 전용 타입| 변경 가능 타입|
|---|---|---|
|List|listOf|mutableListOf, arrayListOf|
|Set|setOf|mutableSetOf, hashSetOf, linkedSetOf, sortedSetOf|
|Map|mapOf|mutableMapOf, hashMapOf, linkedMapOf, sortedMapOf|

```kotlin
// collections.kt
fun printInUppercase(list: List<String>) {
    println(CollectionUtils.uppercaseAll(list))
    println(list.first())
}

fun main(args: Array<String>) {
    val list = listOf("a", "b", "c")
    printInUppercase(list)
}
```

### 컬렉션을 플랫폼 타입으로 다루기
코틀린에서 사용할 컬렉션 타입을 잘 반영해야 한다
- 컬렉션이 널이될 수 있는가?
- 컬렉션의 원소가 널이 될 수 있는가?
- 오버라이드 하는 메소드가 컬렉션을 변경할 수 있는가?

### 객체의 배열과 원시 타입의 배열
배열 사용하기
```kotlin
fun main(args: Array<String>) {
    for (i in args.indices) {
         println("Argument $i is: ${args[i]}")
    }
}
```

코틀린의 배열은 타입 파라미터를 받는 클래스다.
배열의 원소 타입은 바로 그 타입파라미터에 의해 정해진다
코틀린에서 배열을 만들때는
- arrayOf 함수에 원소를 넘기면 배열을 만들 수 있다
- arrayOfNulls 함수에 정수 값을 인자로 넘기면 모든 원소가 null이고 인자로 넘긴 값과 크기가 같은 배열을 만들 수 있다
- Array 생성자는 배열 크기와 람다를 인자로 받아서 람다를 호출해서 각 배열 원소를 초기화 해 준다
arrayOf를 쓰지 않고 각 원소가 널이 아닌 배열을 만들어야 하는 경우 이 생성자를 사용한다

알파벳으로 이뤄진 배열
```kotlin
fun main(args: Array<String>) {
    val letters = Array<String>(26) { i -> ('a' + i).toString() }
    println(letters.joinToString(""))
}
```

컬렉션을 vararg 메소드에 넘기기
```kotlin
fun main(args: Array<String>) {
    val strings = listOf("a", "b", "c")
    println("%s/%s/%s".format(*strings.toTypedArray())) // vararg 인자를 넘기기 위해 스프레드 연산자 사용
}
```

원시 타입의 배열을 만드는 방법
- 각 배열의 타입 생성자는 size 인자를 받아서 원시 타입의 디폴트값 으로 초기화된 size 크기의 배열을 반환한다
- 팩토리 함수(IntArray를 생선하는 intArrayOf등)는 여러 값을 가변 인자로 받아서 그런 값이 들어간 배열을 반환한다
- 크기와 람다를 인자로 받는 생성자를 사용한다

```kotlin
  val squares = IntArray(5) { i -> (i+1) * (i+1) }
```


## 요약
- 코틀린은 널이 될 수 있는 타입을 지원해 NullPointerException 오류를 컴파일 시점에 감지할 수 있다
- 코틀린의 안전한 호출 `?.`, 앨비스 연산자 `?:`, 널 아님 단언 `!!`, `let`함수 등을 사용하면 널이 될 수 있는 타입을 간결한 코드로 다룰 수 있다
- `as?` 연산자를 사용하면 값을 다른 타입으로 반환하는 것과 변환이 불가능한 경우 처리하는것을 한꺼번에 편하게 가능하다
- 자바에서 가져온 타입은 코틀린에서 플랫폼 타입으로 취급된다. 개발자는 플랫폼 타입을 널이 될 수 있는 타입으로도, 널이 될 수 없는 타입으로도 사용 가능하다
- 코틀린에서는 수를 표현하는 타입(Int)이 일반 클래스와 똑같이 생겼고 일반 클래스와 똑같이 동작한다. 하지만 대부분 컴파일러는 숫자 타입을 자바 원시 타입으로 컴파일 한다
- 널이 될 수 있는 원시 타입(Int?)은 자바의 박싱한 원시타입(Integer)에 대응한다
- Any 타입은 모든 타입의 조상이며 자바의 Object에 해당한다
- 정상적으로 끝나지 않는 함수의 반환 타입을 지정할 때 `Nothing` 타입을 사용한다
- 코틀린 컬렉션은  표준 자바 컬렉션 클래스를 사용한다. 하지만 코틀린은 자바보다 컬렉션을 더 개선해서 일기 전용 컬렉션과 변경 가능한 컬렉션을 구분해 제공한다
- 자바 클래스를 코틀린에서 확장하거나 자바 인터페이스를 코틀린에서 구현하는 경우 메소드 파라미터의 널 가능성과 변경 가능성에 대해 깊이 생각해야 한다.
- 코틀린의 Array 클래스는 일반 제네릭 클래스처럼 보인다. 하지만 Array는 자바 배열로 컴파일 된다
- 원시 타입의 배열은 IntArray와 갘이 각 타입의 특별한 배열로 표현된다.