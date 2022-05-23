---
title: "[kotlin] Class, Object, Interface"
categories:
  - kotlin
tags:
  - kotlin
toc: true
---

> 해당 글은 kotlin in action 을 참고하여 작성하였습니다. 

# **class, interface, Object**
## **코틀린 interface**
* 자바와 동일하게 코틀린도 인터페이스 안에는 추상 메서드뿐 아니라, 구현이 있는 메서드도 정의할 수 있다. 
* 인터페이스에는 상태(field)가 들어갈 수 없다.

코틀린에서 interface를 만드는 방법은 다음과 같다. 
```kotlin
interface Clickable {
    fun click()
    fun showOff() = println("I'm clickable!")
}
```
`click()`이라는 추상 메서드와 `showOff()`라는 디폴트 메서드가 있는 interface이다. 이 `Clickable` interface를 구현한 구현체는 다음과 같이 `click()`에 대한 구현을 반드시 제공해야 한다. (`showOff()`는 재정의해도 되며, 재정의하지 않으면 디폴트로 적용된다.)
```kotlin
class Button : Clickable {
    override fun click() = println("I was clicked")
    override fun showOff() = super<Clickable>.showOff() // super.showOff()와 동일
}

fun main(args:Array<String>) {
    Button().click()    //  I was clicked
}
```

## **open, final, abstract 변경자**
* 상속은 기본적으로 취약한 기반 클래스라는 문제점을 갖는다. 
    * 상속을 하게 된다면 상위 클래스에 변경이 생겼을 때 하위 클래스는 그 영향을 받게 되는 것을 의미한다. 
* 따라서 코틀린의 클래스와 메서드는 기본적으로 `final`이다. 
* 어떤 클래스의 상속을 허용하려면 클래스 앞에 `open` 변경자를 붙여야 한다. 
* 오버라이드를 허용하고 싶은 메서드나 프로퍼티 앞에도 `open` 변경자를 붙여야 한다. 
* interface 의 멤버를 오버라이드하는 경우 그 메서드는 기본적으로 열려있다. 
* 오버라이드하는 메서드의 구현을 하위 클래스에서 오버라이드하지 못하게 하기위해선 오버라이드하는 메서드 앞에 final을 명시해야 한다. 
```kotlin
open class RichButton : Clickable {
    fun disable() {}
    open fun animate() {}
    final override fun click() {}
}
```

### abtstract
* 클래스를 abstract 로 선언할 수 있다. 
* abstract 로 선언한 추상 클래스는 인스턴스화 할 수 없다. 
* 추상 멤버는 항상 열려있다. (open을 표시할 필요 X)
```kotlin
abstract class Animated {
    abstract fun animate()
    open fun stopAnimating() {  // 비추상 함수는 기본적으로 final이지만 open으로 오버라이드를 허용
        
    }
    fun animateTwice() {
        
    }
}
```
> 💡 인터페이스의 멤버인 경우 final, open, abstract를 사용하지 않는다. 항상 open이다. final로도 변경되지 않는다. 

다음은 클래스 내에서 상속 제어 변경자의 의미를 표로 나타낸 것이다. 
|변경자|이 변경자가 붙은 멤버는|설명|
|--|--|--|
|`final`|오버라이드할 수 **없음**|클래스 멤버의 기본 변경자이다.|
|`open`|오버라이드할 수 **있음**|반드시 open을 명시해야 오버라이드할 수 있다.|
|`abstract`|반드시 오버라이드해야 함|추상 클래스의 멤버에만 이 변경자를 붙일 수 있다. 추상멤버에는 구현이 있으면 안된다.|
|`override`|상위 클래스나 상위 인스턴스의 멤버를 오버라이드 하는 중|오버라이드하는 멤버는 기본적으로 열려있다. 하위클래스의 오버라이드를 금지하려면 final을 명시해야한다. |

## **가시성 변경자 : 접근제어자(?)**
* 코틀린은 기본 가시성변경자가 `public`이며 `default` 접근자는 따로 존재하지 않는다.
* 모듈 내부에서만 사용할 수 있는 `internal` 접근자를 따로 제공한다.
* public, internal, private, protected의 가시성 변경자가 존재하며 protected를 제외하곤 최상위에 선언도 가능하다.

