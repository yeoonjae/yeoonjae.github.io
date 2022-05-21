> 해당 글은 kotlin in action 을 참고하여 작성하였습니다. 

# **Collection**
* 코틀린은 자체 컬렉션 기능을 제공하지 않는다. 
    * why? 표준 자바 컬렉션을 활용하면 자바 코드와 상호작용하기 더 쉽다.
    * 따라서 자바 <-> 코틀린 컬렉션 함수 호출 시 서로 변환할 필요가 없다. 
* 즉, `코틀린 컬렉션 = 자바 컬렉션` 이다. 

코틀린에서 컬렉션을 만드는 방법은 다음과 같다. 
```kotlin
fun main(args: Array<String>) {
    val set = hashSetOf(1, 7, 10)
    val list = arrayListOf(1, 6, 23)
    val map = hashMapOf(1 to "one", 7 to "seven", 53 to "fifty-three")

    println(set.javaClass)  // javaClass는 자바의 getClass()와 동일하다.
    println(list.javaClass)
    println(map.javaClass)
}
```
결과

    class java.util.HashSet
    class java.util.ArrayList
    class java.util.HashMap

* 코틀린에서 Collection을 만드는 방법은 위의 예제처럼 `hashSetOf`,`arrayListOf`, `hashMapOf(key to value)` 를 사용하면 된다.. 
    * `hashMapOf(key to value)`의 `to`는 일반 함수이다.. 
* `javaClass`는 자바의 `getClass()`에 해당하는 코드다. 
* 코틀린의 Collection은 자바의 Collection 보다 더 많은 기능을 제공한다. 예제로 살펴보자
```kotlin
    val strings = listOf("first", "second", "fourteenth", "four", "nineteenth", "five")
    val numbers = setOf(1, 24, 300, 59, 34)
    val setNumbers = listOf(1, 2, 3)


    println(strings.last()) // Collection의 마지막 원소
    println(numbers.maxOrNull())    // Collection의 최댓값
    println("setNumbers: $setNumbers, min = ${setNumbers.minOrNull()} max = ${setNumbers.maxOrNull()}") // setNumbers: [1, 2, 3], min = 1 max = 3
```
위의 예제처럼 코틀린은 편의를 위해 `last()`, `maxOrNull()` 과 같은 확장 함수를 가지고 있다. 

## **확장함수 만들기**
* 자바 Collection은 `toString()` 함수가 디폴트로 구현되어 있다. 
* 코틀린에서 원하는 형식으로 출력하는 함수를 만들고 싶을 땐 재정의하거나, 함수를 구현하면 된다. 
* 다음은 시작과 끝, 구분자를 원하는 형태로 바꿔서 출력할 수 있는 함수 `joinToString()` 을 만든 예제이다. 
```kotlin
fun <T> joinToString(
        collection: Collection<T>,
        separator: String,
        prefix: String,
        postfix: String,
):String {
    val result = StringBuilder(prefix)
    for ((index, element) in collection.withIndex()) {
        if(index > 0) result.append(separator)
        result.append(element)
    }

    result.append(postfix)
    return result.toString()
}

fun main(args: Array<String>) {
    val strings = listOf("first", "second", "fourteenth", "four", "nineteenth", "five")

    println(joinToString(strings,";","(",")"))
}
```
결과

     (first;second;fourteenth;four;nineteenth;five)
위의 결과처럼 의도한대로 만들 수 있다. 위 코드는 코틀린이 지원하는 여러 기능을 사용하지 않고 만든 코드이다. 이제 코틀린이 지원하는 여러 기능을 사용해보자.

## **이름붙인 인자**
함수를 사용할 때 `joinToString(strings,";","(",")")` 다음과 같이 선언했다. 
몇번째 파라미터가 무엇을 의미하는지 정확하게 파악하기 힘들다. 즉, 가독성이 떨어진다. 

