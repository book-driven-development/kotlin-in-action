# 코틀린이란 무엇이며, 왜 필요한가?

## Index
- [코틀린이란 무엇이며, 왜 필요한가?](#코틀린이란-무엇이며-왜-필요한가)
  - [코틀린 맛보기](#코틀린-맛보기)
  - [코틀린의 주요 특성](#코틀린의-주요-특성)
    - [대상 플랫폼 : 서버, 안드로이드 등 자바가 실행되는 모든 곳](#대상-플랫폼-서버-안드로이드-등-자바가-실행되는-모든-곳)
    - [정적 타입 지정 언어](#정적-타입-지정-언어)
      - [정적 타입언어와 동적 타입 언어](#정적-타입언어와-동적-타입-언어)
      - [정적 타입 지정의 장점](#정적-타입-지정의-장점)
      - [Nullable 타입 지원](#nullable-타입-지원)
    - [함수형 프로그래밍과 객체지향 프로그래밍](#함수형-프로그래밍과-객체지향-프로그래밍)
      - [함수형 프로그래밍의 핵심 개념](#함수형-프로그래밍의-핵심-개념)
      - [함수형 프로그래밍의 장점](#함수형-프로그래밍의-장점)
    - [무료 오픈 소스](#무료-오픈-소스)
  - [코틀린 응용](#코틀린-응용)
    - [코틀린 서버 프로그래밍](#코틀린-서버-프로그래밍)
    - [코틀린 안드로이드 프로그래밍](#코틀린-안드로이드-프로그래밍)
  - [코틀린 철학](#코틀린-철학)
    - [실용성](#실용성)
    - [간결성](#간결성)
    - [안전성](#안전성)
    - [상호 운용성](#상호-운용성)
  - [코틀린 도구 사용](#코틀린-도구-사용)
    - [코틀린 코드 컴파일](#코틀린-코드-컴파일)
    - [인텔리J 아이더어와 안드로이드 스튜디오 플러그인](#인텔리j-아이더어와-안드로이드-스튜디오-플러그인)
    - [대화형 셸](#대화형-셸)
    - [이클립스 플러그인](#이클립스-플러그인)
    - [온라인 놀이터](#온라인-놀이터)
    - [자바-코틀린 변환하기](#자바-코틀린-변환하기)
  - [요약](#요약)


## 코틀린 맛보기
데이터클래스, 파라미터 디폴트 값, 최상위 함수, 이름이 붙은 파라미터, 람다식과 엘비스연산자, 문자열 템플릿
```kotlin
data class Person(val name: String, val age: Int? = null)

fun main(args: Array<String>){
    val persons = listOf(Person("영희"), Person("철수", age = 29))
    val oldest = persons.maxBy { it.age ?: 0}
    println("나이가 가장 많은 사람 : $oldest")
}
```

## 코틀린의 주요 특성

### 대상 플랫폼 : 서버, 안드로이드 등 자바가 실행되는 모든 곳
코틀린의 주 목적은 현재 자바가 사용되고 있는 모든 용도에 적합하면서도 더 간결하고 생산적이며 안전한 대체 언어를 제공하는 것이다.
- 서버상의 코드(웹 애플리케이션의 백앤드)
- 안드로이드 디바이스에서 실행되는 모바일 어플리케이션

### 정적 타입 지정 언어
자바와 마찬가지로 코틀린은 **정적 타입**<sup>statically typed</sup> 지정 언어다.

#### 정적 타입언어와 동적 타입 언어
- 정적 타입 언어 
   -  컴파일 타임시점에 타입을 알 수 있다
   -  객체의 필드나 메소드를 사용할 때마다 컴파일러가 타입을 검증해준다
- 동적 타입 언어  
  - 타입과 관계없이 모든 값을 변수에 넣을 수 있다
  - 메소드나 필드 접근에 대한 검증이 실행 시점에 일어난다
  - 코드가 짧아지고 데이터 구조를 유용하게 생성하고 사용할 수 있다
  - 컴파일시에 에러가 발생하지않고 실행시에 에러가 발생한다  

#### 정적 타입 지정의 장점
하지만 코틀린은 자바와 달리 모든 변수의 타입을 직접 명시할 필요가 없다.  
코틀린 컴파일러가 문맥으로부터 자동으로 타입을 유추한다.  
이를 **타입 추론** 이라 한다.  

장점
- 성능 : 실행 시점에 어떤 메소드를 호출할지 알아내는 과정이 필요없으므로 메소드 호출이 더 빠르다
- 신뢰성 : 컴파일러가 프로그램 정확성을 검증하기 때문에 실행 시 프로그램이 오류로 중단될 가능성이 더 적어진다
- 유지 보수성 : 코드에서 다루는 객체가 어떤 타입에 속하는지 알 수 있기 때문에 처음 보는 코드를 다룰 때 더 쉽다
- 도구 지원 : 정적 타입 지정을 활용하여 더 안전하게 리펙토링 할 수 잇고, 도구는 더 정확한 코드 완성 기능을 제공할 수 있다

#### Nullable 타입 지원
널이 될 수 있는 타입을 지원함에 따라 컴파일 시점에 널 포인터 예외가 발생할 수 있는지 여부를
검사할 수 있어서 신뢰성을 더 높일 수 있다.

### 함수형 프로그래밍과 객체지향 프로그래밍

#### 함수형 프로그래밍의 핵심 개념
- 일급 시민인<sup>first-class</sup>함수
    - 함수를 일반 값처럼 다룰 수 있다
    - 함수를 변수에 저장할 수 있다
    - 함수를 인자로 다른 함수에 전달할 수 있다
    - 함수에서 새로운 함수를 만들어 반환할 수 있다
- 불변셩
    - 일단 만들어지고나면 내부 상태가 절대로 바뀌지 않는 불변 객체를 사용해 프로그램을 작성한다
- 부수 효과 없음
    - 입력이 같으면 항상 같은 출력을 내는다
    - 다른 객체의 상태를 변경하지 않는다
    - 함수 외부나 다른 바깥 환경과 상호작용하지 않는 순수함수를 사용한다

#### 함수형 프로그래밍의 장점
1. 간결성 
    -  함수를 값처럼 활용할 수 있어 강력한 추상화를 할 수 있다
    -  강력한 추상화로 코드 중복을 막을 수 있다
2. 다중 스레드를 사용해도 안전하다
    - 불변 데이터 구조를 사용하고 순수함수를 데이터구조에 적용한다면 다중 스레드 환경에서 여러스레드가 변경할 수 없다
    - 복잡한 동기화를 적용하지 않아도 된다
3. 테스트하기 쉽다
    - 순수 함수는 준비코드가 없이 독립적으로 테스트 할 수 있다

### 무료 오픈 소스
코틀린 언어와 컴파일러, 라이브러리 및 코틀린과 관련된 모든 도구는 모두 오픈소스이며, 어떤 목적에든 무료로 사용할 수 있다.  


## 코틀린 응용

### 코틀린 서버 프로그래밍
서버 프로그래밍에서 자바가 널리 사용되어 왔다.   
이런 환경에서 자바 코드와 매끄럽게 상호 운용할 수 있다는 점이 코틀린의 큰 장점이다.  
자바클래스를 코틀린으로 확장해도 아무 문제가 없으며, 코틀린 클래스 안의 메소드나 필드에 특정 자바 어노테이션을 붙여야 하는 경우에도 아무 문제가 없다.  

### 코틀린 안드로이드 프로그래밍
- 다양항 디바이스에 대해 서비스의 신뢰성을 보장하면서 더 빠르게 개발해 배포 가능
- 코틀린 언어의 특성과 안드로이드 프레임워크의 특별한 컴파일러 플러그인 지원을 조합하면 안드로이드 애플리케이션 개발의 생산성을 높일 수 있다
- 코틀린 타입 시스템으로 null값을 정확히 추적하여 크래시 문제를 줄여준다

```kotlin
verticalLayout {
    val name = editText()
    button("Say Hello") {
        onClick { toast("Hello, ${name.text}!") }
    }
}
```  

## 코틀린 철학

### 실용성
- 검증된 해법과 기능에 의존하여 언어의 복잡도가 줄인다
- 특정 프로그래밍 스타일이나 패러다임을 사용하는것을 강제로 요구하지않는다
- 항상 도구 활용을 염두에두고 설계돼 왔다

### 간결성
- 코드가 간결하여기존 코드의 내용 파악하기가쉽다
    - 게터, 세터, 생성자 파라미터필드 대엡하기 위한 번거로운 준비코드를 묵시적으로 제공
- 다양한 표준 라이브러리를 제공하여 반복되거나 길어지는 코드들을 대치 가능하다
- 코드가 간결하면 생산성을 향상시켜주고 개발을 더 빠르게 진행할 수 있다

### 안전성
- 일부 유형 오류를 프로그램 설게가 원천적으로 방지해준다
- 실행시점에 오류를 발생시키지않고 컴파일시점에 오류를 발생하여 오류를 더 방지해준다

### 상호 운용성
- 자바와 코틀린 소스파일을 자유롭게 내비게이션 할 수 있다
- 여러 언어로 이뤄진 프로젝트를 디버깅하고 서로 다른 언어로 작성된 코드를 언어와 관계없이 한 단계씩 실행할 수 있다
- 자바 메소드를 리펙토링해도 그 메소드와 관련 있는 코틀린 코드까지 제대로 변경된다
- 코틀린 메소드를 리펙토링해도 자바 코드까지 모두 자동으로 변경된다


## 코틀린 도구 사용

### 코틀린 코드 컴파일
코틀린 컴파일러는 자바 컴파일러가 자바 소스코드를 컴파일 할 때와 마찬가지로 코틀린 소스코드를 분석해서 .class 파일을 만들어낸다.  
만들어진 .class 파일은 개발중인 어플리케이션의 유형에 맞는 표준 패키징 과정을 거쳐 실행될 수 있다.  

![image](https://user-images.githubusercontent.com/39984656/115274069-3eb48780-a17b-11eb-9711-becc5e485721.png)
  
시렞로 개발을 진행한다면 프로젝트를 컴파일하기 위해 메이븐, 그레이들 등의 빌드세스템을 사용하는데, 코틀린은 이러한 빌드 시스템과 호환된다.

### 인텔리J 아이더어와 안드로이드 스튜디오 플러그인
코틀린을 사용할 수 있는 개발환경중에서 가장 다양한 기능을 제공한다.

### 대화형 셸
코트린 코드를 빨리 시험해보고 싶다면 대화셜 셸을 사용하면 된다. REPL<sup>read-eval-print loop<sup>이라고도 부른다.  
REPL에서 코틀린 코드를 한 줄 입력하면 즉시 코드를 실행한 결과를 볼 수 있다.  

### 이클립스 플러그인
이클립스에서도 코틀린 코드완성이나 내비게이션들 필수기능을 모두 제공한다.  

### 온라인 놀이터
웹상에서 코드를 작성하고 컴파일하여 실행할 수 있는 사이트도 있다.  

### 자바-코틀린 변환하기
인텔리J 아이디어에서 변환기를 사용하여 자바 파일을 코틀린으로 바로 변환해볼 수 있다.  


## 요약
- 코틀린은 타입 추론을 지원하는 정적 타입 지정 언어이다. 소스코드의 정확성과 성능을 보장하면서도 소스코드를 간결하게 유지할 수 있다
- 코틀린은 객체지향과 함수형 프로그래밍 스타일을 모두 지원한다. 코틀린에서는 일급 시민 함수를 사용해 수준 높은 추상화가 가능하고, 불변 값 지원을 통해 다중 스레드 애플리케이션 개발과 테스트를 더 쉽게 할 수 있다
- 코틀린은 서버 애플리케이션 개발에 잘 활용할 수 있다. 기존 자바 프레임워크를 완벽하게 지원하는 한편, HTML생성기나 영속화등의 일반적인 작업을 위해 새로운 도구를 제공한다
- 코틀린은 안드로이드에도 활용할 수 있다. 코틀린의 런타입 라이브러리는 크기가 작고, 코틀린 컴파일러는 안드로이드 API를 특별히 지원한다. 그리고 코틀린의 다양한 라이브러리는 안드로이드에서 흔히 하는 작업에 사용할 수 있으면서 코틀린과 잘 통합될 수 있는 함수를 제공한다
- 코틀린은 무료며 오픈소스다
- 코틀린은 실용적이고 안전하고, 간결하며 상호 운용성이 좋다. 코틀린을 설계하면서 일반적인 작업에 대해 흔히 발생하는 오류를 방지하며, 읽기 쉽고 간결한 코드를 지원하면서 자바와 아무런 제약없이 통합될 수 있는 언어틀 만드는데 초점을 맞췄다는 뜻이다

