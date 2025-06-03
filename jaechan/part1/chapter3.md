# 람다란?

- 익명 함수를 단순화한 것이다.

### 특징

- 익명
  - 이름이 없음. 구현해야 할 코드에 대한 걱정거리가 줄어든다.
- 함수
  - 특정 클래스에 종속X
  - 메서드처럼 파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트를 포함
- 전달
  - 람다 표현식을 메서드 인수로 전달하거나 변수로 저장할 수 있다.
- 간결성
  - 익명 클래스처럼 필요 없는 코드를 구현할 필요가 없다.

### 람다 표현식 구성 요소

- 파라미터 리스트
- 화살표
- 람다 바디

```java
(Product p1, Product p2) -> p1.getSize().equals(p2.getSize());
-----------------------  -- ---------------------------------
	      람다 파라미터      화살표            람다바디
```

# 함수형 인터페이스

정확히 하나의 추상 메서드를 지정하는 인터페이스이다. Runnable, Comparator, Predicate 등이 있다.

## 함수 디스크립터

함수형 인터페이스의 추상 메서드 시그니처는 람다 표현식의 시그니처를 가르킨다. 람다 표현식의 시그니처를 서술하는 메서드를 함수 디스크립터라고 부른다. Runnable 인터페이스의 유일한 추상 메서드 run은 인수와 반환값이 없으므로 Runnable인터페이스는 인수와 반환값이 없는 시그니처로 생각할 수 있다.

자바8에서 java.util.function패키지로 여러 가지 새로운 함수형 인터페이스를 제공한다.

## 핵심 함수형 인터페이스

| 함수형 인터페이스   | 함수 디스크립터 | 추상 메서드           | 용도      | 예시                         |
| ------------------- | --------------- | --------------------- | --------- | ---------------------------- |
| `Predicate<T>`      | `T -> boolean`  | `boolean test(T t)`   | 조건 검사 | `x -> x > 5`                 |
| `Consumer<T>`       | `T -> void`     | `void accept(T t)`    | 소비/처리 | `x -> System.out.println(x)` |
| `Function<T, R>`    | `T -> R`        | `R apply(T t)`        | 변환/매핑 | `x -> x.toString()`          |
| `Supplier<T>`       | `() -> T`       | `T get()`             | 공급/생성 | `() -> new ArrayList<>()`    |
| `UnaryOperator<T>`  | `T -> T`        | `T apply(T t)`        | 단항 연산 | `x -> x * 2`                 |
| `BinaryOperator<T>` | `(T, T) -> T`   | `T apply(T t1, T t2)` | 이항 연산 | `(x, y) -> x + y`            |

## 기본형 특화 함수형 인터페이스

### Predicate 계열

| 함수형 인터페이스 | 함수 디스크립터     | 추상 메서드                  |
| ----------------- | ------------------- | ---------------------------- |
| `IntPredicate`    | `int -> boolean`    | `boolean test(int value)`    |
| `LongPredicate`   | `long -> boolean`   | `boolean test(long value)`   |
| `DoublePredicate` | `double -> boolean` | `boolean test(double value)` |

### Consumer 계열

| 함수형 인터페이스 | 함수 디스크립터  | 추상 메서드                 |
| ----------------- | ---------------- | --------------------------- |
| `IntConsumer`     | `int -> void`    | `void accept(int value)`    |
| `LongConsumer`    | `long -> void`   | `void accept(long value)`   |
| `DoubleConsumer`  | `double -> void` | `void accept(double value)` |

### Function 계열

| 함수형 인터페이스      | 함수 디스크립터  | 추상 메서드                        |
| ---------------------- | ---------------- | ---------------------------------- |
| `IntFunction<R>`       | `int -> R`       | `R apply(int value)`               |
| `IntToDoubleFunction`  | `int -> double`  | `double applyAsDouble(int value)`  |
| `IntToLongFunction`    | `int -> long`    | `long applyAsLong(int value)`      |
| `LongFunction<R>`      | `long -> R`      | `R apply(long value)`              |
| `LongToDoubleFunction` | `long -> double` | `double applyAsDouble(long value)` |
| `LongToIntFunction`    | `long -> int`    | `int applyAsInt(long value)`       |
| `DoubleFunction<R>`    | `double -> R`    | `R apply(double value)`            |
| `ToIntFunction<T>`     | `T -> int`       | `int applyAsInt(T value)`          |
| `ToLongFunction<T>`    | `T -> long`      | `long applyAsLong(T value)`        |
| `ToDoubleFunction<T>`  | `T -> double`    | `double applyAsDouble(T value)`    |

### Supplier 계열

| 함수형 인터페이스 | 함수 디스크립터 | 추상 메서드              |
| ----------------- | --------------- | ------------------------ |
| `BooleanSupplier` | `() -> boolean` | `boolean getAsBoolean()` |
| `IntSupplier`     | `() -> int`     | `int getAsInt()`         |
| `LongSupplier`    | `() -> long`    | `long getAsLong()`       |
| `DoubleSupplier`  | `() -> double`  | `double getAsDouble()`   |

### UnaryOperator 계열

| 함수형 인터페이스     | 함수 디스크립터    | 추상 메서드                            |
| --------------------- | ------------------ | -------------------------------------- |
| `IntUnaryOperator`    | `int -> int`       | `int applyAsInt(int operand)`          |
| `LongUnaryOperator`   | `long -> long`     | `long applyAsLong(long operand)`       |
| `DoubleUnaryOperator` | `double -> double` | `double applyAsDouble(double operand)` |

### BinaryOperator 계열

