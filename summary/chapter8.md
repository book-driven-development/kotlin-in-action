# 고차 함수: 파라미터와 반환 값으로 람다 사용

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
        transform: ((T) -> String)? = null
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

### 람다를 활용한 중복 제거


## 인라인 함수: 람다의 부가 비용 없애기

### 인라이닝이 작동하는 방식

### 인라인 함수의 한계

### 컬렉션 연란 인라이닝

### 함수를 인라인으로 선언해야 하는 경우

### 자원 관리를 위해 인라인된 람다 사용


## 고차 함수 안에서 흐름 제어

### 람다 안의 return문: 람다를 둘러싼 함수로부터 반환

### 람다로부터 반환: 레이블을 사용한 return

### 무명 함수: 기본적으로 로컬 return


## 요약