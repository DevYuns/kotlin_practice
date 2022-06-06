# Function

함수는 어떤 입력(파라미터)를 받아서 자신을 호출한 코드 쪽에 출력 값을 반환(return)할 수 있는 재사용가능한 코드 블럭

### function

- 코틀린 함수의 파라미터는 무조건 불변이다.

```jsx
fun circleArea(radius: Double): Double {
	return PI * radius * radius
}

//
```

- 값에 의한 호출 의미론(call by value)을 사용하기 때문에 호출 인자로 전달한 변수를 변경해도 호출된 함수 내부의 파라미터 값에는 영향이 없다.
- 하지만 파라미터가 참조 타입(배열 등)이라면 호출한 쪽의 데이터는 그대로 남아있고 이 데이터에 대한 참조만 복사된다. 따라서 파라미터 자체는 함수 내부에서 바뀔 수 없지만, 일반적으로 파라미터가 가리키는 데이터는 바뀔 수 있다.

```jsx
fun increment(a: IntArray): Int {
	return ++a[0]
}

func main() {
	val a = intArray(1, 2, 3)
	println(increment(a)) // 2
	println(a.contentToString()) // 2, 2, 3 - a의 값이 바뀜
}
```

- 파라미터에는 항상 타입을 지정해야 하며, 반환 값 역시 문맥에 따라 변할 수 있으므로 명시하는 것이 좋다.
- `unit` 타입을 반환하는 경우(자바의 `void`) 함수가 의미 있는 반환값을 돌려주지 않는다는 의미이다. 이런 함수가 반환하는 값은 `Unit` 이라는 내장 타입에 속하는 Unit이라는 상수다. 함수 정의에서 반환값 타입을 지정하지 않으면 컴파일러는 `Unit` 함수르 정의한다고 가정한다.

```jsx
fun prompt(name: String): Unit {
	println("Hello $name!")

	return // 없어도 되지만 명시적으로 함수가 끝남을 나타냄
}
```

- 함수가 단일 식으로만 구현된다면 아래와 같은 형태

```kotlin
fun circleArea(radius: Double): Double = PI * radius * radius
```

- 블록이 본문인 함수를 정의할 때 `{}` 앞에 `=` 을 넣으면 이 블록이 익명 함수를 기술하는 람다로 해석되서 익명 함수를 리턴하는 것이 된다.

```kotlin
// 아래 함수는 정해진 반지름에 해당하는 원의 넓이를 계산해주는 다른 함수를 반환하는 함수 정의이다.
// 아래 정의의 블록문안에 return 을 넣으면 컴파일 오류가 난다.
fun circleArea(radius: Double) = {PI * radius * radius}
```

- named parameter
    - 이름있는 파라미터를 이용해 호출하면 인자의 순서가 바뀌어도 된다.
    - 코틀린 1.4부터 이름 있는 인자와 이름 없는 인자를 섞어서 호출할 수 있다. 다만 이름 없는 인자는 제대로 된 위치에 있어야 함

```kotlin
fun rectangleArea(width: Double, height: Double): Double {
	return width*height
}

main() {
	rectangleArea(height = 15.5, width = 20.5)
}
```

### code block

- 문법적으로 문장이 하나 들어갈 수 있는 위치에 여러 문장을 넣고 싶다면 블록을 사용할 수 있다.

### Overloading & default value

- 같은 함수를 여러 개 작성하여 함수 오버로딩이 가능하다.다만 컴파일러가 어떤 함수를 호출해야 할지 구분할 수 있도록 오버로딩한 함수의 파라미터 타입이 모두 달라야 한다. 반환 값만 다르면 컴파일 에러가 난다.
    - 오버로딩 해소 규칙
        - 파라미터의 개수와 타입을 기준으로 호출할 수 있는 함수 찾기
        - 덜 구체적인 함수를 제외시킨다. 파라미터의 타입이 상위 타입인 경우를 제외시킨다.
        - 이때 후보가 둘 이상이라면 컴파일 에러가 난다.
    
- 함수의 파라미터에 기본값을 넣어줄 수 있다. 보통 기본 값이 있는 파라미터를 인자 목록 뒤쪽에 몰아두는 것이 좋다. 그렇지 않을 경우 이름있는 파라미터 기법을 활용해야만 제대로된 호출을 할 수 있다.