|변경자|클래스 멤버|최상위 선언|
|--|--|--|
|`public`(기본 가시성)|모든 곳에서 볼 수 있다.|모든 곳에서 볼 수 있다.|
|`internal`|같은 모듈 안에서만 볼 수 있다.|같은 모듈 안에서만 볼 수 있다.|
|`protected`|하위 클래스 안에서만 볼 수 있다.|최상위 선언에 적용할 수 없음|
|`private`|같은 클래스 안에서만 볼 수 있다.|같은 파일 안에서만 볼 수 있다.|

```kotlin
interface Focusable {
  fun showOff() = println("I`m Focusable showOff")
}

internal open class TalkativeButton : Focusable {
  private fun yell() = println("yell")
  protected fun whisper() = println("whisper")
}

// 확장하려는 클래스가 internal 이므로 가시성이 수준이 같거나 더 낮아야 한다.
internal fun TalkativeButton.giveSpeech() {
//    yell()  private이므로 호출 불가
//    whisper()  자바와 다르게 protected는 오직 하위 클래스에서만 사용 가능 
}
```
### 코틀린과 자바의 가시성 변경자
* public, protected, private은 자바의 바이트코드 안에서 그대로 유지
* 하지만 private class같이 자바에서 구현이 불가능한 것들은 private 클래스를 패키지-전용 클래스로 컴파일한다.
* internal 변경자도 자바에서 지원되지 않는 변경자이므로 바이트코드상으론 public이 된다.
    * 따라서 사실상, 자바에서 접근이 가능하지만 internal 멤버의 이름을 의도적으로 바꾸어 외부에서 사용을 하기 어렵게 컴파일한다.

## **내부 클래스와 중첩된 클래스**
```kotlin
class Button:View {
    override fun getCurrentState() : State = ButtonState()
    override fun restoreState(state: State) {/*..*/}
    
    class ButtonState : State {/*..*/}  // 중첩클래스
}
```
* 자바에서는 내부 중첩클래스는 기본적으로 바깥쪽 클래스를 참조한다. 참조를 제거하기 위해선 static 클래스로 선언해야 한다.
* 코틀린에서는 중첩 클래스에 아무런 변경자가 붙지 않으면 static 중첩클래스이다. 즉, 바깥쪽 클래스에 대한 참조를 저장하지 않는다. 
* 코틀린에서 중첩 클래스를 내부 클래스로 변경해서 바깥쪽 클래스에 대한 참조를 포하하게 만들고 싶다면 inner 변경자를 붙여야 한다. 
```kotlin
class Outer {
    class Inner1 {
        // 코틀린은 기본이 자바의 static 클래스처럼 외부의 참조가 없는 중첩 클래스이다.
        // fun test() = this@Outer 외부 참조가 없으니 불가능 
    }
    
    // 내부 클래스를 위해 inner를 명시적으로 붙여줘야 한다.
    inner class Inner2 {
        fun test() = this@Outer
    }
}
```

## **sealed class**
* sealed class는 상위 클래스를 상속한 하위 클래스 정의를 제한할 수 있다. (봉인 클래스)
```kotlin
sealed class Expr {
    class Num(val value : Int) : Expr()
    class Sum(val left: Expr, val right: Expr) : Expr() 
    
}

fun eval(e: Expr): Int =
    when (e) {
        is Expr.Num -> e.value
        is Expr.Sum -> eval(e.right) + eval(e.left)
    }
```
* 봉인 클래스를 활용하면 when 절에서 else를 사용하지 않아도 된다. 
* 새로운 중첩 클래스가 생겼을 떄 해당 클래스에 대해 when 절을 구현하지 않으면 컴파일 에러가 발생한다. 

## **코틀린의 생성자**
* 코틀린의 생성자는 주 생성자와 부 생성자로 구분할 수 있다. 
    * 주 생성자는 클래스 본문 밖에서 정의 / 클래스를 초기화할 때 주로 사용하는 간략한 생성자
    * 부 생성자는 클래스 본문안에서 정의
* **초기화 블록**을 통해 초기화 로직을 추가할 수 있다. 
```kotlin
// 주 생성자
open class User*val nickname: String) {...}

