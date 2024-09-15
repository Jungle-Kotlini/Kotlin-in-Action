# 2.3 선택 표현과 처리 : enum과 when 

이번 절에서는 코틀린의 구성 요소 중 when에 대해 설명한다. 
- 코틀린에서의 **when**은 자바의 switch를 대치하되 훨씬 더 강력하며, 앞으로 더 자주 사용할 프로그래밍 요소라 생각할 수 있다. 
- **enum을 선언하는 방법**과 **스마트 캐스트**에 대해서도 살펴본다.
### enum을 사용하는 이유?
- **고정된 값들의 집합을 표현**하고 **안전하고 가독성 있는 코드를 작성**하기 위해서 !
	- **의미 있는 이름**으로 값들을 정의할 수 있어 코드의 **가독성**이 높아진다
	- **잘못된 값을 사용하지 않도록 방지**합니다. `enum`으로 정의된 값들만 사용할 수 있기 때문에 안전하게 코딩할 수 있다. 
## enum 클래스 정의

```java
// 코틀린
enum class Color {
	RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET
}

// 자바
public enum Color {
    RED, ORANGE, YELLOW, GREEN, BLUE, INDIGO, VIOLET;
}

```

- 코틀린에서는 **enum class**를 사용, 자바에서는 **enum**을 사용
- 코틀린에서 enum은 **소프트 키워드**라 부르는 존재다.
- 자바처럼 enum은 단순히 값만 열거하는 존재가 아니다. 
	- enum 클래스 안에 프로퍼티나 메소드를 정의할 수 있음
#### 소프트 키워드
- **정해진 문맥에서만** 특별한 역할을 하고, 다른 문맥에서는 **일반적인 식별자로 사용할 수 있는 단어**를 의미
	- 위에 같이 enum은 class 앞에 있을 때는 특별한 의미를 지닌다.
	- 아래와 같이 클래스 앞에 있지 않은 문맥에서는 일반적인 식별자로 사용할 수 있다. 
```kotlin
val enum = "이건 변수 이름입니다"
```
- 소프트 키워드의 반대는 **하드 키워드**로 class는 하드 키워드이다. 


### 프로퍼티와 메소드가 있는 enum 클래스 선언하기 
in java
```java
public enum Color {
    RED(255, 0, 0),
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255),
    INDIGO(75, 0, 130),
    VIOLET(238, 130, 238);

    // 프로퍼티
    private final int r;
    private final int g;
    private final int b;

    // 생성자
    Color(int r, int g, int b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }

    // 메소드
    public int rgb() {
        return (r * 256 + g) * 256 + b;
    }

    public static void main(String[] args) {
        // BLUE의 rgb 값을 출력
        System.out.println(Color.BLUE.rgb()); // 출력: 255
    }
}
```
in kotlin
```kotlin
enum class Color(val r: Int, val g: Int, val b: Int) { // 상수의 프로퍼티를 정의한다.
    RED(255, 0, 0),      // 각 상수를 생성할 때 그에 대한 프로퍼티 값을 지정
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255),
    INDIGO(75, 0, 130),
    VIOLET(238, 130, 238); // 세미콜론 필수

    fun rgb() = (r * 256 + g) * 256 + b // enum 클래스 안에서 메소드를 정의한다.
}

println(Color.BLUE.rgb()) // 출력: 255

```

- enum에서도 일반적인 클래스와 마찬가지로 생성자와 프로퍼티를 선언한다.
- 각 enum 상수를 정의할 때는 그 상수에 해당하는 프로퍼티 값을 지정해야 한다.
#### 코드 이해하기
1. **`enum class Color(val r: Int, val g: Int, val b: Int)`**

- `enum class Color`: `Color`라는 이름의 열거형 클래스를 정의합니다.
- `(val r: Int, val g: Int, val b: Int)`: 각 색상에 대해 빨강(`r`), 초록(`g`), 파랑(`b`)의 값을 저장할 **프로퍼티(속성)**  정의합니다. 여기서 `val`은 값이 변경되지 않는 불변 속성을 의미합니다.

2. `RED(255, 0, 0), ... VIOLET(238, 130, 238);`
- 각 색상(RED, ORANGE 등)은 `Color` 열거형의 **상수**로 정의됩니다.
- 각 상수 뒤의 괄호 안의 숫자들은 해당 색상의 RGB 값을 나타냅니다. 예를 들어, `RED`는 빨강이 255, 초록이 0, 파랑이 0인 색입니다.
- **세미콜론(;)**: 모든 상수들을 정의한 후에 세미콜론을 반드시 넣어야 합니다. 이는 열거형 상수 목록과 그 이후에 오는 메소드(`rgb()`)를 구분하기 위해 필요합니다.
	- 코틀린에서는 `enum` 클래스에서 열거형 상수 목록과 그 이후의 메소드나 프로퍼티를 구분하기 위해 세미콜론을 사용

