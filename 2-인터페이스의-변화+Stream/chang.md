# 인터페이스 기본 메소드와 스태틱 메소드

공통적으로 제공해야하는 Interface에 제공해야하는 메소드가 생기는 경우가 생길 수 도 있다.

억지로 메소드를 추가하면, → compile 에러가 발생한다.

그런 경우에 그런것을 방지하기 위해서는... → default를 앞에 붙혀주면 함수 작성도 가능하게 된다. 

## 주의할점

기능이 재대로 동작할거라는 착각을 할 수 있음. (런타임 에러 발생 가능성이 있다.)→ 반드시 문서화하는것을 추천한다.⇒ @implSpec 을 통해서 문서화를 작성할 수 있다.

@Override도 가능하다.

## 제약사항

Object에서 제공하는 메소드들은 재정의 불가 (equals, hascode)같은 기능은 제공 불가.

본인이 수정할 수 있는 인터페이스에만 기본 메소드 수정 가능. (임의의 라이브러리 인터페이스를 고쳐쓸수는 없다)

다이아몬드 프라블럼

Foo 인터페이스에도 default Bar 인터페이스에서도  동일한 이름의  default메소드가 있을 경우 동시 implements 하는 경우 컴파일 에러가 발생한다. 

```java
public interface foo{
default a(){
}

}

public interface bar{
default a(){
}

}

public class A implements foo, bar{
 a() => //foo에서 사용해야하는지 bar에서 사용해야하는지 모름...
}
```

## static method

**해당 타입 관련한 헬퍼 또는 유틸리티 메소드를 제공**할때 인터페이스에 스태틱 메소드 제공 가능

# 자바 8 API의 기본 메소드와 스태틱 메소드

**iterable**의 기본 메소드

대부분 혼자서 사용하는 경우는 거의 없고 Collection에 포함되어서 사용한다. 

- forEach()
- spliterator() → 쪼갤수 있는 iterator
    - 함수를 .tryAdvance
    - .trySplit() → 내부의 것들을 반을 쪼개서 iterable하게 사용한다.
      
        **병렬 처리할 때 유용하다.**
        

**Collection**의 기본 메소드 

- Stream(), parrallStream()
- removeIf()

**Comparator**의 기본 메소드

- reversed()  ⇒  원래 `Comparator`가 가진 기준에서 반대로 적용하는 역할. (`Comparator`가 바뀜)
- thenComparing()
- static reverseOrder() / naturalOrder() ⇒ 특정 타입의 Comparable이 지정되어있으면 일단 그 기준으로 역순이든 자연순이든 세팅해서 정렬함.
- static nullIsFirst() / nullIsLast()
- static comparing()

 Java 8 이전의 개발 방식 

추상 메소드 class를 만들어 놓고... abc메소드를 비어놓게 만들어놓고

아래 실제 구현 클래스에서 각 메소드를 필요한것만 구현하라고 원하는건 빼서 쓰고, 원하지 않는건 안쓰는 방향으로 구현하기 위해서 구현하는 방향으로...

![스크린샷 2021-10-25 오후 11.18.11.png](JAVA%208%20a041b332498a4f078cd54a98b6ddb2ba/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2021-10-25_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_11.18.11.png)

java8 이후의 방식은

인터페이스에다가 default방식으로 구현하고 implements방식으로 구현하면 되고 상속은 강제되지 않음. (비침투성)

위의 방식으로 구현하면 상속을 강제하므로, 결국에는 다른 상속을 해야하는 경우에는 어려움.

```java
public class A extends B -> 이런 방식보다는 

public class A implements B,C,D => 더 많은 공통 코드를 포함해서 구현 가능. 필요한건 필요한만큼 쓰면되며..
```

ㄴ webmvcconfigurer라는 인터페이스가 존재하며, 원래는 webmvcconfigurerAdapter 의 추상클래스를 통해서 상속을 해서 많은 모든 메소드들을 오버라이딩 해야하는 귀찮은 작업이 생겼을 것...

# Stream 소개

Stream은 데이터를 담고 있는 저장소가 아님. → 즉 스트림은 소스(input)를 변경하지 않는다. 

Collection에서 → 스트림을 통해서 값이 변경되어도 원 데이터는 변경되지 않는다.

무제한일수도? Short Circuit(limit, skip)메소드를  사용해서 제한 가능.

중계 / 종료 함수로 쪼개서 관리할 수 있는데, 중계 함수는 근본적으로 Lazy방식으로 구현되어있으며 중계는 Stream을 리턴하고, 종료는 Stream을 리턴하지 않는다. 

> Lazy란? → 종료 함수가 오기전까지는 그안의 실행을 안시킴. 
중계-중계-중계-....이렇게 해도 종료함수가 오지 않으면  실행시키지 않는다는 뜻.
> 

손쉬운 병렬처리가 가능해진다.  ⇒ parallelStream()을 이용하면 병렬처리해줌. 근데 병렬처리가 막상 겁.나. 빠릅니다. 는 아님ㅋㅋ;  (진짜 데이터가 정말정말많은 경우에만 사용해서 사용하기... 혹은 직접 성능 측정을 해서 확인해보는게 좋음.)

스트림 파이프라인 

중계 0/다수 종료는 단 한개만.  종료가 없으면 중계는 실행되지 않는다.

## 중개 오퍼레이션

