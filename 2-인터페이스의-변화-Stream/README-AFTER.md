> 정리 : [영준](https://github.com/jun108059)

**📚목차**
- [Stream](#stream)
    - [1. Stream의 특징](#1-stream의-특징)
        - [1-1. 컬렉션이 아니다.](#1-1-컬렉션이-아니다)
        - [1-2. Functional in nature.](#1-2-functional-in-nature)
        - [1-3. 재사용 불가능](#1-3-재사용-불가능)
        - [1-4. 무제한 데이터 가능](#1-4-무제한-데이터-가능)
        - [1-5. 지연연산(Lazy)](#1-5-지연연산lazy)
        - [1-6. 병렬 처리](#1-6-병렬-처리)
    - [2. 스트림 파이프라인](#2-스트림-파이프라인)
        - [2-1. Stream 인스턴스 생성](#2-1-stream-인스턴스-생성)
        - [2-2. 중개 오퍼레이션](#2-2-중개-오퍼레이션)
        - [2-3. 종료 오퍼레이션](#2-3-종료-오퍼레이션)
        - [2-4. 파이프라인 예시](#2-4-파이프라인-예시)
    - [3. Stream API 정리](#3-stream-api-정리)

# Stream

> 연속된 데이터를 처리하는 오퍼레이션들의 모음

<img src = "https://user-images.githubusercontent.com/42997924/140324289-8529f61a-5c59-42aa-8075-2a92412b2960.gif" width="50%" height="50%">

🔼 [이미지 출처](https://weibo.com/2856003072/JnaSbeEnJ?type=comment)

- `stream` 은 `컨베이어 벨트`와 비슷하다.
- 컨베이어 벨트에 떡조각(데이터)들을 흘려보내면서 반죽을 하고, 앙금을 쌓고(map), 불량품은 빼고(filter) 포장을 해서(collect) 내보낸다.

## 1. Stream의 특징

### 1-1. 컬렉션이 아니다.

- 데이터를 담고 있는 저장소가 아니다.(컬렉션이 아니라는 의미) 
- 확장해서 재사용이 불가능한데, 아래에서 자세히 살펴보자.

### 1-2. Functional in nature.

- stream은 처리하는 데이터 소스를 변경하지 않는다.
- 즉 내가 `A`라는 데이터를 수정한다고 해서 원본데이터가 수정되는 것은 아니라는 의미.

```java
public static void main(String[] args) {
    List<String> names = new ArrayList<>();
    names.add("yj");

    Stream<String> stringStream = names
        .stream()
        .map(String::toUpperCase);
    
    names.forEach(System.out::println);
}

/*
[실행 결과]
yj
*/
```

### 1-3. 재사용 불가능

> 스트림으로 처리하는 데이터는 오직 한 번만 처리한다.
    - 컨베이어 벨트에서 데이터들이 한 번 지나간뒤 다시 돌아오지 않음.

스트림은 종료연산을 하지 않는 이상 그전까지가 하나의 인스턴스로써 계속 사용이 가능하지만 종료연산을 수행하면 재사용이 불가하게 되는데 이는 `스트림`은 저장된 데이터를 꺼내서 처리하는 용도이지 데이터를 저장하는 목적이 아니기 때문이다.

```java
public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1,2,4,51,2,3);

       Stream<Integer> stream = list.stream().map(i -> i+1);

       stream.count();
       stream.forEach(System.out::println);     //Exception 발생 : java.lang.IllegalStateException: stream has already been operated upon or closed
}
```
Stream은 AutoCloseable을 상속받은 BaseStream을 상속받아 close()메서드를 가지고 있어 종료연산을 사용하게 되면 우리가 명시하지 않아도 내부적으로 close()를 시켜주기 때문에 재사용이 불가능하다.

```java
L1
    LINENUMBER 13 L1
    ALOAD 1
    INVOKEINTERFACE java/util/List.stream ()Ljava/util/stream/Stream; (itf)
    INVOKEDYNAMIC apply()Ljava/util/function/Function; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/LambdaMetafactory.metafactory(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
      // arguments:
      (Ljava/lang/Object;)Ljava/lang/Object;, 
      // handle kind 0x6 : INVOKESTATIC
      ex/Ex.lambda$main$0(Ljava/lang/Integer;)Ljava/lang/Integer;, 
      (Ljava/lang/Integer;)Ljava/lang/Integer;
    ]
    INVOKEINTERFACE java/util/stream/Stream.map (Ljava/util/function/Function;)Ljava/util/stream/Stream; (itf)
    ASTORE 2
   L2
    LINENUMBER 15 L2
    ALOAD 2
    INVOKEINTERFACE java/util/stream/Stream.count ()J (itf)
    POP2
   L3
    LINENUMBER 16 L3
    ALOAD 2
    INVOKEDYNAMIC compare()Ljava/util/Comparator; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/LambdaMetafactory.metafactory(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
      // arguments:
      (Ljava/lang/Object;Ljava/lang/Object;)I, 
      // handle kind 0x5 : INVOKEVIRTUAL
      java/lang/Integer.compareTo(Ljava/lang/Integer;)I, 
      (Ljava/lang/Integer;Ljava/lang/Integer;)I
    ]
    INVOKEINTERFACE java/util/stream/Stream.max (Ljava/util/Comparator;)Ljava/util/Optional; (itf)
    POP
```
앞서 작성한 코드의 바이트코드 일부분인데, 앞서 작성한 Stream을 참조영역 2에 저장하고, count()메서드에서 이를 load하고 pop하는 것을 볼수 있다. 그런데 max()메서드에서 이 pop한 참조영역 2를 다시 load하려고 하니 런타임에 에러가 발생하는 것이다.


### 1-4. 무제한 데이터 가능

- 무제한의 데이터일 수 있다.
- 이 경우 Short Circuit(limit, skip) 메소드를 사용해서 제한 가능

### 1-5. 지연연산(Lazy)

> 중개 오퍼레이션은 근본적으로 `lazy`하다.

    - 여러 중개 오퍼레이션들을 메소드 체이닝을 하더라도 그 시점에서 코드가 수행되지는 않음.
    - 모든 중개 오퍼레이션의 실행 시기는 종료 오퍼레이션의 호출시점!
    - 따라서 중개 오퍼레이션의 반환타입은 또 다른 stream
    
```java
public static void main(String[] args) {
    List<String> names = new ArrayList<>();
    names.add("yj");
    names.add("youngjun");
    names.add("spring");
    names.add("java8");

    Stream<String> stringStream = names.stream()
        .map(s -> {
            System.out.println(s);
            return s.toUpperCase();
        });
    
    names.forEach(System.out::println);
}
```

- `names.stream().map(s->{ ... });`
    - stream의 중개오퍼레이터를 사용하는 순간에는 코드가 수행되지 않는다.
    - 그렇기 때문에 중개오퍼레이션 map 안에 있는 출력문이 수행되지 않는다.
    - 수행시키기 위해서는 스트림 파이프라인을 정의해야 한다.

#### 1-5-1. 지연연산을 통한 성능 향상

내용은 지난주 정리했던 내용중 람다의 지연연산과 동일한 내용이다.

```java
List<Integer> list = Arrays.asList(1,2,4,51,2,3);

long result1 = list.stream().map(i -> {
    System.out.println(i);
    return i+1;
}).skip(3).count();
System.out.println("result 1 : " + result1);

long result2 = list.stream().skip(3).map(i -> {
    System.out.println(i);
    return i+1;
}).count();
System.out.println("result 1 : " + result2);

//print
1
2
4
51
2
3
result 1 : 3

51
2
3
result 1 : 3
```
추가적으로 중간연산의 순서에 따라 위처럼 불필요한 연산을 막아 성능향상을 꾀할 수 있기때문에 중간연산의 순서도 신경을 써주면 좋다.

<br>

#### 1-5-2. 지연처리 가능

```java
public static void main(String[] args) {
    List<Integer> list = Arrays.asList(1,2,4,51,2,3);
    Stream<Integer> stream = list.stream().peek(System.out::println);

}

//bytecode
L1
    LINENUMBER 12 L1
    ALOAD 1
    INVOKEINTERFACE java/util/List.stream ()Ljava/util/stream/Stream; (itf)
    GETSTATIC java/lang/System.out : Ljava/io/PrintStream;
    DUP
    INVOKESTATIC java/util/Objects.requireNonNull (Ljava/lang/Object;)Ljava/lang/Object;
    POP
    INVOKEDYNAMIC accept(Ljava/io/PrintStream;)Ljava/util/function/Consumer; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/LambdaMetafactory.metafactory(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
      // arguments:
      (Ljava/lang/Object;)V, 
      // handle kind 0x5 : INVOKEVIRTUAL
      java/io/PrintStream.println(Ljava/lang/Object;)V, 
      (Ljava/lang/Integer;)V
    ]
    INVOKEINTERFACE java/util/stream/Stream.peek (Ljava/util/function/Consumer;)Ljava/util/stream/Stream; (itf)
    ASTORE 2
```

위를 돌려보면 print가 되지 않은 것을 볼 수 있는데 Stream은 종료연산이 수행될때까지 중간연산은 시작하지 않고 하나의 Stream형태로 저장이 되어 미리 Stream을 선언해두고 나중에 결과를 확인할 수 있다.

byte코드를 보면 Strema형태를 참조타입으로 저장하는 것을 볼 수 있다 .(ASTORE)

### 1-6. 병렬 처리

> 손쉽게 병렬 처리를 할 수 있도록 parallelStream() 제공

- forEach를 통해 단순한 반복 출력이 아닌 로직(조건문)이 추가될수록 stream을 통해 구현할수록 간결해지기 때문에 stream을 쓰는 것 외에도 기본적인 for문이나 for-of문으로는 로직들을 병렬적으로 처리하는게 쉽지 않다.
- 하지만, parallelStream()을 이용하면 손쉽게 병렬처리를 할 수 있다.

```java
List<String> names = Arrays.asList("yjyjyj", "youngjun", "spring", "java8");
List<String> collect = names
        .parallelStream()
        .map(s -> {
            System.out.println(s + " : " + Thread.currentThread().getName());
            return s.toUpperCase();
        })
        .collect(Collectors.toList());

collect.forEach(System.out::println);

/* 실행 결과
yjyjyj : ForkJoinPool.commonPool-worker-23
spring : main
java8 : ForkJoinPool.commonPool-worker-5
youngjun : ForkJoinPool.commonPool-worker-19
YJYJYJ
YOUNGJUN
SPRING
JAVA8
*/
```

> 자바는 ForkJoinPool 라이브러리를 이용해서 병렬처리를 한다.

**ForkJoinPool?** 

- Fork(Task를 잘게 쪼개는 것)를 수행하고 해당 Pool에 있는 스레드들이 task를 처리하고 join을 통해 합친다. 
- 이때 ForkJoinPool에는 task가 존재하는 queue가 있고, 스레드들이 이 queue에서 task를 할당받아 자신의 queue에 적재해가며 처리하는데 놀고있는 스레드가 존재한다면 다른 스레드의 queue에서 task를 steal하는 방법으로 동작하는 Work-Stealing메커니즘을 이용하기 때문에 task가 하위 task를 생성하거나 small task가 많을 경우 효과적이다.

> sequential() 을 이용하면 다시 시퀀셜 스트림으로 변경도 가능하다.

```java
List<String> names = Arrays.asList("yjyjyj", "youngjun", "spring", "java8");
List<String> collect = names
        .parallelStream()
        .sequential()
        .map(s -> {
            System.out.println(s + " : " + Thread.currentThread().getName());
            return s.toUpperCase();
        })
        .collect(Collectors.toList());

collect.forEach(System.out::println);

/* 실행 결과
yjyjyj : main
youngjun : main
spring : main
java8 : main
YJYJYJ
YOUNGJUN
SPRING
JAVA8
 */
```

#### 1-6-1. Pool Size 조절방법

1. property
```java
 public static void main(String[] args) {
        System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism","4");
        List<Integer> list = Arrays.asList(1,2,4,51,2,3);
        list.parallelStream().forEach(i->System.out.println(i+ " : " + Thread.currentThread().getName()));

    }
```

2. ForkJoinPool 객체 이용
```java
 public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1,2,4,51,2,3);
         ForkJoinPool pool = new ForkJoinPool(2);
         pool.submit(() -> list.parallelStream().sequential().forEach(i->System.out.println(i+ " : " + Thread.currentThread().getName()))).get();
    }
```
ForkJoinPool생성자 매개변수로 size를 전달하고 submit()메서드에 람다로 작업할 내용을 정의해주고 get()을 통해 terminate()해주어야 실행이 된다.

3. default Pool size 변경
```
-Djava.util.concurrent.ForkJoinPool.common.parallelism=16
```
build시에 위 옵션으로 기본 풀 사이즈를 변경이 가능하다.

#### 1-6-2. 단점

위에서 설명한것과 같이 ForkJoinPool 특성상 task를 분할할때(fork) 균등하게 분배를하게 되는데 이때 이 분할하는 작업의 비용이 높게 되면 오히려 성능이 안좋을 수 있다. 예를 들어, 이미 사이즈를 알고 있는 ArrayList같은 경우는 분할하는 작업이 빠를 수 있지만 사이즈를 모르는 LinkedList같은 경우에는 순차작업과 같거나 더 느릴 수가 있다.

병렬로 처리되는 작업이 `독립적`이지 않다면 이도 오히려 성능에 안좋을 수 있다. 예를 들어 sorted()/distinct()와 같은 작업을 수행할때는 내부의 상태에 대한 변수를 작업이 `공유`해야지만 가능한 작업이므로 `lock`의 작업이 추가로 발생하고 스레드가 대기하는 상황이 생기기 때문에 순차적으로 실행하는 경우가 더 효과적일 수 있다.

> 정리하자면,  
> 사실상 멀티 스레드를 이용한다고 성능이 무조건 좋은 것은 아니며 오히려 느려질 가능성이 더 높다.  
> 대부분의 경우 그냥 `stream`을 쓰는게 나으며 정말 큰데이터를 다룰 때 성능 테스트 후 `parallelStream()`을 사용하면 된다.


## 2. 스트림 파이프라인

> 스트림이라는 컨베이어 벨트에 0개 혹은 다수의 중개 오퍼레이터(intermediate operation)과 한개의 종료 오퍼레이션(terminal operation)으로 구성.  
> 이 스트림은 반드시 하나의 종료 오퍼레이션이 있어야 하며, 만약 종료 오퍼레이션이 없다면, 스트림은 존재하지만 코드 수행은 되지 않는다.

스트림의 데이터 소스는 오직 터미널 오퍼레이션을 실행할 때에만 처리한다!

### 2-1. Stream 인스턴스 생성

> 스트림 인스턴스 생성하는 메서드

- `Arrays.stream(배열타입 변수)` : 배열을 Stream 타입 객체로 생성
- `collection.stream()` : Collection타입의 객체(Collection, List, Set)를 Stream 타입 객체로 생성
- `Stream.empty()` : 비어있는 Stream() 객체
- `Stream.builder().add()...build()`  : 빌더를 이용한 Stream 생성
- `Stream.generate(() -> {}).limit()` : 람다를 이용하여 생성하는 방식으로 람다의 return값이 들어있는 Stream을 생성한다. 이때 무한히 생성하기 때문에 limit메서드로 제한을 주어야 한다.
- `Stream.iterate(startValue, () -> {}).limit()` : 초기값부터 해당 값을 input으로 하는 람다를 반복하여 나온 값으로 Stream을 생성
- `IntStream.reange(1,5).boxed()` : IntStream을 생성후 boxed()메서드를 통해 Stream 인스턴스로 반환
- `Random().ints().boxed()` : 랜덤한 int값을 이용해 Stream 생성
- `"String".chars()` : IntStream을 반환
    - char들은 ascii 값으로 하나의 숫자로 표현할 수 있기 때문에 IntStream을 반환한다.
- `list.parallelStream()` : 병렬 스트림

### 2-2. 중개 오퍼레이션

> Stream을 리턴한다.  
> 필터링, 맵핑과 같이 어떠한 결과를 만들어가는 중간 작업들의 메서드 ( Intermediate operations )

- Stateless/Stateful 오퍼레이션으로 더 상세하게 구분할 수도 있다.
- 대부분 Stateless지만 distinct나 sorted처럼 이전 소스 데이터를 참조해야 하는 오퍼레이션은 Stateful 오퍼레이션.
- `filter, map, limit, skip, sorted, ...`

#### 2-2-1. 메서드 

- `filter(Predicate)` : Predicate의 반환값이 true인 데이터만 선택
- `distinct()` : 중복된 데이터 제거
- `map(Function<T,R>)` : 데이터들을 가공하여 다른 값으로 변경하고자 할때 사용
- `flatMap(Function<T, Stream>)` : 중첩구조를 제거하고 단일 컬렉션으로 만들어 map연산을 수행(flattening)
- `sorted(Comparator)` : Comparator을 이용해 데이터 정렬
- `peek(Consumer)` : 데이터를 변경하지 않고 그저 순환하는 메서드로 중간에 결과 확인할

#### 2-2-2. flatMap 활용 예시

```java
public static void main(String[] args) {
    List<String> list = Arrays.asList("Hello", "World");
    List<String[]> result = list.stream().map(s -> s.split("")).distinct().collect(Collectors.toList());

    for (String[] strings : result) {
        System.out.println(Arrays.toString(strings));
    }
}

//print
[H, e, l, l, o]
[W, o, r, l, d]
```
만일 문자열이 들어있는 배열에서 모든 문자열에 대해 사용된 알파벳들만 찾아내는 함수를 구성한다고 할때 map()을 이용하면 위와 같이 결과가 나오게 된다.

이는 알파벳을 하나하나 조회하기 위해 String을 split()을 이용하여 String배열로 한번더 나누는 과정에서 자료구조의 depth가 한단계 증가하여 `Hello World`라는 단어에서 distinct가 수행된게 아니라 `Hello`, `World` 따로 수행되었기 때문이다. 바로 이럴때 flatMap을 이용하면 한단계 중첩구조를 벗겨낼 수 있다.

```java
public static void main(String[] args) {
    List<String> list = Arrays.asList("Hello", "World");
    List<String> result = list.stream().map(s -> s.split("")).flatMap(Arrays::stream).distinct().collect(Collectors.toList());

    for (String strings : result) {
        System.out.print(strings);
    }
}

//print
HeloWrd
```

### 2-3. 종료 오퍼레이션

> Stream을 리턴하지 않는다!  
> 최종적으로 결과를 만들고 반환함으로써 chaining을 끝내는 메서드 ( terminal operations )

- `collect, allmatch, count, forEach, min, max...`

Stream은 AutoCloseable을 상속받은 BaseStream을 상속받아 close()메서드를 가지고 있어 종료연산을 사용하게 되면 우리가 명시하지 않아도 내부적으로 close()를 시켜주기 때문에 재사용이 불가능하다.

- 단순 계산
    - count() : 총 갯수
    - sum() : 합
    - min() : 최소값
    - max() : 최대값
    - average() : 평균값
- reduce() : 정의한 로직을 계산 후 결과 반환
    - reduce(BinaryOperator) : 각 요소를 처리하는 로직
    - reduce(T,BinaryOperator) : 계산을 위한 초기값 T를 가지고 각 요소를 처리
- collect : 특정 데이터형태, 자료구조로 바꾸어주는 함수
- matching
    - anyMatch(Predicate) : 하나라도 조건을 만족하는 요소가 있으면 true
    - allMatch(Predicate) : 모든 요소가 조건을 만족하면 true
    - noneMatch(Predicate) : 모두 조건을 만족하지 않으면 ture
- forEach() : 요소를 돌면서 실행하는 메서드

### 2-4. 파이프라인 예시

> 파이프라인을 어떻게 생성하느냐에 따라 실행 순서와 성능이 차이나기도 한다.

```java
Stream.of("a", "b", "c", "d", "e")
    .map(s -> {
        System.out.println("map: " + s);
        return s.toUpperCase();
    })
    .filter(s -> {
        System.out.println("filter: " + s);
        return s.startsWith("A");
    })
    .forEach(s -> System.out.println("forEach: " + s));
```

1. `Stream.of()` 로 Stream 생성
2. `map` 으로 데이터 가공 (UpperCase로 변환)
3. `filter` 로 필터링 (A로 시작하는 String만 추출)
4. `종료 오퍼레이션 : forEach` 로 필터링된 끝난 결과로 함수 실행

실행 결과

```json
map: a
filter: A
forEach: A
map: b
filter: B
map: c
filter: C
map: d
filter: D
map: e
filter: E
```

#### filter를 먼저 실행하도록 변경해보자. 

```java
Stream.of("a", "b", "c", "d", "e")
    .filter(s -> {
        System.out.println("filter: " + s);
        return s.startsWith("a");
    })
    .map(s -> {
        System.out.println("map: " + s);
        return s.toUpperCase();
    })
    .forEach(s -> System.out.println("forEach: " + s));
```

1. `Stream.of()` 로 Stream 생성
2. `filter` 로 필터링 (a로 시작하는 String만 추출)
3. `map` 으로 데이터 가공 (UpperCase로 변환)
4. `종료 오퍼레이션 : forEach` 로 필터링된 끝난 결과로 함수 실행

실행 결과

```json
filter: a
map: a
forEach: A
filter: b
filter: c
filter: d
filter: e
```

> 두 예시 모두 동일하게 forEach 는 필터링된 String A 만 출력한 것을 확인할 수 있음.

- 최종적으로 종료 오퍼레이션은 한번만 실행되지만, 중간에 map 실행 횟수가 차이나게 된다.

## 3. Stream API 정리

> stream api를 정리하고 예제를 통해 알아보자.

1. 걸러내기
    - Filter(Predicate)
    - stream에서 특정 조건(Predicate)을 만족하는 엘리먼트만 새로운 스트림으로 반환
2. 변경하기
    - Map(Function) 또는 FlatMap(Function)
    - 각각의 요소에서 특정요소만 꺼내거나 혹은 변경하여 새로운 스트림으로 반환
    - flatMap의 경우 Array나 Object로 래핑되어있는 내용물을 꺼내어 하나로 합친  스트림으로 생성 후 반환
3. 생성하기
    - generate(Supplier) 또는 Iterator(T seed, UnaryOperator)
    - seed 로부터 UnaryOperator 을 무제한으로 반복하는 스트림 반환
    - ex: 랜덤 무제한 스트림
4. 스트림에 있는 데이터가 특정 조건을 만족하는지 확인
    - anyMatch(), allMatch(), nonMatch()
    - 스트림의 엘리먼트를 돌며 특정 조건을 만족하는지 확인
    - ex1:  스트림의 있는 모든 값이 10보다 작은지 확인(allMatch)
    - ex2: 스트림의 제목 중 "Test"가 들어가는 제목이 있는지 확인(anyMatch)
5. 개수 세기
    - count()
    - ex1: 10보다 작은 수의 갯수를 센다.
6. 스트림을 데이터 하나로 뭉치기
    - reduce(identity, ByFunction), collect(), sum(), max()
    - ex1: 모든 숫자 합 구하기
    - ex2: 모든 데이터를 하나의 리스트 혹은 Set에 옮겨 담기

```java
public static void main(String[] args) {
    List<OnlineClass> springClasses = new ArrayList<>();
    springClasses.add(new OnlineClass(1, "spring boot", true));
    springClasses.add(new OnlineClass(2, "spring data jpa", true));
    springClasses.add(new OnlineClass(3, "spring mvc", false));
    springClasses.add(new OnlineClass(4, "spring core", false));
    springClasses.add(new OnlineClass(5, "rest api development", false));

    List<OnlineClass> javaClasses = new ArrayList<>();
    javaClasses.add(new OnlineClass(6, "The Java, Test", true));
    javaClasses.add(new OnlineClass(7, "The Java, Code mainpulation", true));
    javaClasses.add(new OnlineClass(8, "The Java 8 to 11", false));

    List<List<OnlineClass>> keesunEvents = new ArrayList<>();
    keesunEvents.add(springClasses);
    keesunEvents.add(javaClasses);


    System.out.println("spring으로 시작하는 수업");
    springClasses.stream()
            .filter(oc -> oc.getTitle().startsWith("spring"))
            .forEach(System.out::println);

    System.out.println("closed되지 않은 수업");
    springClasses.stream()
            .filter(Predicate.not(OnlineClass::isClosed))
            .forEach(System.out::println);

    System.out.println("수업 이름만 모아서 스트림 만들기");
    Stream<String> springTitleStream = springClasses.stream()
            .map(OnlineClass::getTitle);
    springTitleStream.forEach(System.out::println);

    System.out.println("두 수업 목록에 들어있는 모든 수업 아이디 출력");
    keesunEvents.stream()
            .flatMap(Collection::stream)
            .forEach(oc-> System.out.println(oc.getId()));

    System.out.println("10부터 1씩 증가하는 무제한 스트림 중에서 앞에 10개 빼고 최대 10개 까지만");
    Stream.iterate(10, i -> i+1)
            .skip(10)
            .limit(10)
            .forEach(System.out::println);

    System.out.println("자바 수업 중에 Test가 들어있는 수업이 있는지 확인");
    boolean test = javaClasses.stream().
            anyMatch(oc -> oc.getTitle().contains("Test"));
    System.out.println(test);

    System.out.println("스프링 수업 중에 제목이 spring이 들어간 제목만 모아서 List로 만들기");
    List<String> spring = springClasses.stream()
            .filter(oc -> oc.getTitle().contains("spring"))
            .map(OnlineClass::getTitle)
            .collect(Collectors.toList());
    spring.forEach(System.out::println);
}
```

---

**Reference**

- [백기선 인프런 강의 : 더 자바, Java8](https://www.inflearn.com/course/the-java-java8/dashboard)
- [Catsbi's DLog : 인터페이스의 변화](https://catsbi.oopy.io/3561609e-6c05-4209-a833-c5bb632b5aaa)
- https://github.com/gowoonsori/my-tech/blob/master/java/java8/interface.md
- https://github.com/gowoonsori/my-tech/blob/master/java/java8/stream.md
- https://dev-milk.tistory.com/5
- https://futurecreator.github.io/2018/08/26/java-8-streams-advanced/
- https://mangkyu.tistory.com/115