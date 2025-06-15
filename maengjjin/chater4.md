### 스트림

거의 모든 자바 애플리케이션은 컬렉션을 만들고 처리하는 과정을 포함한다. 컬렉션으로 데이터를 그룹화하고 처리할 수 있다

- 스트림을 사용하면 컬렉션 데이터를 임시 구현 없이 질의 처럼 선언적으로 처리할 수 있다
- 스트림을 사용하여 멀티스레드 코드를 구현하지 않아도 데이터를 투명하게 병렬로 처리할 수 있다

```java

List<Dish> lowCaloricDishes = new ArrayList<>();
for (Dish d : menu) {
    if (d.getCalories() < 400) {
    lowCaloricDishes.add(d);
    }
}

Collections.sort(lowCaloricDishes, new Comparator<Dish>() {
    public int compare(Dish d1, Dish d2) {
        return Integer.compare(d1.getCalories(), d2.getCalories());
    }
});

List<String> lowCaloricDishesName = new ArrayList<>();
for (Dish d : lowCaloricDishes) {
    lowCaloricDishesName.add(d.getName());
}

```

- 자바7에서는 lowCaloricDishes 라는 중간결과를 저장하는 임시 컨테이너 역할을 하는 중간 변수이다
- 필터링, 정렬, 매핑 등 여러 작업을 각각 명령형 코드로 작성한다
- for 루프와 Collections.sort()로 세부 구현 직접 처리한다

```java
// 자바8
List<String> lowCaloricDishes = menu.stream()
    .filter(d -> d.getCalories() < 400)
    .sorted(comparing(Dish::getCalories))
    .map(Dish::getName)
    .collect(toList());


//  병렬스트림
List<String> lowCaloricDishes = menu.parallelStream()
    .filter(d -> d.getCalories() < 400)
    .sorted(comparing(Dish::getCalories))
    .map(Dish::getName)
    .collect(toList());
```

- stream()을 parallelStream()으로 바꾸면 이 코드를 멀티코어 아키텍처에서 병렬로 실행할 수 있다

##### 스트림의 새로운 기능

- 스트림은 선언형으로 코드를 구현할 수 있다. 즉, if와 루프 등 제어 블록을 사용해서 동작을 직접 지정하지 않아도 된다.
- ‘저칼로리의 요리만 선택하라’ 같은 동작을 람다 표현식으로 간결하게 지정할 수 있다.
- filter, sorted, map, collect 같은 여러 고수준 빌딩 블록 연산을 연결해 복잡한 데이터 처리 파이프라인을 만들 수 있다.
- 연산들을 파이프라인으로 연결하면 가독성과 명확성이 높아지고, 특정 스레딩 모델에 제한되지 않으며 유연하게 사용할 수 있다.

```plaintext
menu -> filter(람다) -> sorted(람다) -> map(람다) -> collect
```

```java
Map<Dish.Type, List<Dish>> dishesByType = menu.stream()
                                              .collect(groupingBy(Dish::getType));
```

- Map 내부의 형식에 따라 요리를 그룹화 한다 Map은 다음과 같은 결과를 포함할 수 있다

```java
{
 FISH = [prawns, salmon],
 OTHER = [french, fries, rice, season],
 MEAT = [pork, beef, chickecn]
}
```

##### 스트림API 특징

- 선언형: 더 간결하고 가독성이 좋아진다
- 조립할 수 있음: 유연성이 좋아진다
- 병렬화: 성능이 좋아진다

### 스트림 시작하기

- 스트림이란 데이터 처리 연산을 지원하도록 소스에서 추출된 연속된 요소로 정의할 수 있다.
- **연속된 요소**

  - 컬렉션과 마찬가지로 스트림은 특정 요소 형식으로 이루어진 연속된 값 집합의 인터페이스를 제공한다
  - filter, sorted, map 처럼 표현 계산식이 주를 이룬다
- **소스**

  - 스트림은 컬렉션, 배열, i/o 자원 등의 데이터를 제공 소스로부터 데이터를 소비한다
  - 정렬된 컬렉션으로 스트림을 생성하면 정렬이 그대로 유지된다
- **데이터 처리 연산**

  - 함수형 프로그래밍 언어에서 일반적으로 지원하는 연산과 데이터베이스와 비슷한 연산을 지원한다
  - 스트림 연산은 순차적으로 또는 병렬로 실행할 수 있다