- **Stream을 리턴한다.**
- Stateless / Stateful 오퍼레이션으로 더 상세하게 구분할 수도 있다. (대부분은 Stateless지만 distinct나 sorted 처럼 이전 이전 소스 데이터를 참조해야 하는 오퍼레이션은 Stateful 오퍼레이션이다.)
- filter, map, limit, skip, sorted, ...

중개 오퍼레이션에서 sorted의 경우 Collections의 reverseOrder() / naturalOrder()와 함께 사용하면, 훨씬 편하게 sorting 할 수 있다. 

## 종료 오퍼레이션

- **Stream을 리턴하지 않는다.**
- collect, allMatch, count, forEach, min, max, ..

Stream은 재사용은 자제하자. → 어차피 이거 Stream은 저장소 개념이 아니라서, 한번 종료되면 완전히 사용성을 잃어버리는 구조로 구현되어있음.

Stream의 동작순서는 쓴 순서대로 사용된다. → 그래서 순서사용시 주의해야하는데, map과 같이 변환 즉, 값의 타입이 변화하는 경우 내가 원하는 방식대로 작동하지 않을수 있다. 

```java
Stream.of("a", "b", "c", "d", "e").map(s -> { 
	System.out.println("map: " + s); 
	return s.toUpperCase(); 
}).filter(s -> {
 System.out.println("filter: " + s);
 return s.startsWith("A"); }
).forEach(s -> System.out.println("forEach: " + s));

//맵 만들고, 필터링한다. 
//맵으로 만들어 낸뒤 => 필터링을 5번 돌고 -> forEach로 출력 10번정도 동작한다.

Stream.of("a", "b", "c", "d", "e").filter(s -> {
	System.out.println("filter: " + s); 
	return s.startsWith("a"); 
}).map(s -> { 
	System.out.println("map: " + s);
	return s.toUpperCase(); 
}).forEach(s -> System.out.println("forEach: " + s));

//필터링한 후에 필터링된 것들을 바탕으로 Map을 한다. -> 6번정도 동작한다.
//필터 통과? true -> 맵 -> forEach순으로
```

## 기본형 스트림

- IntStream
- LongStream
- DoubleStream

제네릭을 사용하지 않기 때문에 불필요한 오토박싱(*auto-boxing*)이 일어나지 않습니다. 필요한 경우 `boxed` 메소드를 이용해서 박싱(*boxing*)할 수 있습니다.

# Stream API

Stream은 input type을 강제하면  그안에서는 계속 그타입이 계속 유지되어진다. 

`map()` 함수 ⇒ 들어오는 type 과 나오는 type을 변경될 수 있음! 

list<list<class>> 의 경우 Map을 통해서 구현하다보면, stream으로 만들어버리면 list<class>타입이 들어가서 class 타입의 형식으로 사용할 수 없게 된다. → 이런 경우 String 타입으로 만들어서 

`flatMap()` ⇒ 이런 list타입을 쪼개서 class로 분리시켜주는 기능. 물론 내가 넣는 함수에 따라서 변경 가능!

![Untitled](JAVA%208%20a041b332498a4f078cd54a98b6ddb2ba/Untitled.png)

flatMap의 동작 방식은 결국 → String[] 배열을 ⇒ Stream<String>으로 변환해서 하나의 형태로 관리할 수 있도록 만들어버린다. 

`generate(Supplier) 또는 Iterate(T seed, UnaryOperator)` ⇒ 무제한 스트림. 계속 반복시키게 하는 api  `skip()`, `limit()`와 같은 제한조건을 둬야 좋다...

`anyMatch()`, `allMatch()`, `nonMatch()` ⇒ 실제로 일치하는는 조건을 걸어주면, 종료하는 함수임.

`filter()` ⇒ 한번 걸러주는 API 

`Sorted()`  ⇒ 정렬하는 API 
`Stream<T>sorted(Comparator<?super T> comparator)`  파라미터 없이 일반적으로 사용할수도 있으나, Comparator가 있어서, lambda식을 통해서 특정 조건에 맞춰서 sorting에 대해서 지정하여 사용할 수 있다.

`reduce(identity, BiFunction)`, `collect()`, `sum()`, `max()` ⇒ 스트림으로 데이터 뭉치기 

collect는 대부분 Collectors와 함께 많이 사용 되는데 toList(), toSet(), toMap()과 같은 Collection의 형태로 묶이는 경우가 많아 다수의 데이터를 묶는데 이용하기 상당히 좋다.

### 그 외에 사용했었던 Stream

`mapToInt()`, `mapToLong()`, `mapToDouble()` ⇒ Stream이었던 것을 →  IntStream과 같은 것으로 변경

mapToInt() 형을 통해서 List<Integer>형식의 값을  int[] → 배열로 만들기쉽다. 

```java
ArrayList<Integer> list; // list형식 그러니까 collection타입은 Integer와 같은 Wrapper type
list.stream().mapToInt(Integer::intvalue).toArray();
//으로 int[]배열로 쉽게 변환할 수 있다. 
```

mapToObject → IntStream이었던 것을 → Stream<>형으로 변환할 수도 있다.

[Java 스트림 Stream (1) 총정리](https://futurecreator.github.io/2018/08/26/java-8-streams/)

[[Java] Stream API의 활용 및 사용법 - 고급 (4/5)](https://mangkyu.tistory.com/115)