```kotlin
fun readInt(radix: Int = 10) = readLine()!!.toInt(radix)
```

### vararg & spread operator

- 인자의 개수가 정해지지 않은 함수를 선언할 때 사용할 수 있다. 파라미터 정의 앞에 `vararg` 변경자(modifier)를 붙이는 것이다.

```kotlin
fun printSorted(vararg items: Int) {
	items.sort()
	println(items.contentToString())
}

fun main() {
	printSorted(5, 2, 3, 10) // [2, 3, 5, 10]
}
```

- printSorted 함수 내부에서 item는 `IntArray` 타입이다.
- 스프레드 연산자인 `*` 를 사용하여 배열을 가변 인자 대신 넘길 수 있다.
    - 스프레드는 배열을 복사하기 때문에 파라미터 배열의 내용을 바꿔도 원본 원소에는 영향을 미치지 않는다. 하지만 얕은(shallow) 복사가 이루어지므로 배열 내부에 참조가 들어있는 경우, 참조가 복사되기 때문에 참조가 가리키는 데이터가 호출하는 쪽과 함수 내부 배열에서 공유된다.

```kotlin
val numbers = intArray(5, 2, 1, 3)
printSorted(*numbers)
printSorted(numbers) // Error: passing IntArray instead of Int
```

- vararg 파라미터가 맨 마지막에 있는 파라미터가 아니라면, vararg 파라미터 이후의 파라미터는 이름 붙은 인자로만 전달할 수 있다.
- vararg 파라미터 뒤에 기본값 파라미터가 있는 경우, 기본값 파라미터를 이름 붙은 인자로 호출해야 사용할 수 있다.
    
    ```kotlin
    fun printArray(vararg items: Int, prefix: String = "") {}
    
    fun main() {
    	printArray(5, 2, 4, prefix = "!")
    }
    ```
    
- vararg는 오버로딩 해소에도 영향을 미친다. 다른 모든 요소가 같다면 vararg 파라미터가 있는 함수는 동일한 타입의 파라미터 수가 고정되어 있는 함수보다 덜 구체적인 함수로 간주된다.
    
    ```kotlin
    fun printArray(vararg items: Int) {} 
    fun printArray(a: Int, b: Int, c: Int) {}
    
    fun main() {
    	printArray(1, 2, 3) // 두 번째 함수가 보다 구체적이기 때문에 2번 함수 호출
    	printArray(1, 2) // 적용할 수 있는 함수가 1번 밖에 없으므로 1번 함수 호출
    }
    ```
    

### 함수의 영역과 가시성

- 파일에 직접 선언된 최상위 함수
- 어떤 타입 내부에 선언된 멤버 함수
- 다름 함수 안에 선언된 지역 함수

- 최상위 함수는 기본적으로 공개(public)이므로 프로젝트 어디에서나 쓰일 수 있다. 이러한 동작을 변경하기 위해 가시성 변경자(visibility modifier)를 사용한다.
    - internal : 함수가 적용된 모듈 내부에서만 함수를 사용. 모듈의 의미는 함께 컴파일 되는 파일 전부를 의미한다.
    - public: 모든 영역에서 사용 가능. 기본적으로 지정된다.
- 지역 함수는 함수 내부에 정의되며, 해당 지역 함수를 감싸고 있는 블록으로 한정된다.
- 지역 함수는 자신을 둘러싼 함수, 블록에 선언된 변수나 함수에 접근할 수 있으며, 지역함수를 둘러싸고 있는 함수의 파라미터도 포함된다.

### 패키지와 임포트

- 코틀린에서 패키지 구조와 파일구조는 반드시 일치할 필요는 없다. 기본적으로 루트 패키지는 이름이 없으며 특정 파일에 패키지를 설정하지 않으면 기본적으로 루트 패키지에 속하게 된다.
- 임포트는 다른 패키지의 코드를 불러올 수 있는 작업이다. 모든 선언은 일반적인 임포트 구문으로 불러올 수 있다.
- 만약 임포트해오는 선언의 이름이 같을 경우 alias를 활용할 수 있다.
- 어떤 영역에 속한 모든 선언을 한번에 임포트할 수 있다.

```kotlin
import foo.readInt as fooReadInt
import bar.readInt as barReadInt

// 모든 선언을 가져옴
import kotlin.math.*
```