3. 메소드 정의
```kotlin
fun rgb() = (r * 256 + g) * 256 + b
```
- `rgb()`는 각 색상의 RGB 값을 하나의 정수로 변환하는 **메소드**입니다.
- 왜 이렇게 계산할까요?
    - RGB 색상은 빨강, 초록, 파랑 세 가지 색상의 강도로 표현됩니다. 각 색상은 0부터 255까지의 값을 가질 수 있습니다.
    - 이 세 가지 값을 하나의 정수로 합치기 위해, 각 색상 값을 특정한 위치에 배치하여 합칩니다.
    - **계산 방식**:
        - 빨강(`r`) 값에 256을 곱하고, 여기에 초록(`g`) 값을 더합니다: `(r * 256 + g)`
        - 그 결과에 다시 256을 곱하고, 파랑(`b`) 값을 더합니다: `((r * 256 + g) * 256 + b)`
    - 이렇게 하면 각 색상의 값이 고유한 위치에 배치된 하나의 큰 정수를 얻을 수 있습니다.

#### RGB 값을 다시 빨강, 초록, 파랑으로 분리하고 싶다면?? 
```kotlin
enum class Color(val r: Int, val g: Int, val b: Int) {
    RED(255, 0, 0),
    ORANGE(255, 165, 0),
    YELLOW(255, 255, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255),
    INDIGO(75, 0, 130),
    VIOLET(238, 130, 238);

    fun rgb() = (r * 256 + g) * 256 + b

    fun getRed() = r
    fun getGreen() = g
    fun getBlue() = b
}

fun main() {
    val color = Color.INDIGO
    println("RGB 값: ${color.rgb()}") // RGB 값: 4936606
    println("빨강: ${color.getRed()}")   // 빨강: 75
    println("초록: ${color.getGreen()}") // 초록: 0
    println("파랑: ${color.getBlue()}")  // 파랑: 130
}
```


<hr>
## when으로 enum 클래스 다루기

- Richard Of York Gave Battle In Vain! 아래 예시 내용 
	- 무지개 색 기억하기 위한 연상법
	- R,O,Y,G,B,I,V : 빨주노초파남보
- **자바에 switch문**에 해당하는 코틀린 구성 요소는 **when**이다.
- if와 마찬가지로 when도 값을 만들어내는 **식**이다. 
	- 식인 본문인 함수에 when을 바로 사용할 수 있다.

### when을 사용해 올바른 enum값 찾기
```kotlin
fun getMnemonic(color:Color)=
	when (color) {
		Color.RED -> "Richard"
		Color.ORANGE -> "Of"
		Color.YELLOW -> "York"
		Color.GREEN -> "Gave"
		Color.BLUE -> "Battle"
		Color.INDIGO -> "In"
		Color.VIOLET -> "Vain"		
	}

println(getMnemonic(Color.BLUE)) // Battle
```

- color로 전달된 값과 같은 분기를 찾는다. 
- 자바와 달리 각 분기의 끝에 break를 넣지 않아도 된다
	- 자바에서는 break를 빼먹어 오류가 생기는 경우가 자주 있다. 
	- 성공적으로 매치되는 분기를 찾으면 switch는 그 분기를 실행한다. 


- 한 분기 안에서 여러 값을 매치 패턴으로 사용할 수도 있다.
	- 그럴 경우 값 사이를 콤마(,)로 분리한다. (아래 예시)
### 한 when 분기 안에 여러 값 사용하기
```kotlin
fun getWarmth(color:Color) = when(color){
	Color.RED, Color.ORANGE, Color.Yellow -> "warm"
	Color.GREEN -> "netural"
	Color.BLUE, Color.INDIGO, Color.VIOLET -> "cold"
}
println(getWarmth(Color.ORANGE)) // warm
```

### enum 상수 값을 임포트해서 enum 클래스 수식자 없이 enum 사용하기
```kotlin
import ch02.colors.Color // 다른 패키지에서 정의한 Color 클래스를 임포트
import ch02.colors.Color.* //짧은 이름으로 사용하기 위해 enum 상수를 모두 임포트

fun getWarmth(color:Color) = when(color){
	RED, ORANGE, YELLOW -> "warm" // 임포트한 enum 상수를 이름만으로 사용
	GREEN -> "neutral"
	BLUE, INDIGO, VIOLET -> "cold"
}

```

<hr>
## when과 임의의 객체를 함께 사용