##### 스트림 특징

- 파이프라이닝
  - 스트림 연산끼리 연결해서 커다란 파이프 라인을 구성할 수 있도록 스트림 자신을 반환한다
  - 연산 파이프라인은 데이터 소스에 적용하는 데이터베이스 질의와 비슷한다
- 내부반복
  - 반복자를 이용해서 명시적으로 반복하는 컬렉션과 달리 스트림은 내부 반복을 지원한다

```java
List<String>  threeHighCaloricDishNames = menu.stream()
                                            .filter(d -> d.getCalories() > 300) // 파이프라인 연산 만들기, 고칼로리 요리 필터
                                            .map(Dish::getName) // 요리명 추출
                                            .limit(3) //선착순 3개만
                                            .collect(toList()); // 결과를 다른 리스트로 저장
```

- 요리 리스트를 포함하는 menu에 stream 메서드를 호출해서 스트림을 얻었다
- 데이터소스는 요리 리스트(메뉴)다. 데이터 소스는 연속된 요소(컬렉션, 배열 등 과 같이 여러개의 요소값을 포함한 데이터 구조)를 스트림에 제공한다
- filter, map, limit, collect로 이어지는 일련의 데이터 처리 연산을 적용한다
- collect 연산으로 파이프라인을 처리해서 결과를 반환한다(list 반환)
- filter, map 등의 중간 연산은 collect 같은 최종 연산이 호출되기 전까지 실제 데이터를 처리하지 않는다.

  - 스트림은 "어떻게 처리할지 계획만 세우는" 상태이며, collect를 만나야 비로소 실행된다.
- filter: 람다를 인수로 받아 스트림에서 특정 요소를 제외 시킨다. 예제에서는 d -> d.getCalories() > 300 라는  람다를 전달해서 300칼로리 이상의 요리를 선택한다
- map: 람다를 이용해서 한 요소를 다른 요소로 변환하거나 정보를 추출한다. 예제에서는 메서드참조 Dish::getName(람다 표현식 d -> d.getName())을 전달해서 각 요리명을 추출한다
- limit: 정해진 개수 이상의 요소가 스트림에 처리되지 못하게 스트림 크기를 축소한다
- collect: 스트림을 다른 형식으로 변환한다. 예제에서는 스트림을 리스트로 변환했다. toList()는 스트림을 리스트로 변환하라고 지시하는 인수다

### 스트림과 컬렉션

- 데이터를 언제 계산하는지가 컬렉션과 스트림의 가장 큰 차이다.
- 컬렉션은 메모리에 모든 요소를 저장하고, 요소가 컬렉션에 추가되기 전에 계산되어야 한다.
- 반면 스트림은 요청할 때만 요소를 계산하는 지연된 계산 모델이며, 생산자와 소비자 관계로 동작한다.
- 사용자가 데이터를 요청할 때만 실제로 값을 계산한다. 컬렉션은 적극적으로 값을 생성하지만, 스트림은 필요한 시점에만 계산한다.

### 외부반복과 내부 반복

- 컬렉션 인터페이스를 사용하려면 사용자가 for-each등을 사용해서 직접 요소를 반복해야 된다. 이를 외부반복이라 한다
- 스트림 라이브러리는 반복을 알아서 처리하고 결과를 스트림값을 어딘가에 저장해주는 내부반복을 사용한다

```java
// 컬렉션 for-each루프를 이용하는 외부 반복
List<String> neams = new ArrayList<>();
for(Dish dish: menu){
 names.add(dish.getName());
}


// 컬랙션 내부적으로 숨겨졌던 반복자를 사용한 외부 반복
Liststring neams = new ArrayList<>();
Iterator(String) iterator = menu.iterator();
while(iterator.hasNext()){
 Dish dish = iterator.next();
 names.add(dish.getName());
}

// 스트림 내부반복
List<String> names = menu.stream()
                         .map(Dish::getName) // map메서드를 getName 메서드로 파라미터화해서 추출
                         .collect(toList());
```

