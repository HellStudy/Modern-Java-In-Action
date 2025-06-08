### 동작파라미터화 코드 전달

동작 파라미터화란 아직은 어떻게 실행할지 정해지지 않는 코드 블록을 **나중에 실행하도록 호출** 하는 것을 말한다

1. 코드 블록(람다 표현식)을 메서드의 인수로 전달
2. 메서드 내부에서 특정 시점에 해당 코드 블록 실행
3. 이를 통해 메서드의 동작을 파라미터화(다양화)할 수 있음

예를 들어, 컬렉션을 처리할 때 다음과 같은 메서드를 구현한다고 가정하자.

- 리스트의 모든 요소에 대해 '어떤 동작'을 수행할 수 있음
- 리스트 작업이 끝난 후 '어떤 다른 동작'을 수행할 수 있음
- 에러가 발생하면 '정해진 동작'을 수행할 수 있음

이처럼 다양한 기능을 동작 파라미터화를 통해 유연하게 처리할 수 있다.

```java
List<Apple> heavyApples2 = filterApples(inventory, (Apple a) -> a.getWeight() > 150);

public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> p) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (p.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}

```

- `(Apple a) -> a.getWeight() > 150`는 실행을 미룬 코드 블록(람다 표현식)이다.
- 이 코드 블록은 즉시 실행되지 않고 `filterApples`에 전달 된다
- `filterApples` 메서드 내부의 `p.test(apple)` 호출 시점에서 비로소 실행된다
- 코드 블럭은 `Predicate<Apple>` 인터페이스의 구현체로 작동

### 변화하는 요구사항에 대응하기

```java
public static List<Apple> filterApplesByColor(List<Apple> inventory, Color color) {
    List<Apple> result = new ArrayList<>();
    for (Apple apple : inventory) {
        if (apple.getColor().equals(apple.getColor(color))) {
            result.add(apple);
        }
    }
    return result;
}

if((flag && apple.getColor().equals(apple.getColor(color))) 
    || (!flag && apple.getWeight() > weight) ){
    }

```

갑자기 녹색 말고 빨간 사과로 필터링 하고 메서드를 새로 만들고 if문에 추가를 하면 되지만  나중에 요구사항이 추가되면 대응할 수 없어 비슷한 코드가 반복 존재하면 코드를 추상화 하면 된다
색 뿐만 아니라 무게 등 여러가지가 바뀌면 중복된 필터 메서드를 만들거나 if에 모든 것을 처리하는 하나의 필터 메서드를 구현 해야 된다


### 동작 파라미터 화

```java

public interface ApplePredicate {
	boolean test(Apple apple);
}

public class AppleHeavyWeightPredicate implements ApplePredicate {
	public boolean test(Apple apple) {
		return apple.getWeight() > 150;
    }
}

public class AppleGreenColorPredicate implements ApplePredicate {
	public boolean test(Apple apple) {
		return GREEN.equals(apple.getColor());
    }
}
```

ApplePredicate는 선택에 따라 전략을 캡슐화 했다. ApplePredicate는 알고리즘 패밀리고  AppleHeavyWeightPredicate와 AppleGreenColorPredicate가 전략이다
동작 파라미터화는 메서드가 다양한 동작을 받아서 내부적으로 다양한 동작을 수행할 수 있다


### 추상적 조건으로 필터링


```java
public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
    List<Apple> result = new ArrayList<>();

    for (Apple apple : inventory) {
        if (p.test(apple)) {
            result.add(apple);
        }
    }
    return result;
}

```

ApplePredicate를 구현한 클래스를 만들어서 filterApples에 전달하면, filterApples 메서드는 다양한 조건에 따라 Apple을 필터링할 수 있으므로, 동작을 파라미터화한 것이다


### 익명클래스

```java

List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
    public boolean test(Apple apple) {
        return RED.equals(apple.getColor());
    }
});

```

익명클래스는 블록 내부에 선언된 클래스와 비슷한 개념이고 이름이 없는 클래스이며 클래스 선완과 인스턴스화를 동시에 할 수 있다



### 람다 표현식 사용
```java
List<Apple> result = filterApples(inventory, (Apple apple) -> RED.equals(apple.getColor()));
```

### 리스트 형식으로 추상화 및 람다식 표현

```java
public interface Predicate<T> {
	boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<T> result = new ArrayList<>();
	
	for (T t : list) {
		if (p.test(t)) {
            result.add(t);
		}
	}
	return result;
}

List<Apple> redApples = filter(inventory, (Apple apple) -> RED.equals(apple.getColor()));
List<Integer> evenNumbers = filter(number, (Integer i) -> i%2 == 0);

```

- 동작 파라미터화에서는 메서드 내부적으로 다양한 동작을 수행할 수 있도록 코드를 메서드 인수로 전달한다
- 동작 파라미터화를 이용하면 변화하는 요구사항에 더 잘 대응할 수 있는 코드를 구현할 수 있으며 나중에 엔지니어링 비용을 줄일 수 있다
- 코드를 인수로 전달하는 기법 을 이용하면 동작을 메서드의 인수로 전달할 수 있다.