// 주 생성자를 풀어쓰면 다음과 같다.
class User constructor(_name: String) {
    val name: String
    init {
        name = _name
    }
}

// 부모의 생성자 호출은 아래와 같이 가능하며 괄호를 붙여 부모 클래스 생성자를 호출해줘야 한다.
class SubUser(name: String) : User(name)
```
> 💡 코틀린은 모든 생성자 프로퍼티에 디폴트값을 부여하면 컴파일러가 자동으로 디폴트 생성자를 만들어준다. 
> * 인터페이스는 생성자가 없기 때문에 인터페이스를 구현하는 경우 뒤에 괄호가 없다. 
> * 코틀린은 디폴트 파라미터가 있기 때문에 대부분의 부 생성자 오버로딩이 필요없다. 

만약 생성자를 외부에서 만들지 못하게 하고싶다면 `private` 키워드를 붙이면 된다. 
```kotlin
class Secretive private constructor() {}    // 주 생성자가 비공개
```
위 Secretive 는 외부에서 인스턴스화 할 수 없다. 
* 부 생성자는 constructor 키워드로 시작한다. 
```kotlin
class MyButton : View {
    // 부 생성자
    constructor(ctx: Context)
        : super(ctx) {
            // ...
        }

    // 부 생성자
    constructor(ctx: Context, attr: AttributeSet)
        : super(ctx, attr) {
            // ...
        }
}
```
* 위의 두 부 생성자는 super()를 통해 자신에 대응하는 상위 클래스 생성자를 호출한다. 
* 클래스에 주 생성자가 없다면 부 생성자는 반드시 상위 클래스를 초기화하거나 다른 생성자에게 생성을 위임해야 한다. 

## **인터페이스에 선언된 프로퍼티**
* 코틀린은 인터페이스에 추상 프로퍼티를 선언할 수 있다. 
    * 상태값을 가질 수 없으니 선언만 할 수 있다.
* 추상 프로퍼티가 선언된 인터페이스를 구현하는 구현체는 추상 프로퍼티의 상태값을 저장할 수 있게끔 구현해야한다. 
```kotlin
// 추상 프로퍼티를 포함한 인터페이스
interface User {
    val nickname: String
}

// 주 생성자에 있는 프로퍼티
class PrivateUser(override val nickname: String): User 

// 커스텀 게터
class SubscribingUser(val email:String) :User {
    override val nickname: String
        get() = email.substringBefore('@')
}

fun main(args: Array<String>) {
    println(PrivateUser("test@kotlinlang.org").nickname) // test@kotlinlang.org
    println(SubscribingUser("test@kotlinlang.org").nickname)    // test
}
```

## **커스텀 접근자의 Backing field**
* 프로퍼티는 값을 직접 저장하는 프로퍼티와 커스텀 접근자를 활용해 매번 새로운 값을 계산하는 프로퍼티가 존재한다.
* 이 두가지 방법을 조합하여 값이 변경할 때 이전 값과 현재 저장할 값을 이용하여 원하는 로직을 수행하도록 할 수 있다.
* 이를 위해 접근자 안에서 해당 프로퍼티의 Backing field에 접근할 수 있어야 한다.
```kotlin
class AUser(val name: String) {
  var address: String = "unselected"
    set(value: String) {
      // backing field는 `field`로 접근가능 (get에서는 field를 참조할 수 있지만 읽기만 가능하다.)
      print("백킹 필드값(이전값): $field, 새로운 값: $value")
      field = value
    }
}

