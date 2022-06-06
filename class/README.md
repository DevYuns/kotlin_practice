# Class

### 클래스 기본 구조

- 기본적으로 `new` 키워드는 생략한다.

```kotlin
class Person {
	var firstName: String = ""
	var familyName: String = ""
	var age: Int = 0
  
	fun fullName() = "$firstName $FamilyName"

	fun showMe() {
		println("${fullName()}: $age")
	}
}
```

- 프로퍼티는 어떤 클래스의 구체적인 인스턴스와 엮여 있기 때문에 이 인스턴스를 식으로 지정해야 한다. 이런 인스턴스를 수신 객체(receiver)라고 부르고, 수신 객체는 프로퍼티에 접근해야할 때 사용해야 하는 객체를 지정한다.
- 맴버 함수의 경우에도 똑같이 수신 객체가 있고 이런 경우 멤버 함수를 메서드라고 부른다.
- 클래스 맴버 내부에서 this를 통해 수신 객체를 참고할 수 있지만, 생략도 가능하다. 다만 클래스의 프로퍼티와 메서드 파라미터의 이름이 같은 경우에는 프로퍼티 앞에 this를 붙여서 구분해주어야 한다.
- 생성자 호출을 통해 인스턴스를 생성하면 새 인스턴스를 힙 메모리에 할당한 다음, 인스턴스의 상태를 초기화해주는 생성자 코드를 호출해준다.
- 기본적인 코틀랜 클래스는 공개(public) 가시성을 가진다.

### 생성자

- 생성자는 클래스 인스턴스를 초기화해주고 인스턴스 생성 시 호출되는 특별한 함수이다.

```kotlin
class Person(firstName: String, familyName: String) {
	val fullName = "$firstName $familyName"

	init {
		println("Created new Person instance: $fullName")
	}
}
```

- 클래스 헤더의 파라미터 목록을 주생성자(primary constructor) 선언이라고 한다. 주 생성자는 클래스 정의 내에서 프로퍼티 초기화와 초기화 블록이 등장하는 순서대로 구성된다. 초기화 블록이란 `init` 이라는 키워드가 앞에 붙은 블록이다. 초기화 블록에서는 `return` 문이 들어갈 수 없다.
- 한 클래스 안에 여러 init 블록이 들어갈 수 있다. 이런 경우 각 블록은 프로퍼티 초기화와 함께 순서대로 실행된다.
- 주생성자 파라미터를 프로퍼티 초기화나 init 블록 밖에서 사용할 수 없다. 그런데 하나의 식으로 표현하기 여러운 복잡한 초기화 로직을 실행해야 프로퍼티를 초기화할 수 있는 경우도 있다. 이에 대한 해법은 생성자 파라미터의 값을 맴버 프로퍼티로 정의하는 것이다.
- 생성자 파라미터 앞에 val 이나 var 키워드를 붙이면 자동으로 해당 생성자 파라미터로 초기화되는 프로퍼티를 생성한다. 이때 파라미터 이름을 프로퍼티 초기화나 init 블록 안에서 참조하면 생성자 파라미터를 가리키고, 다른 위치에서 참조하면 프로퍼티를 가리키게 된다.

```kotlin
class Person(val firstName: String, familyName: String) {
	val fullName = "$firstName $familyName" // firstName은 생성자 프로퍼티를 가리킴

	init {
		println("Created new Person instance: $fullName")
	}
	
	fun printFirstName () {
		println("firstName") // 맴버 프로퍼티를 가리킴
	}
}

// 프로퍼티 초기화 방식으로 본문이 빈 클래스를 생성할 수도 있다.
class Person(val firstName: String, val familyName: String = "")
```

### 부생성자

