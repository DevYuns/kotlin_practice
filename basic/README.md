# Basic

### Variable declaration keyword

```kotlin
val str = 'hello world!'; // Immutable
var str = 'hello world!'; // Mutable
```

### Identifier type

- 문자 숫자 혹은 언더 바(_)
- 숫자로 시작할 수 없다.
- $ 기호를 쓸 수 없다.

```kotlin
// 코틀린에 없는 자바 키워드를 사용하기 위한 목적의 용례
val `fun` = 1
val `name with space` = 2
```

### Operators

- !! : not-null assertion
    - val a = readLine()!!.toInt()

### Data Type

- 코틀린 내에서는 똑같은 타입(ex. Int)이 문맥에 따라 원시 타입과 참조 타입을 가리키게 된다.
    - 윈시 타입의 값은 메서드의 스택 영역에 저장 반면 클래스 기반의 참조 타입은 동적으로 힙에 할당된 메모리를 가리키는 참조일 뿐이다.
    - 코틀린에서는 문맥에 따라 필요한 경우 원시 타입을 암시적으로 박싱(boxing type)을 수행한다.
- 모든 코틀린 타입은 근본적으로 클래스 정의를 기반으로 만들어진다. Int와 같은 원시 타입도 메서드와 프로퍼티를 제공한다. ex. `1.5.toInt()`

### 정수 타입

- Byte : 1byte / -128..128
- Short: 2byte / -32768..32767
- Int: 4byte
- Long: 8byte
    - 숫자 리터럴은 `_` 를 구분자로 사용할 수 있다. `1_000_000`
    - 2진수(0b) 및 16진수(0x) 리터럴도 가능 : `0b10101` / `0xF9`
- 각 정수 타입에는 최솟값과 최댓값을 포함하는 상수 정의가 들어 있다.
    - Short.MIN_VALUE
    - Short.MAX_VALUE

### 부동 소수점 타입

- 부동 소수점 리터럴의 기본은 `Double` 타입이다. 필요한 경우 f 나 F를 붙여서 명시적으로 Float 타입을 만들 수 있다.
- Float
- Double
- Float와 Double도 각 타입의 특별한 값을 표현하는 상수를 제공
    - MIN_VALUE, MAX_VALUE, NEGATIVE_INFINITY, POSITIBE_INFINITY, NaN

### 비트 연산

- shl : 왼쪽 시프트 - 13 shl 2
- shr : 오른쪽 시프트 - 13 shr 2
- ushr : 부호 없는 오른쪽 시프트
- and: 비트 곱
- or : 비트 합
- xor : 비트 베타합
- inv : 비트 반전 - 13.inv()
- Int, Long, Byte, Short에서 or 및 xor, inv를 호출할 수 있다.

### 문자 타입 Char

- 유니코드 한 글자를 포현하며 16비트
- `\u` 다음에 네 자리 16진수를 넣는 시퀀스를 통해 임의의 유니코드 문자를 문자 리터럴에 넣을 수 있다.
    - val pi = ‘\u03C0’ // pi
- 내부에서 Char 값은 그냥 문자 코드일 뿐이지만, 코틀린에서는 Char를 수 타입으로 취급하지 않는다. 하지만 유니코드 문자 집합 내에서 몇 가지 산술 연산을 허용한다.
    - `+/-` 연산자를 통해 문자에 수를 더하거나 빼면 그 수만큼 코드 포인트가 이동한 새 문자를 반환한다.
    - 두 문자로 뺄셈을 하면 두 문자의 코드포인트 간 거리를 얻을 수 있다.
        - println(h - a) // 7
        - println(a + 6) // f
        

### 수 변환

- 범위가 큰 타입이 사용 되어야 할 문맥에 범위가 작은 타입을 쓸 수 없다. 예를 들어 `Int` 값을 `Long` 변수에 대입할 수 없다.
    
    ```kotlin
    val n = 100 // Int
    val l: Long = n // Error Can't assign Int to Long
    ```
    
- 이는 암시적인 박싱 때문이다. 일반적인 Int 값이 꼭 원시 타입의 값으로 표현된다는 보장이 없다. 문맥에 따라 암시적인 박싱으로 인해 참조 타입이 될 수도 있기 때문이다. 따라서 더 큰 범위의 타입으로 변환하는 경우 다른 방식한 타입의 값을 만들어낼 수 있는 가능성이 생기고 이는 미묘한 오류를 발생시킬 수 있다. 위 코드를 올바른 코드로 인정하면 다음 연산이 false를 출력할 것이다.
    
    ```kotlin
    println(l == n) // false
    ```
    
- 정수 타입 사이의 변환은 대상 타입이 더 큰 범위를 담는 타입인 경우 손실 없이 진행된다.

### 비교와 동등성

