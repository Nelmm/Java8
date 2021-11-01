# 인터페이스
자바의 `다형성`을 극대화하여 개발코드 수정을 줄이고 `유지보수`를 용이하게 하기 위함

>다형성? 동일한 메시지를 수신했을 때 객체의 타입에 따라 다르게 응답할 수 있는 능력

<br>

## 추상클래스와 인터페이스
추상메서드를 가짐으로써 다형성을 극대화하면서 어떤 `역할`을 구현하는 방법(객체들이 따라야 하는 책임의 집합을 서술한 것)이라는 공통점이 있다.

하지만 추상 클래스는 `is-a`관계로 `-는 -이다.`의 개념이며 책임의 일부를 구현해놓은 것이고 인터페이스는 `has-a`의 관계로 `-는 -를 할 수 있다`의 개념으로 책임의 집합들을 나열해 놓은 것.

이때 이러한 관계가 객체지향에 있어 중요한 것은 객체의 속성이 아니라 객체의 행동간의 관계라는 것.

한마디로 `닭은 새다`, `맥은 os이다` 와 같은 문장이 있을때 이 문장들 앞에 `클라이언트 입장에서` 라는 말이 빠져있는 것이다. 실제세계의 관점으로 바라보며 객체간의 관계를 정의하는 것이 아닌 이 객체들을 사용하는 클라이언트 입장에서 바라보며 특정 객체의 행동을 통해 관계를 맺어야 한다.


<br>

>상속의 가장큰 문제점은 캡슐화를 위반한다는 것과 설계가 유연하지 않다는 것.

>코드의 재사용을 위해서는 상속이 아니라 합성을 사용하는 것이 바람직하고 상속은 타입계층을 구조화하기 위해 사용해야 한다.

>의존성에 의한 영향이 적은 경우에도 추상화에 의존하고 의존성을 명시적으로 드러내는 것이 좋은 설계 습관이다.

<br>

### 서브 클래싱과 서브 타이핑
- 서브 클래싱 : 다른 클래스의 코드를 재사용할 목적으로 상속을 사용하는 경우. 자식과 부모클래스간의 행동이 호환되지 않아 자식인스턴스가 부모인스턴스를 대체할 수없고 이를 `구현 상속` / `클래스 상속`이라고 부른다.
- 서브 타이핑 : 타입 계층을 구성하기 위해 상속을 사용하는 경우. 자식과 부모클래스간의 행동이 호환되어 인스턴스를 대체할 수 있다.

<br><br>

## 인터페이스의 장점
1. 개발 기간 단축 : 팀내에서 서로 다른 부분을 개발할때 인터페이스만 잘 정의해두어도 서로의 개발이 끝나지 않아도 따로 코드를 작성할 수 있다.
2. 클래스간의 낮은 결합도 : 코드의 종속을 줄이고 유지보수성을 높일 수 있다.
3. 표준화 가능 : 협업시에도 일정한 틀의 형태로 개발을 할 수 있게 한다.

<br><br>

## 인터페이스 특징
1. 필드로는 상수만 가능 ( 변수는 불가능 하다. ) : 무조건 `public static final`로 선언 되며 생략 가능
2. 추상 메서드 존재 : 무조건 public abstract으로 선언되며 생략 가능

<br><br>

## Java 8의 인터페이스 변화
### 1. Static method 추가
인스턴스 없이 수행할 수 있는 행동을 정의 한 것으로 해당 인터페이스를 구현한 모든 객체에게 해당 타입과 관련된 유틸,헬퍼 메소드를 제공하고 싶을 때 이용 할 수 있다.
```java
public interface ExInterface {
    static String staticMethod(){
        return "스태틱 메서드입니다";
    }
}

// byte code
public static method()I
   L0
    LINENUMBER 5 L0
    ICONST_1
    IRETURN
    MAXSTACK = 1
    MAXLOCALS = 0
}
```
접근지정자를 생략하면 기본적으로 public이 붙게되고 java8에서는 static method가 `public`만 가능하지만 java9 이상부터는 `private`도 가능하다. 당연히 private이기 때문에 외부에서는 접근이 불가능하다. 

#### private 메서드의 조건
1. 구현부가 존재해야 한다.
2. 해당 인터페이스 내부에서만 사용이 가능하다.

<br>

### 2. Default method 추가
추상 메서드가 아니라 구현이 된 메서드를 제공하는 방법으로 코드의 중복을 피하고 기능을 추가하는 방법

#### 특징
1. 해당 인터페이스를 구현한 구현체도 모르게 기능이 추가가 되기 때문에 많은 위험의 소지가 있다. ( 메서드의 조건들을 모르기 때문에 컴파일타임에는 괜찮더라고 런타임에 에러가 발생할 수 있다. -> 문서화를 철저히 하여 이런 위험발생의 가능성을 최대한 줄이자.)

