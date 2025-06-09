### 람다 표현식

#### 람다란 무엇인가

- 익명: 보통의 메서드와 달리 이름 없으므로 익명이라 표현한다. 구현해야 할 코드에 대한 걱정거리가 줄어든다
- 함수: 람다는 메서드처럼 특정 클래스에 종속되지 않으므로 함수라고 부른다. 하지만 메서드처럼 파라미터 리스트, 바디, 반환 형식, 가능한 예외 리스트를 포함한다
- 전달: 람다 표현식을 메서드 인수로 전달하거나 변수로 저장 할 수 있다
- 간결성: 익명 클래스처럼 많은 자질구레한 코드를 구현할 필요가 없다

```java

Comparator<Apple> byWeight = new Comparator<Apple>() {
   public int compare(Apple a1, Apple a2){
       return a1.getWeight().compareTo(a2.getWeight());
   }
Comparator<Apple> byWeight = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
}

```
위와 같이 간결하게 만들 수 있다

```
(Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```
- 파라미터 리스트: (Apple a1, Apple a2) 부분이며 Comparator의 compare 메서드 파라미터(사과 두개)
- 화살표: 화살표 ->는 람다의 파라미터 리스트와 바디를 구분한다
- 람다 바디: 두 사과의 무게를 비교한다. 람다의 반환값에 해당하는 표현식이다


### 함수형 인터페이스

```java
public interface Predicate<T> {
    boolean test(T t);
}
```
위와 같이 하나의 추상 메서드를 지정하는 인터페이스다. 인터페이스는 디폴트 메서드를 포함할 수 있다. 많은 디폴트 메서드가 있더라도 추상 메서드가 오직 하나면 함수형 인터페이스다
@Functionallnterface: 함수형 인터페이스임을 가리키는 어노테이션이며, 실제 함수형 인터페이스가 아닐 시 컴파일러가 에러를 발생시킨다

### 함수 디스크립터

함수형 인터페이스의 추상 메서드 시그니처는 람다 표현식의 시그니처를 가리킨다. 람다 표현식의 시그니처를 서술하는 메서드를 함수 디스크립터라고 한다.
Runnable 인터페이스의 유일한 추상 메서드 run은 인수와 반환값이 없으므로 Runnable 인테페이스는 인수와 반환값이 없는(void) 시그니처로 생각 할 수 있다
()-> void 표기는 파라미터 리스트가 없으며 void를 반환하는 함수를 의미한다


### 실행 어라운드 패턴
실제 자원을 처리하는 코드를 설정과 정리 두 과정이 둘러싸는 형태를 실행 어라운드 패턴이라고 부른다

### 항수형 인터페이스 및 정리

함수형 인터페이스는 오직 하나의 추상메서드를 지정한다. 함수형 인터페이스의 추상 메서드는 람다표현식의 시그니처를 묘사한다

| 함수형 인터페이스        | 함수 디스크립터       | 기본형 특화                                                                                          |
|-------------------------|----------------------|------------------------------------------------------------------------------------------------------|
| `Predicate<T>`          | `T -> boolean`       | `IntPredicate`, `LongPredicate`, `DoublePredicate`                                                  |
| `Consumer<T>`           | `T -> void`          | `IntConsumer`, `LongConsumer`, `DoubleConsumer`                                                     |
| `Function<T, R>`        | `T -> R`             | `IntFunction<R>`, `IntToDoubleFunction`, `IntToLongFunction`,<br>`LongFunction<R>`, `LongToDoubleFunction`, `LongToIntFunction`,<br>`DoubleFunction<R>`, `DoubleToIntFunction`, `DoubleToLongFunction`,<br>`ToIntFunction<T>`, `ToDoubleFunction<T>`, `ToLongFunction<T>` |
| `Supplier<T>`           | `() -> T`            | `BooleanSupplier`, `IntSupplier`, `LongSupplier`, `DoubleSupplier`                                  |



### 형식검사

람다가 사용되는 콘텍스트를 이용해서 형식을 추론할 수 있다. 어떤 콘텍스트에서 기대되는 람다 표현식의 형식을 대상 형식이라 부른다

```java
List<Apple> heavierThan150g = filter(inventory, (Apple, apple) -> apple.getWeight() > 150);
```

1. filter 메서드의 선언을 확인한다
2. filter메서드는 두번째 파라미터로 Predicate<Apple> 형식을 기대한다
3. Predicate<Apple>은 test라는 한 개의 추상 메서드를 정의하는 함수형 인터페이스다
4. test는 메서드 Apple을 받아 boolean을 반환하는 함수 디스크립터를 묘사한다
5. filter메서드로 전달된 인수는 이와 같은 요구사항을 만족해야 된다
위와 같은 형식 확인 과정이 진행 된다


### 형식추론

자바 컴파일러는 람다 표현식이 사용된 콘텍스트를 이용해서 람다 표현식과 관련된 함수형 인터페이스를 추론한다. 즉 대상 형식을 이용해서 함수 디스크립터를 알 수 있으므로 컴파일러는 람다의 시그치처도 추론 할 수 있다.
결과적으로 컴파일러는 람다 표현식의 파라미터 형식에 접근할 수 있으므로 람다 문법에서 생략할 수 있다

```java
// 형식 추론을 하지 않음
Comparator<Apple> c = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());

// 형식을 추론함
Comparator<Apple> c = (a1, a2) -> a1.getWeight().compareTo(a2.getWeight());

```

### 지역 변수 사용

람다 표현식에서는 익명 함수가 하는 것처럼 자유변수(파라미터로 넘겨진 변수가 아닌 외부에서 정의된 변수)를 활용하고 할 수 있는데 이와 같은 동작을 람다 캡처링이라고 부른다
이러한 이유로 자바에서는 람다에서 사용하는 지역 변수는 반드시 final이거나 실질적으로 final로 선언된 변수와 똑같이 사용되어야 한다
람다에서 지역변수에 바로 접근할 수 있따는 가정하에 람다가 스레드에서 실행 된다면 변수를 할당한 스레드가 사라져 변수항강이 해제되었는데도 람다를 실행하는 스레드에서는 해당 변수를 접근하려 할 수 있다
따라서 자바 구현에서는 원래 변수에 접근을 허용하는 것이 아니라 자유 지역 변수의 복사본을 제공한다. 따라서 복사본의 값이 바뀌지 않아야 하므로 지역 변수에 한번만 값을 할당해야 한다는 제약이 생긴 것이다


### 메서드 참조
메서드 참조를 이요하면 기존의 메서드 정의를 재활용해서 람다처럼 전달 할 수 있다.

- 정적 메서드 참조: Integer의 parseint 메서드는 Integer::parsInt
- 다양한 형식의 인스턴스 메서드 참조: String의 length 메서드는 String::length
- 기존 객체의 인스턴스 메서드 참조: Transaction 객체를 할당받은 expensiveTransaction 지역변수가 있고, Transaction 객체에 getValue 메서드가 있다면 expensiveTransaction::getValue


- 람다 표현식은 이름이 없는 익명 함수의 일종으로, 파라미터 리스트, 바디, 반환 형식, 그리고 예외 처리까지 포함할 수 있다
- 함수형 인터페이스는 하나의 추상 메서드만을 정의하는 인터페이스이다
- 람다 표현시을 이용해서 함수형 인터페이스의 추상 메서드를 즉성으로 제공할 수 있으며 람다 표현식 전체가 함수형 인터페이스의 인스턴스로 취급된다
- 람다 표현식의 기대형식을 대상 형식 이라고 한다
- 메서드 참조를 이용하면 기존의 메서드 구현을 재사용하고 직접 전달 할 수 있다