자바에서는 파라미터의 의미를 정확하게 하고, 다른 개발자의 가독성을 높여주기 위해 다음과 같이 작성한다. 
```java
joinToString(collection, /* separator */ "", /* prefix */ "", /*postfix */ ".");
```

하지만, 코틀린에서는 명시적으로 파라미터 이름을 파라미터에 같이 넣어줄 수 있다. 
```kotlin
joinToString(collection, separator = "", prefix = "", postfix = ".")
```
이처럼 파라미터에 이름을 붙여주었다면 어느 순서로 적어도 상관없다. 
만약 여러개의 파라미터 중 하나라도 이름을 명시했다면, 나머지 파라미터도 이름을 명시해주어야 한다. 

## **디폴트 파라미터 값**
* 자바에서의 하나의 이름에 다양한 파라미터를 전달받기 위해 오버로딩을 사용한다. 
* 이는 결국 중복의 문제가 존재하며, 복잡도도 증가한다. 
* 코틀린에서는 이런 단점을 극복하기 위해 함수 선언시 파라미터에 디폴트값을 넣어줄 수 있다. 즉, 파라미터가 들어오지 않는다면 디폴트값을 사용할 수 있다. 

`joinToString()`함수를 디폴트 값을 사용하여 개선해보자.
```kotlin
fun <T> joinToString_V2(
        collection: Collection<T>,
        separator: String = ", ",
        prefix: String = "",
        postfix: String = "",
):String {
    ...
}
```
이렇게 디폴트값을 정해놓으면 아무것도 입력하지 않았을 때 디폴트값이 적용된다. 

> 💡 디폴트값과 자바<br>
> 자바에서는 디폴트값이라는 개념이 없어서 디폴트값이 적용된 코틀린 함수를 자바에서 호출하는 경우도 모든 인자를 명시해야 한다. 만약, 자바에서도 동일하게 사용하고싶다면 함수에 `@JvmOverloads` 어노테이션을 붙여주면 complier가 자동으로 자바를 위한 메서드를 추가해준다. 

## **최상위 함수와 프로퍼티**
* 자바는 모든 함수가 class 내에 존재한다. 
* 하지만, 코틀린에서는 클래스 외부에 선언함으로써 어디서든 접근할 수 있는 함수를 만들 수 있다. 
* 즉, 여러곳에서 호출해서 쓰려고 만드느 자바의 util 클래스가 사라질수 있다.

```kotlin
// 파일명: Join.kt 
package strings 
fun joinToString(...): String {...}
```
위의 코드와 같이 생성한다면, 다른곳에서 해당 package만 import한다면 `joinToString()`을 호출할 수 있다. 위 코드를 컴파일하면 컴파일러가 클래스를 만들고 그 안에 함수를 넣는다. 

위 코드를 자바코드로 쓰면 다음과 같다.
```java
package strings; 

public class JoinKt { 
    public static String joinToString(...) {...} 
}

```
최상위 함수는 정적인 메서드로 만들어진다. 

자바에서 위 함수를 호출하기 위해선 다음과 같이 호출하면 된다. 
```java
import strings.JointKt;
 ... 
 JoinKt.joinToStirng(list,",","","");

```

> 💡 파일명이 아닌 다른 이름으로 클래스명을 지정하고싶다면 ?
> ```java
> @file:JvmName("StringFunction") 
> package strings 
> fun joinToString(...); String> {...}
> ```
> 위처럼 `@file:JvmName(이름)`을 작성해주면 된다. 

### 최상위 프로퍼티
* 함수와 마찬가지로 프로퍼티도 최상위에 위치시킬 수 있다. 

```kotlin
var totalCount = 0
const val CONNECTION_TIME_OUT = 1000 

fun addOperation() { 

    totalCount++; 
}

```
* `CONNECTION_TIME_OUT`는 `const`라는 키워드에 의해 상수값이 된다. 
    * const == public static final
* `const`를 붙이지 않는다면 getter를 갖는 프로퍼티가 된다.


---
참고한 사이트

https://tourspace.tistory.com/103?category=797357