### 조건문

- if문
- 코틀린의 if문은 식으로 사용할 수 있다.(삼항 연산자가 없음)
- if문을 식으로 사용할 때는 else문이 반드시 있어야 한다.

```kotlin
fun max(a: Int, b: Int): Int {
	if(a > b) return a
	else return b
}

// if문을 식으로 사용
fun max(a: Int, b: Int) = if(a > b) a else b
```

- if 식에서 return을 사용하면 편리한 경우가 있다. return 문은 존재하지 않는 값을 뜻하는 `Nothing` 이라는 특별한 타입의 값으로 간주된다. return은 이 문장을 둘러싼 함수가 끝난다는 뜻이다. `Nothing` 타입은 모든 코틀린 타입의 하위 타입이다. 따라서 식이 필요한 위치에 `return` 을 사용해도 타입 오류가 발생하지 않는다.
    - `Nothing` 과 `Unit` 을 구분해야 하는데, Nothing과 달리 Unit 타입에는 한 가지 인스턴스가 존재하는데 이 인스턴스는 보통 유용한 값이 없다는 사실을 표현한다. 반면 Nothing은 아예 값이 없다는 사실을 표현한다.
    

### 범위, 진행, 연산

- 순서가 정해진 값 사이의 수열(interval)을 표현하는 타입이 있다. for loop 안에서 주로 사용한다.
    - `..` 연산자 : val chars = ‘a’..’h’ // ‘a’ 부터 ‘h’ 까지의 모든 문자
        - 모든 비교가능한 타입에 대해 `..` 연산자를 써서 범위를 나타낼 수 있다.
        - `..` 연산은 시작 값과 끝 값이 범위에 포함된다.
    - `in` 연산자 : 어떤 값이 범위 안에 들어 있는지 여부(`!in` 도 가능)
        - print(nums in 10..99) // nums 변수가 10부터 99사이에 있는지
    - `until` : 10 until 100 // 10..99와 같음. 100은 포함되지 않는다.

- 범위와 관련된 진행(progression)이 있다. 진행의 간격은 양수여야 한다.
    - downTo : 5 in 10 downTo 1 // true
    - step : 1..10 step 3 // 1, 4, 7, 10
- 범위는 동적으로 할당되는 객체이기 때문에 비교 대신 범위를 사용하면 약간의 부가 비용이 든다. 하지만 컴파일러의 최적화로 인해 꼭 필요한 경우에는 Range 객체를 생성한다.

### when 문

- 짜여진 순서대로 조건을 찾아서 대응하는 문을 실행하며 일반적인 `switch` 문처럼 `fall through`가 발생하지 않는다.
- when문 역시 식으로 사용될 수 있다.

```kotlin
fun hexDigit(n: Int) = when {
	n in 0..9 -> '0' + n
	n in 10..15 -> 'A' + n - 10
	else -> '?'
}
```

- 만약 조건이 어떤 값에 대한 동등성이나 in 연산만 수행하는 경우 더욱 간결하게 표현할 수 있다.

```kotlin
// 비교의 대상이 when 문 뒤의 괄호로 명시되어 있다.
// 1, 2, 3의 경우 처럼 대상이 있는 형태에서는 한 가지안에 콤마로 분리해서 여러 조건을 넣을 수 있다.
fun numberToDescription(n: Int, max: Int = 100): String = when(n) {
	0 -> "Zero"
	1, 2, 3 -> "Small"
	in 4..9 -> "Medium"
	in 10..max -> "Large"
	!in Int.MIN_VALUE until 0 -> "Negative"
	else -> "Huge"
}
```

### 루프

- do-while

```kotlin
fun main() {
	var sum = 0
	var num

	do {
		num = readLine()!!.toInt()
		sum += num
	} while (num != 0)
}
```

- while: `do-while` 문이 블럭을 실행한 후에 조건을 검사하는 반면, while 문은 조건을 먼저 검사해서 블록을 실행할지 여부를 결정한다.

- for 문
    - for문 내의 x 값은 자동으로 새로운 값으로 갱신되며 불변하다.
    - Iterable에 속하는 배열, 리스트, 집합 등에 대해 for문을 수행할 수 있으며, 사용자 정의 타입도 Iterable을 상속받을 수 있다.