- 스트림은 내부 반복을 사용하므로 반복 과정을 개발자가 직접 제어할 필요가 없다.
- 내부 반복을 사용하면 병렬 처리를 더 쉽게 적용할 수 있고, 다양한 최적화 및 처리 순서를 자동으로 활용할 수 있다.
- 하지만 filter나 map 같은 내부 반복을 수행하는 연산은 미리 정의되어 있어야 하며, 그 연산들을 조합해 데이터를 처리해야 한다.
- 외부 반복(for-each 등)을 사용하는 경우 병렬성을 직접 관리 해야 된다
- 스트림의 내부 반복은 filter나 map같은 반복을 숨겨주는 연산 리스트가 미리 정의 되어야 한다



### 스트림 연산

```java
List<String>  threeHighCaloricDishNames = menu.stream() // 요리 리스트에서 스트림 얻기
    .filter(d -> d.getCalories() > 300) // 중간연산
    .map(Dish::getName) // 중간연산
    .limit(3) // 중간연산
    .collect(toList()); // 최종연산(스트림을 리스트로 변환)
```

- filter, map, limit는 서로 연결되어 파이프라인을 형성한다
- collect로 파이프라인을 실행한 다음에 닫는다
- 스트림연산을 중간연산이라고 하며 스트림을 닫는 연산을 최종 연산이라고 한다


### 중간연산

- filter나 sorted 같은 중간 연산은 다른 스트림을 반환한다. 따라서 여러 중간 연산을 연결해서 질의를 만들 수 있다.
- 중간 연산의 특징은 최종 연산(단말 연산)이 실행되기 전까지 아무 연산도 수행하지 않는다는 점이다. 즉, lazy하다.
- 중간 연산은 각각 바로 실행되는 것이 아니라, 모두 하나로 합쳐진 후 최종 연산이 실행될 때 한 번에 처리된다.

```java
List<String> names = menu.stream()
                         .filter(dish -> {
                                           System.out.println("filtering: " + dish.getName());
                                           return dish.getCalories() > 300;})
                         .map(dish -> {
                                           System.out.println("mapping: " + dish.getName());
                                           return dish.getCalories() > 300;})
                         .limit(3)
                         .collect(toList());
System.out.println(names);
```

```plaintext
filtering:pork
mapping:pork
filtering:beef
mapping:beef
filtering:chicken
mapping:chicken
[pork, beef, chicken]
```

- 300칼로리가 넘는 요리는 여러 개지만, limit 연산 덕분에 처음 3개만 선택되었다.
- 이는 쇼트서킷 기법 덕분이며, filter와 map은 서로 다른 연산이지만 내부적으로 하나의 반복 과정으로 병합되어 처리되었다. 루프 퓨전이라고 한다


### 최종연산

- 최종 연산은 스트림 파이프라인의 처리 결과를 도출하는 연산이다.
- 대부분의 최종 연산은 스트림이 아닌 결과(List, Integer, void 등)를 반환한다.  java

```java
//  요리를 출력하는 최종 연산을 수행
menu.stream().forEach(System.out::println)
```


### 스트림 이용하기

- 질의를 수행할 데이터
- 스트림 파이프라인을 구성할 중간 연산 연결
- 스트림 파이프라인을 실행하고 결과를 만들 최종연산

##### 최종 연산


| 연산      | 연산 종류 | 반환 형식       | 목적 및 설명                                                     |
| --------- | --------- | --------------- | ---------------------------------------------------------------- |
| `forEach` | 최종 연산 | `void`          | 스트림의 각 요소를 소비하며 지정한 동작을 수행함                 |
| `count`   | 최종 연산 | `long`(generic) | 스트림의 요소 개수를 반환함                                      |
| `collect` | 최종 연산 |                 | 스트림의 요소를 리듀싱해서 리스트, 맵, 다른 형식의 결과를 생성함 |

### 정리


- 스트림 소스에서 추출된 연속 요소로, 데이터 처리 연산을 지원한다
- 스트림은 내부반복을 지원한다. 내부 반복은 filter, map, sprted 등의 연산으로 반복을 추상화한다
- 스트림에는 중간 연산과 최종연산이 있다
- 중간 연산은 filter와 map처럼 스틞을 반환하면서 다른 연산과 연결되는 연산이다
- 중간 연산을 이용해서 파이프라인을 구성할 수 있지만 중간 연산으로는 어떤 결과도 생성 할 수 없다
- forEach나 count처럼 스트림 파이프라인을 처리해서 스트림이 아닌 결과를 반환하는 연산을 최종 연산이라고 한다
- 스트림의 요소는 요청할 때 게으르게 계산된다