- 코틀린에서 when은 자바의 switch보다 훨씬 더 강력하다. 
- 분기 조건에 상수(enum 상수나 숫자 리터럴)만을 사용할 수 있는 자바 switch와 달리 코틀린 when의 분기 조건은 임의의 객체를 허용한다. 두 색을 혼합했을 때 미리 정해진 팔레트에 들어있는 색이 될 수 있는지 알려주는 함수를 작성한다.
### when의 분기 조건에 여러 다른 객체 사용하기
```kotlin
fun mix(c1:Color, c2:Color) = 
	when (setOf(c1,c2)) {
		setOf(RED, YELLOW) -> ORANGE // 두 색을 혼합해서 다른 색을 만들 수 있는 경우 
		setOf(YELLOW, BLUE) -> GREEN
		setOf(BLUE, VIOLET) -> INDIGO
		else -> throw Exception("Dirty color") // 매치 되는 분기 조건 없을 시 
	}

>>> println(mix(BLUE, YELLOW)) // GREEN
```

-  코틀린 표준 라이브러리에는 인자로 전달받은 여러 객체를 그 객체들을 포함하는 집합인 Set 객체로 만드는 setOf라는 함수가 있다. 

- **집합(`Set`) 사용**
    - `setOf(c1, c2)`는 두 개의 색상(`c1`과 `c2`)을 집합으로 만듭니다. 집합은 순서에 상관없이 원소를 모아놓는 구조입니다.
    - 예를 들어, `setOf(BLUE, YELLOW)`와 `setOf(YELLOW, BLUE)`는 동일한 집합입니다.

- **`when`의 조건**
    - `when`의 조건으로 집합을 사용할 수 있습니다. 각 분기에서 `setOf(RED, YELLOW)` 같은 집합과 비교하여, 조건이 맞으면 해당 분기로 넘어갑니다.
    - `when`은 `setOf(c1, c2)`와 각 분기의 집합을 비교하여 일치하는 조건을 찾습니다.

- **동등성 검사**    
    - 앞의 코드는 처음에는 setOf(c1,c2)와 setOf(RED, YELLOW)를 비교하고, 그 둘이 같지 않으면 계속 다음 분기의 조건 객체와 setOf(c1,c2)를 차례로 비교하는 식으로 작동
	- 모든 분기 식에서 만족하는 조건을 찾을 수 없다면 else 분기의 문장을 계산

- **매칭되지 않는 경우**
    - 위의 모든 분기와 매칭되지 않는 경우 `else` 분기가 실행되며 예외를 던집니다. 이는 `setOf(c1, c2)`가 미리 정의된 집합과 맞지 않을 때의 처리입니다.

<hr>

## 인자 없는 when 사용

위에 코드에서 여러 Set 인스턴스를 생성했다. 이 함수가 아주 자주 호출되면 불필요한 가비지 객체가 늘어난다. 인자가 없는 when 식을 사용하면 불필요한 객체 생성을 막을 수 있다. 
코드는 약간 읽기 어려워지지만 성능을 더 향상시킬 수 있다. 
```java
fun mixOptimized(c1:Color, c2:Color)=
	when {
		(c1 == RED && C2 == YELLOW) || (c1==YELLOW && c2 == RED) -> ORANGE
		(c1 == YELLOW && C2 == BLUE) || (c1==BLUE && c2 == YELLOW) -> GREEN
		(c1 == BLUE && C2 == VIOLET) || (c1==VIOLET && c2 == BLUE) -> INDIGO
		else -> throw Exception("Dirty color")
	}

println(mixOptimized(BLUE, YELLOW)) //GREEN
```

- when에 아무 인자도 없으려면 각 분기의 조건이 불리언 결과를 계산하는 식이어야 한다.
- 추가 객체를 만들지 않는다는 장점이 있지만 가독성은 더 떨어진다.


<hr>
## 스마트 캐스트 : 타입 검사와 타입 캐스트를 조합

### (1+2) + 4 와 같은 간단한 산술식을 계산하는 함수 구현

- 식을 트리 구조로 저장
- 노드는 합계(Sum)나 수(Num) 중 하나다. 
- Num은 항상 리프(말단) 노드 
- Sum은 자식이 둘 있는 중간(non-terminal) 노드다. 
- Sum은 노드의 두 자식은 덧셈의 두 인자다. 

```kotlin
// 식을 위한 Expr 인터페이스 
interface Expr

// value라는 프로퍼티만 존재하는 단순한 클래스로 Expr 인터페이스를 구현한다.
class Num(val value:Int) : Expr

// Expr 타입의 객체라면 어떤 것이나 Sum 연산의 인자가 될 수 있다. 따라서 Num이나 다른 Sum이 인자로 올 수 있다. 
class Sum(val left:Expr, val right:Expr) : Expr

println(eval( Sum( Sum(Num(1),Num(2)) , Num(4)) )) //7
```