- `==` 및 `!=` 는 `equals()` 를 가리키는 편의 문법이다. 참조 동등성을 확인하고 싶을 때는 `===` 와 `!==` 를 사용하면 된다.
- NaN은 기본적으로 어떠한 값과도 같지 않으므로 `isNaN()` 함수를 사용해야 한다.
- `==` 및 `!=` 비교는 두 인자가 같은 타입일 때만 허용한다. 이 역시 암시적인 박싱 때문에 혼란을 야기할 수 있기 때문이다.  그러나 이는 할당된 변수의 경우이며 모든 수 타입의 값은 서로 비교 연산이 가능하다.
    
    ```kotlin
    val a = 1 // Int
    val b = 2L // Long
    print(a == b) // Error: comparing Int and Long
    
    // 하지만 수 타입의 값에서는 가능
    print(1 <= 2L || 3 > 2.5) // No error
    
    ```
    

### 문자열

```kotlin
import java.util.Date

val = "name"
print("hello! $name! to day is ${Date()}")

val message = """
	이 줄은 로우 문자열입니다. 
	줄바꿈이 가능합니다.
"""
```

- 문자열 연산

```kotlin
"hello!".length // 6
"hello!".lastIndex // 5

val s = "Hello!"
println(s[0]) // H
```

### 배열

```kotlin
val a = emptyArray<String>()
val b = arrayOf("hello", "world") // Array<String>
val c = arrayOf(1, 2, 3) // Array<Int>

// 인덱스에서 원소를 만들어 내는 방법
val size = readLine()!!.toInt()
val squares = Array(size) {(it + 1)*(it + 1)}
```

- Array<Int>를 사용하는 배열은 모든 수를 박싱하기 때문에 그다지 실용적이지 않다. 더 효율적인 ByteArray, CharArray 등을 사용하는 것이 좋다.
    - charArrayOf(’a’, ‘b’), IntArray(10) {(it + 1)}
- 배열에는 `size` 및 `lastIndex` 프로퍼티가 있음
- 배열 타입의 변수 자체에는 실제 데이터에 대한 참조가 들어간다. 원본과 별도의 배열을 만들고 싶다면 `copyOf` 함수를 사용해야 한다.

```kotlin
val numbers = sqares.copyOf()
```

- 배열 타입은 자신과 같은 타입을 제외하고 모든 다른 배여 타입과 서로 하위 타입 관계가 성립하지 않는다고 가정하고 할당을 금지한다.
- 배열에 대한 `==` 및 `!=` 연산자는 원소 자체를 비교하지 않고 참조를 비교한다. 배열 내용을 비교하기 위해서는 `contentEquals()` 함수를 사용해야 한다.

### Null

- 아무것도 참조하지 않는 경우를 나타내는 null 값이 존재한다.
- 코틀린에서는 nullable 타입을 통해 Null일 가능성이 있는 타입과 그렇지 않은 경우를 확실히 구분한다.
- 코틀린에서 기본적으로 모든 참조타입은 널이 될 수 없는 타입이다.
- nullable한 타입을 만들기 위해서는 뒤에 `?` 를 붙이면 된다. 모든 널이 될 수 있는 타입은 원래 타입의 상위타입이 된다.
- Int나 Boolean 같은 원시 타입도 널이 될 수 있는 타입이 존재하며, 이 타입은 항상 박싱한 값만 표현한다.

```kotlin
val n: Int = 1 // 원시 타입의 값
val n: Int? = 1 // 박싱한 타입의 값을 참조
```

- 가장 좁은 널이 될 수 있는 타입은 `Nothing?` 이다. 이 타입은 널 상수 이외의 어떤 값도 포함하지 않는다. 이 타입은 null 값 자체의 타입이며 다른 모든 널이 될 수 있는 타입의 하위 타입이다. 가장 큰 널이 될 수 있는 타입은 `Any?` 이다.
- 널이 될 수 있는 타입은 원래 타입에 들어 있는 어떤 프로퍼티나 메서드도 제공하지 않는다.

### Null 가능성과 스마트 캐스트

- nullable 한 값을 처리하는 가장 직관적인 방법은 해당 값을 조건문을 통해 검사하는 것이다. 컴파일러의 스마트 캐스트는 조건문을 통해 앞에서 널 검사를 수행하면 뒤에 있는 값을 널이 아닌 값으로 타입 변환(cast)한다.
- 이러한 스마트 캐스트는 when 이나 루프 같은 조건 검사가 들어가는 다른 문이나 식 안에서도 작동 한다.

```kotlin
fun describeNumber(n: Int?) = when(n) {
    null -> "null"
    in 0..10 -> "small"
    in 11..100 -> "large"
    else -> "out of range"
}
```

- 하지만 null 검사와 사용 지점 사이에 값이 변경되는 경우에는 스마트 캐스트가 작동하지 않는다. 특히 객체의 가변 프로퍼티에 대해서는 절대 스마트 캐스트를 적용할 수 없다. 일반적으로 언제든 코드의 다른 부분에서 프로퍼티의 값을 바꿀 수 있기 때문이다.
- `!!` 연산자를 통해 널이 아님을 단언할 수 있다.이는 NPE를 발생시킬 수 도 있다.

```kotlin
val n = readLine()!!.toInt()
```

- `?.` 연산자를 통해 안전한 호출 체이닝이 가능하다. 연산자 왼쪽 값이 널이 아니면 일반적으로 작동하고 널일 경우에는 그냥 널을 반환해준다.

```kotlin
val n = readLine()?.toInt()
```

- 엘비스 연산자 `?:` 는 널을 대신할 기본 값을 지정할 수 있다.