| 함수형 인터페이스      | 함수 디스크립터              | 추상 메서드                                       |
| ---------------------- | ---------------------------- | ------------------------------------------------- |
| `IntBinaryOperator`    | `(int, int) -> int`          | `int applyAsInt(int left, int right)`             |
| `LongBinaryOperator`   | `(long, long) -> long`       | `long applyAsLong(long left, long right)`         |
| `DoubleBinaryOperator` | `(double, double) -> double` | `double applyAsDouble(double left, double right)` |

## 이변수 함수형 인터페이스

| 함수형 인터페이스     | 함수 디스크립터     | 추상 메서드              | 용도              |
| --------------------- | ------------------- | ------------------------ | ----------------- |
| `BiPredicate<T, U>`   | `(T, U) -> boolean` | `boolean test(T t, U u)` | 두 인수 조건 검사 |
| `BiConsumer<T, U>`    | `(T, U) -> void`    | `void accept(T t, U u)`  | 두 인수 소비      |
| `BiFunction<T, U, R>` | `(T, U) -> R`       | `R apply(T t, U u)`      | 두 인수 변환      |

### @FunctionalInterface란?

함수형 인터페이스임을 알리는 어노테이션이다. 이 어노테이션을 사용한 인터페이스는 함수형 인터페이스가 아니면 컴파일 에러가 난다.

## 기본형 특화

자바의 모든 형식은 참조형 아니면 기본형에 해당한다. 하지만 제네릭 파라미터에는 참조형만 사용할 수 있다. 그래서 자바에서는 기본형을 참조형으로 변환하는 기능과 그 반대의 기능을 제공한다. 이것을 박싱, 언박싱이라고 한다.

또한 프로그래머가 코드를 편하게 코드를 구현할 수 있도록 박싱과 언박싱을 자동으로 이루어지도록 오토박싱이라는 기능도 제공한다.

```java
List<Integer> list = new ArrayList<>();
for(int i = 0; i < 100; i++){
	list.add(i); // 오토박싱으로 인해서 Integer형으로 바뀜.
}
```

참조형으로 변환되면 힙 메모리에 올라가기 떄문에 내부 연산이 많아진다. 그래서 기본 자료형에 대한 함수형 인터페이스도 제공된다.

```java
public interface IntPredicate{
	boolean test(int t);
}

IntPredicate evenNumbers = (int i) i % 2 == 0;
evenNumbers.test(1000);
```

## 형식 검사, 형식 추론, 제약

람다 형식에는 어떤 함수형 인터페이스를 구현하는지의 정보가 포함도어 있지 않다. 따라서 람다 표현식을 더 제대로 이해하려면 람다의 실제 형식을 파악해야 한다.

### 형식 검사

어떤 컨텍스트에서 기대되는 람다 표현식의 형식을 대상 형식이라고 부른다. 람다 표현식을 사용할 때 실제로는 아래와 같은 과정이 일어난다.

1. 람다가 사용된 컨텍스트를 filter의 정의를 통해 확인
2. 대상 형식은 정의를 통해 추론되며 T는 해당 값(들어오는 값의 형식)으로 대치된다.
3. 인터페이스의 추상 메서드는 무엇인지 봄
4. 매칭되는 람다시그니처를 찾고, 형식 검사를 함.

대상 형식이라는 특징 때문에 같은 람다 표현식이라도 호환되는 추상 메서드를 가진 다른 함수형 인터페이스로 사용될 수 있다.

```java
Callable<Integer> c = () -> 42; // 대상 형식은 Callable<Integer>
PrivilegedAction<Integer> p = () -> 42; // 대상 형식은 PrivilegedAction<Integer>
```

### void의 호환 규칙

람다의 바디에 일반 표현식이 있으면 void를 반환하는 함수 디스크립터와 호환된다.

```java
Predicate<String> p = s -> list.add(s); //
Consumer<String> b = s -> list.add(s); // T -> void이지만 유효함
```

## 형식 추론

자바 컴파일러는 람다 표현식이 사용된 콘텍스트(대상 형식)를 이용해서 람다 표현식과 관련된 함수형 인터페이스를 추론한다. 즉, 대상 형식을 이용해서 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그니처로도 추론할 수 있다.

```java
Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight()); // 형식 추론

```

## 지역 변수 사용

람다 캡쳐링

- 람다 표현식에서는 익명 함수가 하는 것처럼 자유 변수를 활용할 수 있다.

```java
int portNum = 3333;
Runnable r = () -> System.out.println(portNum);
```

제약사항은 외부에서 온 변수는 final이여야한다. 만약 이후에 해당 변수가 바뀌게 되면 오류가 난다.

인스턴스 변수는 힙에 저장되는 반면 지역 변수는 스택에 위치한다. 람다에서 지역 변수에 바로 접근할 수 있다는 가정하에 람다가 스레드에서 실행된다면 변수를 할당한 스레드가 사라져서 변수 할당이 해제되었는데도 람다를 실행하는 스레드에서는 해당 변수를 접근하려 할 수 있다. 따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공한다. 따라서 복사본의 값이 바뀌지 않아야 하므로 지역변수에는 한 번만 값을 할당해야 한다는 제약이 생긴 것이다.

## 메서드 참조

람다의 축약형이라고 할 수 있다. 가독성을 높일 수 있다.

1. 정적 메서드 참조
   1. Integer의 parseInt메서드는 Integer::parseInt로 표현 가능
2. 다양한 형식의 인스턴스 메서드 참조
   1. String의 length 메서드는 String::length로 표현할 수 있다.
3. 기존 객체의 인스턴스 메서드 참조