```kotlin
fun main() {
	val a = IntArray(10) {it * it} // 0, 1, 4, 9 ...
	var sum = 0

	for(x in a) {
		sum += x
	}
	println("Sum: $sum") // Sum: 285
}

// for 문을 통해 문자열의 각 문자에 대한 루프를 수행할 수도 있다.
fun parseIntNumber(s: String, fallback: Int = -1): Int {
	var num = 0
	
	if(s.length !in 1..31) return fallback

	for(c in s) {
		if(c !in '0'..'1') return fallback
		num = num*2 + (c - '0')
	}

	return num
}

// 수 범위에 대한 for 문

val a = IntArray(10) {it*it}
for(1 in 0..a.lastIndex) {
	//
}

// 문자열과 배열에는 원소나 문자의 인덱스 범위를 제공하는 indices 라는 프로퍼티가 있다
for(i in a.indices) {
	//
}
```

### 루프 제어 흐름 변경하기 : break 와 continue

- break: 즉시 루프를 종료시키고, 실행 흐름이 루프 다로 다음 문으로 이동하게 한다
- continue: 현재 루프 이터레이션을 마치고 조건 검사로 바로 진행하게 만든다.
- 코틀린 1.4 이전에서는 when 문 내에서 break의 사용이 금지되어 있었다. 그 이유는 코틀린의 when문은 폴스루가 없으므로 혼동을 막기 위함이었다. 그러나 1.4 이후에서는 when 내에서 사용하는 break나 continue에 대해 when 문을 둘러싸고 있는 가장 가까운 루프로 제어가 이동한다. 만약 when문이 붙은 가장 가까운 루프가 아니라 그보다 밖의 루프로 제어를 넘기기 위해서는 레이블을 활용해야 한다.
- break와 continue도 return과 마찬가지로 Nothing 타입의 식이다.

### 내포된 루프와 레이블

```kotlin
// 어떤 정수 배열 안에 어떤 정해진 순서로 정수가 배열된 하위 배열이 있는지 찾는 함수의 경우
fun indexOf(subArray: IntArray, array: IntArray): Int {
	outerLoop@ for (i in array.incices) {
		for(j in subArray.indices) {
			if(subArray[j] != array[i + j]) continue@outerLoop
		}
		return i
	}
	return -1
}

// 레이블을 사용하면 break와 continue를 when 식 안에서 쓰면서 제어를 옮길 대상 루프를 지정할 수 있다.

fun main() {
	val num = Random.nextInt(1, 101)

	loop@ while(true) {
		val guess = readLine()!!.toInt()
		
		val message = when {
			guess < num -> "Too Small"
			guess > num -> "Too Big"
			else -> break@loop
		}
		println(message)
	}
	pinrtln("Right: it's $num")
}

```

### 꼬리 재귀 함수

- `tailrec` 키워드를 활용하면 꼬리 재귀 함수에 대한 최적화 컴파일을 지원한다. 이는 재귀 함수를 비재귀 함수로 자동으로 변환해주는 것으로, 재귀 함수의 간결함과 비재귀함수의 성능상 이점을 가질 수 있다.
- 이같은 변환을 적용하려면 함수가 재귀 호출 다음에 아무 동작도 수행하지 말아야 한다.

### 예외 처리

- `throw` 를 통해 예외를 던질 수 있다.
- 예외를 던지면 프로그램은 예외를 잡아내는 핸들러를 찾는다. 예외와 일치하는 예외 핸들러가 없다면 함수 실행이 종료되고 함수가 스택에서 제거된다. 그리고 호출한 쪽의 문맥 안에서 예외 핸들러 검색을 수행한다. 이를 예외가 전파(propagation)됐다고 한다.
- 프로그램 진입점 까지 예외를 찾지 못햇다면 현재 스레드가 종료된다.
- throw도 `Nothing` 타입의 식이다.
- `try..catch..finally` 문을 사용할 수 있다.
    - 코틀린에서는 아직 한 catch 블록에서 여러 예외를 처리하지 못한다.
    - catch 블록은 선언된 순서대로 예외 타입을 검사하기 때문에 상위 타입을 처리하는 catch 문이 앞에 있으면 하위 타입인 예외도 다 잡아버린다.
- 코틀린에서 `try..catch..finally` 도 식으로 취급된다.