# Chapter 2 - 동작 파라미터화 코드 전달하기

* 변화하는 요구사항에 대응
* 동작 파라미터화
* 익명 클래스
* 람다 표현식

소비자의 요구사항은 항상 변화한다. 그 변화에 대응하기 위해 엔지니어링적인 비용을 최소화해야 한다.<br>
또한 새로운 기능을 쉽게 추가할 수 있어야 한다. 동작 파라미터를 이용하면 변하는 요구사항에 쉽게 대응할 수 있다.<br>

### 변화하는 요구사항에 대응

```java
import java.util.ArrayList;
import java.util.List;

public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> list = new ArrayList<>();
	
	// for문을 순회하면서 녹색 사과를 찾아라.(외부 반복)
	for (Apple apple : inventory) {
		if (GREEN.equals(apple.getColor())) {
			list.add(apple);
        }
    }
    return list;
}
```

* 해결 포인트 : 녹색 사과를 찾을 수 있었다.
* 한계점 : 그러나 다른 색깔 사과를 찾으라 그런다면 코드의 수정이 불가피해진다.

### 색깔을 파라미터화

```java
import java.util.ArrayList;
import java.util.List;

public static List<Apple> filterAppleByColor(List<Apple> inventory, Color color) {
	List<Apple> list = new ArrayList<>();
	
	for (Apple apple : inventory) {
		if (apple.getColors().equals(color)) {
			list.add(apple);
		}
	}
	return list;
}

List<Apple> greenApples = filterAppleByColor(inventory, GREEN);
List<Apple> redApples = filterAppleByColor(inventory, RED);
```

* 해결 포인트 : 컬러를 파라미터에 넣어 여러 색깔을 처리할 수 있게 되었다.
* 한계점 : 그러나 색깔의 개수에 비례해서 메서드가 증가하게 된다.

### 가능한 모든 속성으로 필터링

```java
import java.util.ArrayList;
import java.util.List;

// 가능한 모든 속성들을 파라미터에 넣는다.
public static List<Apple> filterApples(List<Apple> inventory, Color color, int weight, boolean flag) {
	List<Apple> list = new ArrayList<>();
	
	for (Apple apple : inventory) {
		if ((flag && apple.getColor().equals(color)) || (!flag && apple.getWeight() > weight)) {
			list.add(apple);
		}
	}
	return list;
}
```

* 해결 포인트 : 파라미터를 통한 관리
* 한계점 : 의미없는 변수명이 다른 사람으로 하여금 코드의 혼란이 올 수 있다.

### 동작 파라미터화

```java
// 추상적인 관점으로 접근 -> 인터페이스 선언
public interface ApplePredicate {
	boolean test(Apple apple);
}

// 인터페이스를 구현한 구체 클래스 1
public class AppleHeavyWeightPredicate implements ApplePredicate {
	public boolean test(Apple apple) {
		return apple.getWeight() > 150;
    }
}

// 인터페이스를 구현한 구체 클래스 2
public class AppleGreenColorPredicate implements ApplePredicate {
	public boolean test(Apple apple) {
		return GREEN.equals(apple.getColor());
    }
}
```

### 추상적 조건으로 필터링

* 앞서 만들었던 인터페이스를 사용해 추상적 조건으로 필터링을 해보자.
* 이 때, 만들었던 ApplePredicate를 동작 파라미터화시킨다.

```java
import java.util.ArrayList;
import java.util.List;

// 추상적 조건을 동작 파라미터화
public static List<Apple> filterApples(List<Apple> inventory, ApplePredicate p) {
	List<Apple> list = new ArrayList<>();
	
	for (Apple apple : inventory) {
		if (p.test(apple)) {
			list.add(apple);
		}
	}
	return list;
}
```

* 다형성에 근거해 ApplePredicate 인터페이스를 구현하는 클래스만 만들어둔다면 된다.<br>
* 다만 여러 클래스를 구현하는 과정에서 조금 번거로울 수 있다.

### 복잡한 과정을 간소화

* 익명 클래스란, 이름이 없는 클래스이다.
* 익명 클래스는 선언과 동시에 인스턴스화 할 수 있다.

#### 익명 클래스

```java
import java.util.List;

List<Apple> redApples = filterApples(inventory, new ApplePredicate() {
	public boolean test(Apple apple) {
		return RED.equals(apple.getColor());
    }
});
```

* 익명 클래스를 이용하여 ApplePredicate를 한 번만 사용하고 버리는 객체로 만들어 메서드 동작 자체를 파라미터화 한 것이다.<br>
* 허나 이 방식으로 작성된 코드를 보게 되면 파라미터 부분에서 많은 코드양을 차지하는 것을 볼 수 있다.<br>
* 많은 개발자가 익명 클래스 사용에 익숙하지 않다.<br>

#### 람다 표현식(Lambda Expression)

```java
import java.util.List;

List<Apple> result = filterApples(inventory, (Apple apple) -> RED.eqauls(apple.getColor()));
```

#### 리스트 형식으로 추상화

```java
import java.util.ArrayList;
import java.util.List;

// 제네릭 타입
public interface Predicate<T> {
	boolean test(T t);
}

// Ex
public static <T> List<T> filter(List<T> list, Predicate<T> p) {
    List<Apple> list = new ArrayList<>();
	
	for (T t : list) {
		if (p.test(t)) {
			list.add(t);
		}
	}
	return list;
}
```

### 람다 표현식(Lambda Expression)

```java
import java.util.Comparator;

Comparator<Apple> byWeight = (Apple a1, Apple a2) -> a1.getWeight().compareTo(a2.getWeight());
```

#### 함수형 인터페이스(Functional Interface)

* 람다식을 다루기 위한 인터페이스
* 단, 함수형 인터페이스는 오직 하나의 추상 메서드만 정의되어 있어야 한다는 제약이 있다.
* `@FunctionalInterface`를 붙이면 컴파일러가 함수형 인터페이스를 올바르게 정의했는가를 확인해주므로 꼭 붙이는 것이 좋다.

```java
@FunctionalInterface
public interface MyInterface() {
	public abstract int max(int a, int b);  // 추상 메서드 선언
}
```