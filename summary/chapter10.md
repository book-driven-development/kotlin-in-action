# 애노테이션과 리플렉션
어떤 함수를 호출하려면 어떤 함수가 정의된 클래스의 이름과 함수 이름, 파라미터 이름등을 알아야만 했다.
**애노테이션**과 **리플렉션**을 사용하면 그런 제약을 벗어나 미리 알지 못하는 임의의 클래스를 다룰 수 있다.

## Index

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->


## 애노테이션 선언과 적용

### 애노테이션 적용
애노테이션 적용하기
```kotlin
class MyTest {
    @Test fun testTrue() { ... } //@Test 애노테이션 사용
}
```
```kotlin
@Deprecated("Use removeAt(index) instead.",ReplaceWith("removeAt(index)"))
fun remove(index: Int) { ... }
```
#### 애노테이션 문법
- 클래스를 애노테이션 인자로 지정할 때 `@MyAnnotation(MyClass:class)`처럼 `::Class`를 클래스 이름 뒤에 넣어야 한다
- 다른 애노테이션을 인자로 지정할 때는 인자로 돌아가는 애노테이션의 이름 앞에 @를 넣지 않아야 한다. 
    - 예를들어 ReplaceWith는 애노테이션이다 하지만 Deprecated 애노테이션의 인자로 들어가므로 ReplaceWith앞에 @를 사용하지 않는다
- 배열을 인자로 지정하려면 `@RequestMapping(path=arrayOf("/foo","/bar"))` 처럼 arrayOf함수를 사용한다

애노테이션 인자를 컴파일 시점에 알 수 있어야 한다.
따라서 임의의 프로퍼티를 인자로 지정할 수는 없다
프로퍼티를 애노테이션 인자로 사용하려면 그 앞에 `const`를 붙여야 한다

### 애노테이션 대상
코틀린 소스코드에서 한 선언을 컴파일러가 여러 자바코드에 선언과 대응하느 경우가 자주 있다.
코틀린의 프로퍼티는 자바의 게터 세터와 대응한다.
따라서 애노테이션을 붙일 때 이런 요소중 어떤 요소에 애노테이션을 붙일 지 표시해야 한다.

```kotlin
class HasTempFolder {
    @get:Rule //프로퍼티가 아니라 게터에 애노테이션이 붙는다
    val folder = TemporaryFolder() 
}
```

 애노테이션 대상 지정 
- property : 프로퍼티 전체. 자바에서 선언된 애노테이션에는 이 사용지점을 사용할 수 없음
- field : 프로퍼티에 의해 생성되는(뒷받침하는) 필드
- get : 프로퍼티 게터
- set : 프로퍼티 세터
- receiver : 확장 함수나 프로퍼티의 수신 객체 파라미터
- param : 생성자 파라미터
- setparam : 세터 파라미터
- delegate : 위임 프로퍼티 위임 인스턴스를 담아 둔 필드
- file : 파일 안에 선언된 최상위 함수와 프로퍼티를 담아두는 클래스  

자바 API를 애노테이션으로 제어하기
- `@JvmName`은 코틀린 선언이 만들어내는 자바 필드나 메소드 이름을 변경한다
- `@JvmStatic`을 메소드, 객체 선언, 동반 객체에 적용하면 그 요소가 자바 정적 메소드로 노출된다
- `@JvmOverloads`를 사요하면 디폴트 파라미터 값이 있는 함수에 대해 컴파일러가 자동으로 오버로딩한 함수를 생성해준다
- `@JvmField`를 프로퍼티에 사용하면 게터나 세터가 없는 공개된(public) 자바 필드로 프로퍼티를 노출시킨다

### 애노테이션을 활용한 JSON 직렬화 제어
- `@JsonExclude` 애노테이션을 사용하면 직렬화나 역직렬화 시 그 프로퍼티를 무시할 수 있다
- `@JsonName` 애노테이션을 사용하면 프로퍼티를 표현하는 키/값 쌍의 키로 프로퍼티 이름 대신 애노테이션이 지정한 이름을 쓰게 될 수 있다

```kotlin
data class Person(
    @JsonName("alias") val firstName: String,
    @JsonExclude val age: Int? = null
)
```

### 애노테이션 선언
애노테이션 선언은 오직 선언이나 식과 관련있는 메타데이터 구조를 정의하기 때문에
내부에 아무 코드도 들어있을 수 없다.

파라미터가 없는 애노테이션 선언
```kotlin
annotation class JsonExclude
```

파라미터가 있는 애노테이션 선언
```kotlin
// 애노테이션 클래스에서는 모든 파라미터 앞에 val을 붙여야 한다
annotation class JsonName(val name: String)
```

### 메타애노테이션: 애노테이션을 처리하는 방법 제어
자바와 마찬가지로 코틀린 애노테이션 클래스에도 애노테이션을 붙일 수 있다
이를 **메타애노테이션** 이라 한다.

```kotlin
@Target(AnnotationTarget.PROPERTY)
annotation class JsonExclude
```
`@Target`메타애노테이션은 애토네이션을 적용할 수 있는 요소의 유형을 지정한다.
애노테이션 클래스에 대해 구체적인 타겟을 지정하지않으면 모든 선언에 적용할 수 있는 애노테이션이 된다.

AnnotationTarget Enum은 클래스, 파일, 프로퍼티, 프로퍼티 접근자, 타입, 식등에 대한 Enum정의가 들어있다
필요하다면 둘 이상을 한꺼번에 선언할 수도 있다

