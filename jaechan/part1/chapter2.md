# 동적 파라미터화

아직은 어떻게 실행할 것인지 결정하지 않은 코드 블록을 의미한다. 이 코드블록은 나중에 실행된다. 예를 들어 나는 이러한 동작을 여기서 실행할건데 구체적인 내용은 잘 몰라! 라는 느낌이다. 이것을 통해 우리는 유연성을 얻을 수 있다.

예를 들어 생각해보자. 우리는 옷 쇼핑몰을 운영중이다.

요구사항은 아래와 같다

- 검정색

```java

enum Color { RED, ORANGE, ,YELLOW, GREEN, BLUE, BLACK, WHITE}
public List<Product> filterByColor(List<Product> products, ***Color colo***){
	List<Product> result = new ArrayList<>();
	for(Product product : products) {
		***if(product.getColor().equals(BALCK)) {
			result.add(product);
		}***
	}
}
```

- 사이즈가 L인 옷

```java
emum Size { S, M, L, XL, XXL}
public List<Product> filterBySize(List<Product> products, Size size){
	List<Product> result = new ArrayList<>();
	for(Product product : products) {
		**if(product.getSize().equals(L)) {
			result.add(product);
		}**
	}
}
```

- 사이즈가 L이면서 검정색 옷

```java
public List<Product> filterByColorAandSize(List<Product> products, ***Color colo, Size size***){
	List<Product> result = new ArrayList<>();
	for(Product product : products) {
		***if(product.getColor().equals(BALCK) && product.getSize().equals(L)) {
			result.add(product);
		}***
	}
}
```

이 코드들의 공통점은 필터링 조건문이 반복된다는 것이다. 만약 요구사항이 바뀐다면 모든 메서드들을 바꿔야 하며, 필터링이 늘어날 수록 고치기 힘든 코드가 되어버린다. 이 패턴은 안티패턴으로 사용하지 않는 것이 좋다.

다음 로직은 모든 속성을 메서드 파리미터로 추가한 것이다.

```java
public List<Product> filterProduct(List<Product> products, ***Color colo, Size size, boolean flag***){
	List<Product> result = new ArrayList<>();
	for(Product product : products) {
		***if((flag && product.getColor().equals(BALCK)) && (!flag && product.getSize().equals(L)) {
			result.add(product);
		}***
	}
}
```

이렇게 작성하면 사용할 때 아래와 같이 사용해야한다.

```java
List<Product> blackProducts = filterProduct(products, BALCK, null, true);
List<Product> largeSizeProducts = filterProduct(products, null, L , false);
```

딱 이 코드만 봤을 떄 이 함수가 무엇을 하는지 정확하게 이해할 수 없다. 특히 파라미터 부분을 보면 뭐를 의미하는지 제대로 이해할 수 없다.

이 문제에서 추가하는 조건은 boolea값을 통해 조건을 검사한다. 자바에서 참 또는 거짓을 반환하는 함수를 프레디케이트라고 한다. 선택 조건을 결정하는 인터페이스를 정의해서 사용해보자.

```java
public interface ProductPredicate {
	boolean test(Product product);
}
```

이걸 사용해서 만들어보자

```java
public class ProductBlackColorPredicate implements ProductPredicate {
	public boolean test(Product product){
		return product.getColor().equals(BLACK);
	}
}

public class ProductLargeSizePredicate implements ProductPredicate {
	public boolean test(Product product){
		return product.getSize().equals(L);
	}
}
public class ProductLargeSizeAndBlackColorPredicate implements ProductPredicate {
	public boolean test(Product product){
		return L.equals(product.getSize()) && BALCK.equals(product.getColor());
	}
}

---

public filterProducts(List<Product> products, ProductPredicate predicate){
	List<Product> results = new ArrayList<>();
	for(Product product : products) {
		***if(predicate.test(product) {
			results.add(product);
		}***
	}
}
```

> product.getSize()나 product.getColor()는 null 일 수 있다. 그래서 null.equals로 인해서 NPE를 일으킬 수 있기 때문에 BLACK.equals(product.getColor()) 이 더 안전한 사용법이다.

```java
List<Product> blackProducts = filterProducts(products, new ProductBlackColorPredicate());

List<Product> blackProducts = filterProducts(products, new ProductLargeSizePredicate());

List<Product> blackAndLargeSizeProducts = filterProducts(products, new ProductLargeSizeAndBlackColorPredicate());
```

if조건문을 추상화하여 다양하게 필터링 할 수 있는 함수를 만들었다. ProductPredicate를 통해서 동작이 결정된다. 이것은 메서드의 동작을 파라미터화한 것이다.

## 익명 클래스로 단순화하기

익명 클래스는 이름이 없는 클래스이다. 그래서 이걸 이용하면 선언과 인스턴스화를 동시에 할 수 있다. = 즉석에서 필요한 구현을 만들어서 사용할 수 있다.

```java
List<Product> blackProducts = filterProducts(products, new ProductPredicate(){
	public boolean test(Product product) {
		return BLACK.equals(product.getColor());
	}
});
```

사실 이렇게 써도 좋지만 문제점이 있다. 저 필터 로직이 복잡해지면 해당 함수를 알아보기가 힘들어진다. 개발자들은 익명 클래스에 익숙학지 않으며, 너무 장황하다. 나도 처음 정렬을 접했을 때 익명클래스로 사용하는 방법으로 외워서 사용했던 기억이 있다. 이걸 단순화 할 수 있다.

## 람다식로 더 단순화 시키기

```java
List<Product> blackProducts = filterProducts(products, ~~new ProductPredicate~~() **->** {
	~~public boolean test(Product product) {~~
		return BLACK.equals(product.getColor());
	~~}~~
});

---
// 단순화 버전1
List<Product> blackProducts = filterProducts(products, () **->** {
		return BLACK.equals(product.getColor());
});

// 단순화 버전2
List<Product> blackProducts = filterProducts(products, () **->** BLACK.equals(product.getColor());
```

익명 클래스를 단순화 하기 위해서는 가로줄을 지우고, 굵은 글씨는 추가하면 된다.

단순화 버전2는 더 축약한 버전이다. 이렇게 바꿈으로 써 현재 코드가 무엇을 하는지 한눈에 파악이 가능하다.

## 리스트 형식으로 추상화

```java
public interface Predictate<T> {
	boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> p) {
	List<T> result = new ArrayList<>();
	for(T e: list) {
		if(p.test(e)){
			result.add(e);
		}
	}
	return result;
}
```

이렇게 바꿈으로써 Product에만이 아니라 모든 데이터 타입에 대해서 적용이 가능해진다.