fun main() {
  val aUser = AUser("name")
  aUser.address = "서울시" // setter 호출
  aUser.address = "부산시" // setter 호출
//    ## 출력 ##
//    백킹 필드값(이전값): unselected, 새로운 값: 서울시
//    백킹 필드값(이전값): 서울시, 새로운 값: 부산시
}
```

### **클라이언트 입장에서의 Backing Field**
* 해당 프로퍼티를 사용하는 클라이언트 입장에서는 Backing Field에 대해 알 필요가 없다.
* 디폴트 접근자로 구현을 하더라도 코틀린 내부적으로 Backing Field를 생성해주기 때문이다.
* 단, 직접 커스텀 접근자를 구현하였는데 거기서 Backing Field를 사용하지 않으면 Backing Field는 존재하지 않게 된다.

## **data class**
* JVM언어에서는 hash 컬렉션의 사용 방식으로 인해 equals, hashCode를 반드시 동시에 알맞게 구현해줘야 하는 규칙이 있다.
* 최적화를 위해 hashCode로 비교 후 equals로 한번 더 비교하기 때문이다.
* 이런 보일러 플레이트 같은 메서드들을 자동 구현해주는 data class가 존재한다. -> `data class User(val name: String)`


## **클래스 위임 : by**
* 상속을 하지않고 클래스에 새로운 동작을 추가해야할 때 사용하는 **데코레이터 패턴**이 있따. 
* 데코레이터 패턴을 위해선 동일한 인터페이스를 구현해야하고, 관련되지 않은 모든 동작도 위임해주어야 한다. 
* 코틀린은 언어적으로 이런 위임을 간편히 할 수 있도록 제공해준다.
```kotlin
// 직접 구현하지 않으면 innerList로 전부 위임하도록 해준다.
class DelegatingCollection<T>(innerList: Collection<T> = ArrayList<T>()) : Collection<T> by innerList {
    override fun isEmpty(): Boolean {
        TODO("Not yet implemented")
    }
```

## **onject 키워드: 클래스 선언과 인스턴스 생성**
* 코틀린에서 onject 키워드는 클래스 선언과 동시에 인스턴스(객체)를 생성한다.
* 주로 싱글턴을 정의할 때, 동반 객체, 익명 내부 클래스에서 사용된다. 
```kotlin
// object 키워드를 사용해 싱글턴으로 만든다.
object Payroll {
    val allEmployees = arrayListOf<Person>()

    fun calculateSalary() {
        for (person in allEmployees) {...}
    }
}

// 중첩 객체를 사용해 Comparator 구현하기
data class Person(val name:String) {
    object NameComparator : Comparator<Person> {
        override fun compare(p1: Person, p2: Person): Int = 
            p1.name.compareTo(p2.name)
    }
}
```

## **동반객체: 팩터리 메서드와 정적 멤버가 들어갈 장소**
* 코틀린 언어는 자바의 static 키워드를 지원하지 않는다. (최상위 함수와 객체 선언이 대신함)
* 하지만 상황에 따라 클래스 내부에 private 접근자로 접근하기 위해 클래스 내부에 구현되어야 할 필요가 있다.
* 이런경우 동반 객체를 활용하여 private 접근자에 접근이 가능하도록 할 수 있다.
```kotlin
class BUser private constructor(val name: String) {
    companion object {
        // private 생성자로 접근가능, 사용처에선 static 메서드처럼 호출 가능
        fun newUser(name: String) = BUser(name)
    }
}

// 동반객체도 인터페이스를 구현하여 다형성을 활용할 수 있다.
interface JSONFactory<T> {
    fun fromJSON(jsonText: String): T
}

class CUser(val name: String) {
    // 동반 객체도 인터페이스를 구현할 수 있다.
    companion object : JSONFactory<CUser> {
        override fun fromJSON(jsonText: String): CUser {
            TODO("Not yet implemented")
        }
    }
}

fun <T> loadFromJSON(factory: JSONFactory<T>): T {
    TODO()
}

fun main() {
    // 동반 객체가 구현한 인터페이스를 파라미터로 가지는 메서드에 CUser를 넘겨 다형성 활용 가능
    loadFromJSON(CUser)
}
```


참고한 사이트
* https://pompitzz.github.io/blog/Kotlin/kotlinInAction.html#%E1%84%8B%E1%85%B5%E1%86%AB%E1%84%90%E1%85%A5%E1%84%91%E1%85%A6%E1%84%8B%E1%85%B5%E1%84%89%E1%85%B3-%E1%84%91%E1%85%B3%E1%84%85%E1%85%A9%E1%84%91%E1%85%A5%E1%84%90%E1%85%B5%E1%84%8B%E1%85%AA-backing-field