(1 + 2) + 4 => Sum( Sum(Num(1),Num(2)) , Num(4))  
![[Pasted image 20240915192330.png]]

### Expr 인터페이스의 두 가지 구현 클래스
- 어떤 식이 수라면 그 값을 반환한다.
- 어떤 식이 합계라면 좌항과 우항의 값을 계산한 다음에 그 두 값을 합한 값을 반환한다.

#### 자바 스타일로 작성한 함수
```kotlin
fun eval(e:Expr) : Int{
	if (e is Num) {
		val n = e as Num // Num으로 타입을 변환 (불필요한 중복)
		return n.value
	}
	if (e is Sum) {
		return eval(e.right) + eval(e.left) // 변수 e에 대해 스마트 캐스트 사용
	}
	throw IllegalArgumentException("Unknown expression")
}
println(eval( Sum( Sum(Num(1),Num(2)) , Num(4)) )) //7
```

- 코틀린에서는 is를 사용해 변수 타입을 검사
	- is 검사는 자바의 instanceof와 비슷하다. 
		- instanceof로 확인한 후 다음에 그 타입에 속한 멤버에 접근하기 위해서는 명시적으로 변수 타입을 캐스팅해줘야 한다. 
		- 이런 멤버 접근을 여러 번 수행해야 한다면 변수에 따로 캐스팅한 결과를 저장한 후 사용해야 한다. 
	- 코틀린에서는 컴파일러가 대신 캐스팅 해준다. 
		- 어떤 변수가 원하는 타입인지 일단 is로 검사하고 나면 굳이 변수를 원하는 타입으로 캐스팅하지 않아도 마치 처음부터 그 변수가 원하는 타입으로 선언된 것처럼 사용 가능하다. 실제로는 컴파일러가 캐스팅을 수행한다 => 이를 **스마트 캐스트** 라 부른다.

- eval 함수에서  e의 타입이 Num인지 검사한 다음 부분에서 컴파일러는 e의 타입을 Num으로 해석한다. 그래서 Num의 프로퍼티인 value를 명시적 캐스팅 없이 e.value로 사용할 수 있다. 
	- Sum 프로퍼티인 right, left도 마찬가지

#### 스마트 캐스트 
- is로 변수에 든 값의 타입을 검사한 다음에 **그 값이 바뀔 수 없는 경우에만** 작동한다. 
- **프로퍼티는 반드시 val**이어야 한다. 커스텀 접근자를 사용한 것도 안된다. 
	- val이 아니거나 val이지만 커스텀 접근자를 사용하는 경우에는 해당 프로퍼티에 대한 접근이 항상 같은 값을 내놓는다고 확신할 수 없기 때문
- **원하는 타입으로 명시적으로 타입 캐스팅**하려면 **as 키워드**를 사용한다.
	- `val n = e as Num`

## 리팩토링 : if를 when으로 변경

eval 함수에서 return문과 중괄호를 없애고 if 식을 본문으로 사용해 더 간단하게 만들 수 있다.
```kotlin
fun eval(e:Expr) : Int = 
if (e is Num){
	e.value
} else if (e is Sum){
	eval(e.right) + eval(e.left)
} else {
	throw IllegalArgumenException("Unknow expression")
}

println(eval(Sum(Num(1),Num(2))))
```

if 중첩 대신 when 사용하기 (타입을 검사하면 스마트 캐스트가 이루어져 캐스팅할 필요 없음)
```kotlin
fun eval(e:Expr) : Int=
	when(e){
		is Num -> e.value
		is Sum -> eval(e.right) + eval(e.left)
		else -> throw IllegalArgumenException("Unknow expression")
	}
```

## if와 when 분기에서 블록 사용

if나 when 모두 분기에 블록을 사용할 수 있다. 블록의 마지막 문장이 블록의 전체의 결과가 된다. 

### 분기에 복잡한 동작이 들어가 있는 when 사용하기

```kotlin
fun evalWithLogging(e:Expr) : Int = 
	when(e){
		is Num->{
			println("num:${e.value}")
			e.value
		}
		is Sum->{
			val left = evalWithLogging(e.left)
			val right = evalWithLogging(e.right)
			println("sum: $left + $right")
			left + right
		}
		else -> throw IllegalArgumenException("Unknow expression")
	}

println(evalWithLogging(Sum( Sum(Num(1),Num(2)) , Num(4))))
// num : 1
// num : 2
// sum : 1+2
// num : 4
// sum : 3+4
// 7
```