- 여러 생성자를 사용해 클래스 인스턴스를 서로 다른 방법으로 초기화하고 싶을 때 부생성자를 사용할 수 있다.
- `constructor` 키워드를 사용한다.
- 부생성자에 반환 타입을 지정할 수 없지만 기본적으로 `Unit` 타입 값을 반환하는 함수와 마찬가지 형태이다. 부생성자 안에서는 return 문을 사용할 수 있다.
- 클래스에서 **주생성자가 없다면** 모든 부생성자는 자신의 본문을 실행하기 전에 프로퍼티 초기화와 init 블록을 실행한다.
- 부생성자가 생성자 위임 호출을 사용해 다른 부생성자를 호출하는 것이 있다.

```kotlin
class Person {
	val fullName: String
	constructor(fisrtName: String, familyName: String):
		this("$firstName $familyName")
	constructor(fullName: String) {
		this.fullName = fullName
	}
}
```

- 생성자 파라미터 목록 뒤에 콜론을 넣고 그 뒤에 일반 함수를 호출하는 것처럼 코드를 작성하되 함수 이름 대신this를 사용하면 생성자 위임 호출이 된다.
- 클래스에 **주생성자가 있다면** (부생성자가 있는 경우) 모든 부생성자는 주생성자에게 위임을 하거나 다른 부생성자에게 위임을 해야 한다.
- 부생성자의 파라미터 목록에서는 val/var 키워드를 쓸 수 없다.

```kotlin
// 부 생성자가 this를 통해 주 생성자를 호출하고 있다.
class Person (val fullName: String) {
	val fullName: String
	constructor(fisrtName: String, familyName: String):
		this("$firstName $familyName")
}
```

### 맴버 가시성

- public : 맴버를 어디서나 볼 수 있다. 기본 가시성 설정이다.
    - 코틀린은 기본 가시성이 모듈 단위인 반면 자바는 패키지 단위이다.
- internal : 맴버를 맴버가 속한 클래스가 포함된 컴파일 모듈 내부에서만 볼 수 있다.
- protected : 맴버를 맴버가 속한 클래스와 맴버가 속한 클래스의 모든 하위 클래스 안에서 볼 수 있다.
- private : 맴버를 맴버가 속한 클래스 내부에서만 볼 수 있다.

```kotlin
class Person (private val fisrtName: String, private val familyName: String) {
	fun fullName() = "$firstName $familyName"
}
```

- 함수와 프로퍼티, 주생성자 부생성자에 대해 가시성 변경자를 지원한다. 주생성자의 가시성을 지정하려면 `constructor` 키워드를 반드시 명시해야 한다. `companion object` 에서 팩토리 메서드를 제공할 때 함게 사용할 수 있다.

```kotlin
class Empty private constructor() {
	fun showMe() = println("Empty")
}

fun main () {
	// Error : cannot access <init> : it is private in Empty
	Empty().showMe()
}
```

### 내포된 클래스(nested class)

- 코틀린 클래스는 다른 클래스도 맴버로 가질 수 있다.
    
    ```kotlin
    class Person(val id: Id, val age: Int) {
    	class Id(val firstName: String, val familyName: String)
    	fun showMe() = println("${id.firstName} ${id.familyName}, $age")
    }
    
    fun main() {
    	val id = Person.Id("John", "Doe")
    	val person = Person(id, 25)
    	person.showMe()
    }
    ```
    
