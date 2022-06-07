# Object

### 객체 선언

- 코틀린은 싱글턴 패턴을 내장하고 있다. `object` 라는 키워드를 사용하여 싱글턴을 선언할 수 있다.
  - 싱글턴은 어떤 클래스에 인스턴스가 오직 하나만 존재하게 보장하는 패턴이다.

```kotlin
object Application {
    val name = "My Application"
    override fun toString() = name
    fun exit() {}
}
```

- 객체 선언은 클래스를 정의함과 동시에 인스턴스를 정의하는 것과 같다. 객체의 인스턴스는 일반적으로 단 하나 뿐이므로 인스턴스만 가리켜도 어떤 타입을 쓰는지 알 수 있다.
- 객체 정의는 `thread-safe` 하다. 컴파일러는 실행되는 여러 스레드에서 싱글턴에 접근 하더라도 오직 한 인스턴스만 공유되고 초기화 코드도 단 한 번만 실행되도록 보장한다.
- 초기화는 싱글턴 클래스가 실제 로딩되는 시점까지 지연된다. 보통은 프로그램이 객체 인스턴스에 처음 접근할 때 초기화가 이루어진다.
- 객체 선언도 멤버 함수와 프로퍼티를 포함할 수 있고 초기화 블록도 포함할 수 있지만, 주생성자나 부생성자는 없다. 객체 인스턴스는 항상 암시적으로 만들어지기 때문에 객체의 경우 생성자 호출이 아무 의미가 없다.
- 객체 본문에 들어있는 클래스에는 `inner` 를 붙일 수 없다. 내부 클래스의 인스턴스는 항상 바깥 쪽 클래스의 인스턴스와 연관되는데, 객체의 인스턴스는 하나이기 때문에 의미가 없기 때문이다.
- 객체의 맴버만 임포트해서 사용할 수도 있다.

### 동반 객체(companion object)

- 내포된 객체는 인스턴스가 생기면 자신을 둘러싼 클래스의 비공개 맴버에 접근할 수 있다.이런 특성은 팩토리 디자인 패턴을 쉽게 구현하는 경우 유용하게 활용할 수 있다.

```kotlin
class Application private constructor(val name: String) {
    object Factory {
        fun create(args: Array<String>): Application? {
            val name = args.firstOrNull() ?: return null
            return Application(name)
        }
    }
}

fun main(args: Array<String>) {
    val app = Applcation.Factory.create(args) ?: return

    println("Application started : ${app.name}")
}
```

- 위와 같은 경우 별도로 팩토리 메서드를 임포트하지 않는 한 매번 내포된 객체의 이름을 지정해야 한다. 코틀린에서는 동반 객체(companion object)로 정의함으로써 이러한 문제를 해결한다.
- 동반 객체는 `companion` 이라는 키워드를 덧붙인 내포된 객체이다. 이 객체는 다른 내포된 객체와 동일하게 동작하지만 동반 객체의 맴버에 접근할 때는 동반 객체가 들어있는 외부 클래스의 이름을 사용할 수 있다.

```kotlin
class Application private constructor(val name: String) {
    companion object {
        fun create(args: Array<String>): Application? {
            val name = args.firstOrNull() ?: return null
            return Application(name)
        }
    }
}

fun main(args: Array<String>) {
		// 바로 동반 객체의 맴버에 접근
    val app = Applcation.create(args) ?: return

    println("Application started : ${app.name}")
}
```

- 동반 객체의 이름을 생략할 경우 컴파일러에서 자동으로 `Companion` 이라는 이름을 붙여준다. 동반 객체의 맴버를 임포트할 때는 이 이름을 사용해 준다.

```kotlin
import Application.Companion.create
```

- 동반 객체 안에서도 `init` 블록을 사용한 초기화를 진행할 수 있다.

### 객체 식

- 명시적인 선언 없이 바로 객체를 생성할 수 있는 특별한 식이 있다. 객체 식은 자바 익명 클래스와 아주 비슷하다.

```kotlin
fun main(args: Array<String>) {
    fun midPoint(xRange: IntRange, yRange: IntRange) = object {
        val x = (xRange.first + xRange.last)/2
        val y = (yRange.first + yRange.last)/2
    }

    val midPoint = midPoint(1..5, 2..6)
    println("${midPoint.x}, ${midPoint.y}") // (3, 4)
}
```

- 클래스나 객체 식과 달리 객체를 함수 안에 정의할 수는 없다. 그 이유는 객체 선언이 싱글턴 표현이어야 하는데 반해 지역 객체들은 자신을 둘러싼 바깥 함수가 호출될 때마다 매번 다시 생성되어야 하기 때문이다.
- 객체식을 반환하는 함수의 반환 타입은 객체 식 안에 정의된 모든 멤버가 들어있는 클래스를 표현하는 익명 객체 타입이며 이 타입은 유일하다(똑같은 모양의 객체 식끼리도 타입이 다르다).
- 익명 객체 타입은 지역 선언이나 비공개 선언에만 전달될 수 있다. 만약 위 예제의 `midPoint` 함수를 최상위 함수로 정의하면 객체 멤버에 접근할 때 에러가 난다.

```kotlin
fun midPoint(xRange: IntRange, yRange: IntRange) = object {
	val x = (xRange.first + xRange.last)/2
	val y = (yRange.first + yRange.last)/2
}

fun main(args: Array<String>) {
    val midPoint = midPoint(1..5, 2..6)

    // error: unresolved reference: x
    // error: unresolved reference: y
    println("${midPoint.x}, ${midPoint.y}")
}
```

- `midPoint` 함수의 타입은 객체 식에 해당하는 익명 객체 타입이 아니라 객체 식에 지정된 상위 타입이 된다. 하지만 예제 객체 식에는 상위 타입을 명시하지 않았으므로 Any를 상위 타입으로 가정한다. 따라서 참조 에러가 난다.
- 객체 식도 자신을 둘러싼 코드 영역의 변수를 포획(capture)할 수 있다. 이렇게 포획한 가변 변수를 객체 본문에서 변경할 수 있다. 컴파일러는 지역 클래스와 비슷하게 데이터를 공유하기 위해 필요한 래퍼를 생성해 준다.

```kotlin
fun main() {
	var x = 1

	var o = object {
		fun change() {
			x = 2
		}
	}

	o.change()
	print(x) // 2
}
```

- 지연 초기화되는 객체 선언과 달리 객체 식이 만들어내는 객체는 객체 인스턴스가 생성된 직후 바로 초기화 된다.

```kotlin
  fun main {
  var x = 1

      val o = object {
      		val a = x++
      }

      println("o.a = ${o.a}") // o.a = 1
      println("x = $x") // x = 2

  }
```