```java
public interface Foo {
    void printName();

    /**
     * @implSpec 이 구현체는 getName()으로 가져온 문자열을 대문자로 바꿔출력한다.
     */
    default void printNameUpperCase(){
        System.out.println(getName().toUpperCase());
    }

    String getName();       
}
```
이 Foo를 구현한 객체가 getName()이라는 함수의 반환을 `null`로 정의하고 `printNameUpperCase()`를 사용하려한다면 null에서 toUpperCase()를 참조하려고하기 때문에 runtime 에러가 발생할 수 있다. 이러한 문제점때문에 문서화를 꼼꼼히 하거나 에러 처리를 꼼꼼히 하여 정의 해야한다.
```java
/**
* null 방지 하여 구현
*/
default void printNameUpperCase(){
   if(getName() == null) System.out.println("");
   else System.out.println(getName().toUpperCase());
}
```


2. Object 타입이 제공하는 메서드는 기본메서드로 정의가 불가능하다.

![error](https://github.com/gowoonsori/my-tech/blob/master/java/image/default-method-error.png)

3. 인터페이스의 구현체는 이 default 메서드를 ovverride가 가능하다.

4. 같은 이름의 default method를 가진 인터페이스를 여러개 구현할때는 override해주어야 한다.

![erro2r](https://github.com/gowoonsori/my-tech/blob/master/java/image/annotation.png)

구현하려는 인터페이스가 같은 이름의 default 메서드를 가지고 있다면 위 사진과 같이 에러가 발생하기 때문에 재정의 해주거나 사용하려는 인터페이스를 지정할 수 있다.

```java
@Override
    public int method() {
        return Ex1.super.method();      //Ex1의 default method를 사용
        //return Ex2.super.method();
    }
```


#### 사용 목적
`하위 호환성` 이 가장 큰 목적으로 어떤 인터페이스에 기능을 추가했을때 이를 상속,구현한 클래스들이 깨지지 않게 하기위한 목적으로 default 메서드를 사용할 수 있다. 대표적으로 Collection의 of나 forEach 등이 있다.

`람다`를 활용할 수 있는 기술 중 하나로 일종의 `무한한 데이터의 흐름`. 배열또는 컬렉션, Map과 같은 자료구조들을 하나의 데이터흐름으로 만듦으로서 함수 여러개를 chaning하여 코드의 양을 줄이고 간결하게 표현할 수 있게 해준다. 

<br>

# Stream 
## 특징
### 1. 병렬처리 가능
한가지 작업에 대해 여러 스레드로 나누어 병렬적으로 처리하고 결과들을 합하는 방법으로 병렬처리가 가능하고, 이때 자바는 ForkJoinPool 라이브러리를 이용해서 병렬처리를 한다. 

>ForkJoinPool? Fork(Task를 잘게 쪼개는 것)를 수행하고 해당 Pool에 있는 스레드들이 task를 처리하고 join을 통해 합친다. 이때 ForkJoinPool에는 task가 존재하는 queue가 있고, 스레드들이 이 queue에서 task를 할당받아 자신의 queue에 적재해가며 처리하는데 놀고있는 스레드가 존재한다면 다른 스레드의 queue에서 task를 `steal`하는 방법으로 동작하는 `Work-Stealing`메커니즘을 이용하기 때문에 task가 하위 task를 생성하거나 small task가 많을 경우 효과적이다.

```java
public class Ex{
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1,2,4,51,2,3);
        list.parallelStream().forEach(i->System.out.println(i+ " : " + Thread.currentThread().getName()));
    }
}

//print
2 : ForkJoinPool.commonPool-worker-3
51 : main
3 : ForkJoinPool.commonPool-worker-5
1 : ForkJoinPool.commonPool-worker-13
4 : ForkJoinPool.commonPool-worker-9
2 : ForkJoinPool.commonPool-worker-7
```
`parallelStream`을 이용하면 쉽게 병렬스트림을 사용할 수 있다. 

```java
public class Ex{
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1,2,4,51,2,3);
        list.parallelStream()
                .sequential().forEach(i->System.out.println(i+ " : " + Thread.currentThread().getName()));
    }
}

//print
1 : main
2 : main
4 : main
51 : main
2 : main
3 : main
```
`sequential()`를 이용하면 다시 시퀀셜 스트림으로 변경도 가능하다.

<Br>

#### +) Pool Size 조절방법
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
    
<Br>

#### 단점
위에서 설명한것과 같이 ForkJoinPool 특성상 task를 분할할때(fork) 균등하게 분배를하게 되는데 이때 이 분할하는 작업의 비용이 높게 되면 오히려 성능이 안좋을 수 있다. 예를 들어, 이미 사이즈를 알고 있는 ArrayList같은 경우는 분할하는 작업이 빠를 수 있지만 사이즈를 모르는 LinkedList같은 경우에는 순차작업과 같거나 더 느릴 수가 있다.

병렬로 처리되는 작업이 `독립적`이지 않다면 이도 오히려 성능에 안좋을 수 있다. 예를 들어 sorted()/distinct()와 같은 작업을 수행할때는 내부의 상태에 대한 변수를 작업이 `공유`해야지만 가능한 작업이므로 `lock`의 작업이 추가로 발생하고 스레드가 대기하는 상황이 생기기 때문에 순차적으로 실행하는 경우가 더 효과적일 수 있다.

<br>

### 2. 지연연산을 통한 성능 향상
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

### 3. 지연처리 가능
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

<br>

### 4. 재사용 불가능
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

<br><br>

## 메서드 종류
### 1. 생성
스트림 인스턴스 생성하는 메서드

- **Arrays.stream(배열타입 변수)** : 배열을 Stream 타입 객체로 생성
- **collection.stream()** : Collection타입의 객체(Collection, List, Set)를 Stream 타입 객체로 생성
- **Stream.empty()** : 비어있는 Stream() 객체
- **Stream.builder().add()...build()**  : 빌더를 이용한 Stream 생성
- **Stream.generate(() -> {}).limit()** : 람다를 이용하여 생성하는 방식으로 람다의 return값이 들어있는 Stream을 생성한다. 이때 무한히 생성하기 때문에 limit메서드로 제한을 주어야 한다.
- **Stream.iterate(startValue, () -> {}).limit()** : 초기값부터 해당 값을 input으로 하는 람다를 반복하여 나온 값으로 Stream을 생성
- **IntStream.reange(1,5).boxed()** : IntStream을 생성후 boxed()메서드를 통해 Stream 인스턴스로 반환
- **Random().ints().boxed()** : 랜덤한 int값을 이용해 Stream 생성
- **"String".chars()** : IntStream을 반환
    - char들은 ascii 값으로 하나의 숫자로 표현할 수 있기 때문에 IntStream을 반환한다.
- **list.parallelStream()** : 병렬 스트림

### 2. 가공 
필터링, 맵핑과 같이 어떠한 결과를 만들어가는 중간 작업들의 메서드 ( Intermediate operations )

- filter(Predicate) : Predicate의 반환값이 true인 데이터만 선택
- distinct() : 중복된 데이터 제거
- map(Function<T,R>) : 데이터들을 가공하여 다른 값으로 변경하고자 할때 사용
- flatMap(Function<T, Stream>) : 중첩구조를 제거하고 단일 컬렉션으로 만들어 map연산을 수행(flattening)

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

- sorted(Comparator) : Comparator을 이용해 데이터 정렬
- peek(Consumer) : 데이터를 변경하지 않고 그저 순환하는 메서드로 중간에 결과 확인할

<br>

### 3. 종료
최종적으로 결과를 만들고 반환함으로써 chaining을 끝내는 메서드 ( terminal operations )

Stream은 AutoCloseable을 상속받은 BaseStream을 상속받아 close()메서드를 가지고 있어 종료연산을 사용하게 되면 우리가 명시하지 않아도 내부적으로 close()를 시켜주기 때문에 재사용이 불가능하다.

- 단순 계산
    - count() : 총 갯수
    - sum() : 합
    - min() : 최소값
    - max() : 최대값
    - avarage() : 평균값
- reduce() : 정의한 로직을 계산 후 결과 반환
    - reduce(BinaryOperator) : 각 요소를 처리하는 로직
    - reduce(T,BinaryOperator) : 계산을 위한 초기값 T를 가지고 각 요소를 처리
- collect : 특정 데이터형태, 자료구조로 바꾸어주는 함수
- matching
    - anyMatch(Predicate) : 하나라도 조건을 만족하는 요소가 있으면 true
    - allMAtch(Predicate) : 모든 요소가 조건을 만족하면 true
    - noneMatch(Predicate) : 모두 조건을 만족하지 않으면 ture
- forEach() : 요소를 돌면서 실행하는 메서드



<br><br><br>

----
## Reference

https://github.com/gowoonsori/my-tech/blob/master/java/java8/interface.md

https://github.com/gowoonsori/my-tech/blob/master/java/java8/stream.md

https://www.inflearn.com/course/the-java-java8/dashboard

https://dev-milk.tistory.com/5

https://futurecreator.github.io/2018/08/26/java-8-streams-advanced/