`@Retention`메타애노테이션은 정의중인 애노테이션 클래스를 소스 수준에서만 유지할지, .class파일에 저장할지, 실행 시점에 리플렉션을 사용해 접근할 수 있게 할지 지정하는 메타애노테이션이다.
자바 컴파일러는 기본적으로 애노테이션을 .class 파일에는 저장하지만 런타임에는 사용할 수 없게 된다.
하지만 대부분의 애노테이션은 런타임에도 사용할 수 있어야 하므로 코틀린에서는 기본적으로 `@Retention`을 RUNTIME으로 지정한다.

### 애노테이션 파라미터 클래스 사용
선언
```kotlin
annotation class DeserializeInterface(val targetClass: KClass<out Any>)
```

사용
```kotlin
@DeserializeInterface(CompanyImple::class)
```

### 애노테이션 파라미터로 제네릭 클래스 받기
선언
```kotlin
annotation Class CustomSerializer(
    val serializerClass: KClass<out ValueSerializer<*>>
)
```


## 리플렉션: 실행 시점에 코틀린 객체 내부 관찰
리플렉션은 실행 시점에 객체의 프로퍼티와 메소드에 접근할 수 있게 해주는 방법이다.
타입과 관계없이 객체를 다뤄야하거나 객체가 제공하는 메소드나 프로퍼티 이름을 오직 실행 시점에만 알 수 있는 경우가 있다.
JSON 직렬화 라이브러리는 어떤 객체든 JSON으로 변경할 수 있어야 하고, 실행 시점에 되기 전까지는 라이브러리가 직렬화할 프로퍼티나 클래스에 대한 정보를 알 수 없다 이런경우 리플렉션을 사용해야 한다.

### 코틀린 리플렉션 API: KClass, KCallable, KFunction, KProperty

#### KClass
KClass는 클래스 안에 모든 선언을 열거하고 각 선언에 접근하거나 클래스의 상위 클래스를 얻는 등의 작업이 가능하다
`MyClass::class`라는 식을 쓰면 KClass 인스턴스를 얻을 수 있다.
```kotlin
interface KClass<T: Any>(
    val simpleName: String?,
    val qualifiedName: String?,
    val members: Collection<KCallable<*>>,
    val constructors: Collection<KFunction<T>>
    val nestedClasses: Collection<KClass<*>>
)
```

#### KCallable
클래스의 모든 멤버 목록이 KCallable 인스턴스의 컬렉션이다.
KCallable은 함수와 프로퍼티를 아우르는 공통 상위 인터페이스 이다.
```kotlin
interface KCallable<out R> {
    fun call(vararg args: Any?): R
}
```

#### KFunction
```kotlin
fun foo(x: Int) = println(x)

fun main(args: Array<String>) {
    val kFunction = ::foo
    kFunction.call(42)
}
```
`::foo`식의 값 타입이 리플렉션 API에 있는 KFunction 클래스의 인스턴스이다.

#### KProperty
```kotlin
var counter = 0

fun main(args: Array<String>) {
    val kProperty = ::counter
    kProperty.setter.call(21) // 리플렉션 기능을 통해 세터를 호출하면서 21을 인자로 넘긴다
    println(kProperty.get())
}
```

## 요약
- 코틀린에서 애노테이션을 적용할 때 사용하는 문법은 자바와 거의 같다
- 코틀린에서 자바보다 더 넒은 대상에 애노테이션을 적용할 수 있다. 그런 대상으로는 파일과 식을 들 수 있다
- 애노테이션 인자로 원시 타입 값, 문자열, 이넘 클래스 참조, 다른 애노테이션 클래스의 인스턴스, 그리고 지금까지 말한 여러 유형의 값으로 이뤄진 배열을 사용할 수 있다
- `@get:Rule`을 사용해 애노테이션의 사용 대상을 명시하면 한 코틀린 선언이 여러 가지 바이트코드 요소를 만들어내는 경우 정확히 어떤 부분에 애노테이션을 적용할지 지정할 수 있다
- 애노테이션 클래스를 정의할 때 본문이 없고 주 생성자의 모든 파라미터를 val 프로퍼티로 표시한 코틀린 클래스를 사용한다
- 메타애노테이션을 사용해 대상, 애노테이션 유지 방식 등 여러 애노테이션 특성을 지정할 수 있다
- 리플렉션 API를 통해 실행 시점에 객체의 메소드와 프로퍼티를 열거하고 접근할 수 있다. 리플렉션 API에는 클래스(KClass), 함수(KFunction)등 여러 종류의 선언을 표현하는 인터페이스가 들어있다
- 클래스를 컴파일 시점에 알고 있다면 KClass 인스턴스를 얻기 위해 ClassName::class를 사요한다. 하지만 실행 시점에 obj 변수에 담긴 객체로부터 KClass 인스턴스를 얻기 위해서는 obj.javaClass.kotlin을 사용한다
- KFunction과 KProperty 인터페이스는 모두 KCallable을 확장한다. KCallable은 제네릭 call 메소드를 제공한다
- KCallable.callBy 메소드를 사용하면 메소드를 호출하면서 디폴트 파라미터 값을 사용할 수 있다
- KFunction0, KFunction1 등의 인터페이스는 모두 파라미터 수가 다른 함수를 표현하며, invoke 메소드를 사용해 함수를 호출할 수 있다
- KProperty0는 최상위 프로퍼티나 변수, KProperty1은 수신 객체가 있는 프로퍼티에 접근할 때 쓰는 인터페이스다. 두 인터페이스 모두 get 메소드를 사용해 프로퍼티 값을 가져올 수 있다. 