- 내포된 클래스를 둘러싸고 있는 클래스의 본문 밖에서는 [Person.Id](http://Person.Id) 처럼 내포된 클래스 이름 앞에 바깥쪽 클래스의 이름을 덧붙여야만 내포된 클래스를 참조할 수 있다.
- 내포된 클래스에서도 여러 가지 가시성을 지정할 수 있으며, **자바와 달리** 바깥 쪽 클래스는 자신에게 내포된 클래스의 비공개 맴버에 접근할 수 없다. 반면 내포된 클래스도 바깥 쪽 클래스의 맴버이므로 바깥쪽 클래스의 비공개 선언에 접근할 수 있다.
- 내포된 클래스에 `inner` 를 붙이면 자신을 둘러싼 외부 클래스의 현재 인스턴스에 접근할 수 있다.

```kotlin
class Person (val fisrtName: String, val familyName: String) {
	inner class Possesion(val description: String) {
		fun showOwner() = println(fullName())
	}
	// this 생략 가능
	val myWallet = Possession("Wallet")
	private fun fullName() = "$firstName $familyName"
}

fun main() {
	val person = Person("John", "Doe")
	// Possession 생성자 호출
	val wallet = person.Possession("Wallet")

	wallet.showOwner() // John Doe
}
```

- 내부 클래스 생성자를 호출할 때 반드시 **외부 클래스의 인스턴스를 지정**해야 한다.
- 클래스의 내부 클래스를 가리킬 때도 this를 생략할 수 있다. 일반적으로 this는 항상 가장 내부의 클래스 인스턴스를 가리킨다. 따라서 내부 클래스 본문에서 this는 내부 클래스 자신을 가리킨다. 내부 클래스 본문에서 외부 클래스 인스턴스를 가리켜야 한다면 한정시킨(qualified) this 식을 사용해야 한다.

```kotlin
class Person (val firstName: String, val familyName: String) {
    inner class Possession(val description: String) {
        fun getOwner() = this@Person
    }
}
```

- 내부 클래스가 외부 클래스의 인스턴스와 연관되길 원하지 않는다면 `inner` 를 붙이지 않으면 내포된 클래스로서 외부 클래스의 인스턴스와 연관되지 않는다.

### 지역 클래스

- 함수 본문에서 클래스를 정의할 수 있다. 지역 클래스는 자신을 감싼 코드 블록 안에서만 쓰일 수 있다.
- 지역 함수와 비슷하게 지역 클래스도 자신을 둘러싼 코드의 선언에 접근할 수 있다. 특히 지역 클래스는 클래스 본문 안에서 **자신이 접근할 수 있는 값을 capture 할 수 있고, 변경할 수 있다.**

```kotlin
fun main() {
    var x = 1
    class Counter {
        fun increment() {
            x++
        }
    }
    Counter().increment()
    println(x) // 2
}
```

- 위와 같은 경우 익명 객체와 이 객체를 둘러싸고 있는 코드 사이에 변수를 공유하기 위해 코틀린 컴파일러는 공유되는 값을 특별한 래퍼 객체로 감싼다. 반면 불변 변수를 값이 바뀌지 않기 때문에 래퍼 객체가 필요 없다.
- 지역 클래스는 가시성 변경자를 붙일 수 없다. 또한 함수, 프로퍼티, 생성자, 내포된 클래스를 가질 수 있으며, 이때 내포된 클래스는 반드시 `inner` 키워드가 붙어야 한다.
- 지역 클래스 안에 내포된 클래스를 허용하지 않는 이유는 지역 클래스는 자신을 둘러싼 지역적인 상태에 접근할 수 있다. 하지만 내포된 클래스의 특성상 지역 클래스 안에 있는 내포된 클래스는 자신의 외부 클래스에서 사용하 수 있는 상태에 접근할 수 없는데, 이는 구문 영역(lexical scope)에 따른 변수 가시성 규칙에 반하는 것처럼 보여서 혼돈하기 쉽기 때문에 금지한다.

### 최상위 프로퍼티

- 클래스나 함수와 마찬가지로 최상위 수준에 프로퍼티를 정의할 수도 있다. 이런 경우 프로퍼티는 전역 변수나 상수와 비슷한 역할을 한다.
- 이런 프로퍼티에 최상위 가시성을 지정할 수 있으며, 임포트를 통해 가져올 수 있다.

### 지연 초기화

- 어떤 프로퍼티는 클래스 인스턴스가 생성된 뒤에, 그러나 해당 프로퍼티가 사용되는 시점보다는 이전에 초기화돼야 할 수 있다. 예를 들어 의존 관계 주입에 의해 대입되어야 하는 프로퍼티의 경우가 있다.
- `lateinit` 키워드를 사용하면 지연 초기화가 가능하며, 값을 읽으려고 시도할 때 프로그램이 프로퍼티가 초기화 됐는지 검사해서 그렇지 않을 경우 에러를 던진다.

```kotlin
import  java.io.File

class Content {
    lateinit var text: String
    
    fun loadFile(file: File) {
        text = file.readText()
    }
}

fun getContentSize(content: Content) = content.text.length
```

- 프로퍼티를 `lateinit` 으로 만들기 위한 조건
    - 프로퍼티를 가변(var)으로 정의해야 한다
    - 널이 아닌 타입이어야 하고 Int나 Boolean 같은 원시 값을 표현하는 타입이 아니어야 한다. 내부적으로는 초기화되지 않는 상태를 표현하기 위해 null을 사용하는 nullable한 값으로 표현되기 때문이다.
    - lateinit 프로퍼티를 정의하면서 초기화 식을 지정해 값을 대입할 수 없다.
- 코틀린 1.2 부터 최상위 프로퍼티에서도 lateinit을 사용할 수 있다.

### 커스텀 접근자

- 변수와 함수의 동작을 한 선언 안에 조합할 수 있다.커스텀 접근자는 프로퍼티 값을 읽거나 쓸 때 호출되는 틀별한 함수다.

```kotlin
// 커스텀 게터
// 게터에는 파라미터가 없으며 반환 타입은 프로퍼티와 같은 타입이어야 한다
class Person(val firstName: String, val familyName: String) {
    val fullName: String
        get() = "$firstName $familyName"
}

fun main () {
    val person = Person("John", "Doe")
		// 자동으로 게터 호출
    println(person.fullName)
}
```

- 위 처럼 도입한 프로퍼티의 값은 매번 fullName 프로퍼티를 읽을 때마다 다시 계산된다.
- fullName에는 뒷받침 필드(backing field)가 없기 때문에 클래스 인스턴스에서 전혀 메모리를 차지하지 않는다.
    - 프로퍼티에 명시적으로 field를 사용하는 디폴트 접근자나 커스텀 접근자가 하나라도 있으면 뒷받침을 하는 필드가 생성된다.
    - 뒷받침 하는 필드 참조는 `field` 라는 키워드를 사용하며 접근자의 본문 안에서만 유용하다.

```kotlin
class Person(val firstName: String, val familyName: String, age: Int) {
    val age: Int = age
        get(): Int {
            println("Accessing age")
            return field
        }
}
```

- var로 정의하는 가변 프로퍼티에는 게터와 세터가 존재한다.

```kotlin
class Person(val firstName: String, val familyName: String) {
    var age: Int? = null
        set(value) {
            if(value != null && value <= 0) {
                throw IllegalArgumentException("Invalid age: $value")
            }
            field = value
        }
		var lastChaged: Date? = null
			private set // Person 클래스 밖에서 변경할 수 없다.
}

fun main () {
    val person = Person("John", "Doe")
    person.age = 20 // 세터 호출
    println(person.age) // 20 (게터 호출)
}
```

### 지연 계산 프로퍼티와 위임

- 프로퍼티를 처음 읽을 때까지 그 값에 대한 계산을 미뤄두고 싶을 때가 있다. 이때 `lazy` 키워드를 쓴다.
- 아래 예제에서 main 함수 내에서 text 값을 읽기 전까지 값을 계산하지 않는다.초기화된 이후 프로퍼티의 값은 필드에 저장되고, 그 이후로는 프로퍼티 값을 읽을 때마다 저장된 값을 읽게 된다.

```kotlin
import java.io.File

val text by lazy { 
    File("date.txt").readText()
}

fun  main() {
    while(true) {
        when (val command = readLine() ?: return) {
            "print date" -> println(text)
            "exit" -> return
        }
    }
}
```