# 함수형 인터페이스와 람다

>  정리 : [창섭](https://github.com/ventulus95)

## 1. 함수형 인터페이스와 람다 표현식 소개

------

> **함수형 인터페이스**란 **추상 메소드가 하나**만 선언된 **인터페이스**이다.

### 1-1. 함수형 VS. 객체지향

Java 개발자에게 익숙한 객체지향 프로그래밍과의 차이를 비교하자면, 값을 취급하는 단위가 어디까지 인지 나눌 수 있다.

Java 는 값(상태)과 행위를 다루기 위한 기본 단위를 **객체**로 정의하고, 이 객체를 **클래스**라는 형태로 구현한다.

함수형 프로그래밍은 **행위(로직)를 값**으로 취급한다. 입력에 의해서만 출력이 결정되는 순수 함수를 기본 단위로 정의한다.

객체지향 → 기능 구현을 위해 필요한 객체를 먼저 설계 함수형 → 기능 단위로 함수를 만들어서 조합

### 1-2. 왜 필요할까?

> **`Side-effect` 를 제거할 수 있다.**

- 연관관계나 연계성 보다는 기능을 하는 함수를 이용해 Side-effect가 없도록 선언형으로 개발한다.
- 순수 함수의 조합으로 이루어지기 때문에 결과 값도 변하지 않는다.
- 예를 들어, 멀티쓰레드 공유자원의 경우 변경이 아닌 복사를 통한 함수 로직 실행으로 결과 값을 얻어 동시성 Side-effect 이슈가 없다.

> **구조적으로 유연하고 간결해진다.**

- 코드 재사용 단위가 클래스였던 것이 함수 단위로 가능하여 유연한 개발이 가능하다.
- 복잡한 연계를 줄일 수 있어 구조적으로 간결해진다.

### 1-3. 코드를 살펴보자.

> 추상 메소드 하나만 정의하면 된다.

`@FunctioanlInterface` 어노테이션은 컴파일 시 체크해주기 때문에 명시해주면 명확하다.

```java
@FunctionalInterface
public interface RunSomething {
    void doIt();
}
```

Java8 이전에는 이 함수형 인터페이스의 구현체를 만들어서 쓰기 위해 **익명 내부 클래스**(annonymous inner class)로 정의해서 사용했다.

```java
public class Foo {

    public static void main(String[] args) {
        RunSomething runSomething = new RunSomething() {
            @Override
            public void doIt() {
                System.out.println("Hello");
            }
        };
    }
}
```

람다 표현식을 사용하면 더 간결하게 표현할 수 있다.

```java
public class Foo {

    public static void main(String[] args) {
        RunSomething runSomething = () -> System.out.println("Hello");
    }
}
```

메소드 구현 코드 라인이 많아지면 다음과 같이 쓸 수 있다.

```java
public class Foo {

    public static void main(String[] args) {
        RunSomething runSomething = () -> {
            System.out.println("Hello");
            System.out.println("YoungJun");
        };
    }
}
```

> Java에서는 이 **함수를 일급 객체로 사용**할 수 있다.

- `() -> System.out.println("Hello")` 이 행위(함수)의 결과를 변수로 할당할 수 있다.
- 위 함수 자체를 리턴 할 수도 있다.
- 함수가 함수를 파라미터로 받거나, 함수가 함수를 리턴할 수 있다. → 이를 고차함수라고 함.

> **순수 함수**란 **동일 입력, 동일 출력**이 원칙이다.

```java
??
@FunctionalInterface
public interface PureFunction {
    int doIt(int number);
}
```

구현해서 사용하면 다음과 같다.

```java
PureFunction pureFunction = (number) -> number + 10;

System.out.println(pureFunction.doIt(1));
System.out.println(pureFunction.doIt(1));
System.out.println(pureFunction.doIt(1));
```

동일 입력, 동일 출력이 보장되어야 `Side-effect`가 없는 함수형 프로그래밍이다.

> **값이 변경될 여지가 있는 경우**

- 함수 외부 상태 값에 의존하는 경우
- 함수 외부에 있는 값을 변경하는 경우

입력으로 참조값(변수)이 오는 경우 Side-effect가 발생할 수 있다.

동일 입력-동일 출력을 지향하되, Java 특성 때문에 순수 함수가 보장되지 않을 수 있다. 진짜 함수형 프로그래밍을 구현하려면 **순수 함수, 불변성을 잘 고려**하자.

## 자바에서의 특수한 함수형 프로그래밍

```java
int id = 2; 
RunSomething runSomething = new RunSomething() {
	int value = 1;	
	@Override
	public void doIt(int num){
      value++; //함수 내부의 상태를 변화 시키면 -> 순수함수는아니고, 함수형 프로그래밍에는 어긋난다. 
      id++; //이렇게 함수 외의 값이 변화하거나
		return num+id;
	}
}
```

순수 함수형 프로그래밍은 아니지만,  자바에서 가능한 모양의 함수형 프로그래밍이다. 물론 사이드 이펙트의 문제점은 가지고 있으므로, 주의해서 사용해야한다. 

## 2. 자바에서 제공하는 함수형 인터페이스

> java.lang.funcation 패키지에 있다.


| 종류 | 인자 | 반환 | 예시 | 설명 |
| ---- | ---- | ---- | ---- | ---- |
|Runnable|    |      |     	| 기본적인 형태의 인터페이스, 인자와 반환값 모두 없음 |
| Function<T, R>      	|   <T>  	|   <R>   	| R apply(T t)                                     	| 함수 조합 용 메소드(andThen, compose)               	|
| BiFunction<T, U, R> 	| <T, U> 	|   <R>   	| R apply(T t, U u)                                	| 두 개의 값(T, U)를 받아서 R 타입을 리턴             	|
| Consumer<T>         	|   <T>  	|         	| void Accept(T t)                                 	| T 타입을 받아서 아무값도 리턴하지 않는 함수         	|
| Supplier<T>         	|        	|   <T>   	| T get()                                          	| 항상 같은 값을 반환                                 	|
| Predicate<T>        	|   <T>  	| Boolean 	| boolean test(T t)                                	| 함수 조합 용 메소드(And, Or, Negate)                	|
| UnaryOperator<T>    	|   <T>  	|   <T>   	| Function<T, R> 의 특수한 형태                    	| 입력값 하나를 받아서 동일한 타입을 리턴             	|
| BinaryOperator<T>   	| <T, T> 	|   <T>   	| BiFunction<T, U, R> 의 특수한 형태               	| 동일한 타입의 입렵값 두개를 받아 리턴               	|
| BiConsumer<T, U>    	| <T, U> 	|         	| void Accept(T t, U u)                            	| 인자 2개를 받고 리턴하지 않는 함수                  	|
| BiPredicate<T, U>   	| <T, U> 	| Boolean 	| boolean test(T t, U u)                           	| 인자 2개를 받고 Boolean 타입 반환                   	|
| Comparator<T>       	| <T, T> 	|   int   	| 같은 제너릭 타입의 인자 2개를 받고 Integer 반환  	| 객체간의 값을 비교하기 위한 compare 기능            	|



- 제네릭 타입으로 객체형을 명시한다.
- 인자는 최대 2개로 설계했다.
  - 이것은 함수를 어떻게 설계하는 것이 좋은지에 대한 가이드라고 볼 수 있음
  - 함수는 한가지의 일만 해야 되며 인자가 2개를 넘어가는 순간 하나 이상의 일을 하고 있을 가능성이 높으므로 다른 부수효과를 일으키지 않도록 어느정도 설계를 강제하는 것
  - 하지만 로직에 어쩔 수 없이 (그런 경우는 거의 없지만) 하나의 함수에서 처리하는게 더 효율적이라면 별도의 DTO 를 정의하고 여기에 값을 담아 인자로 전달하는 방법을 사용
- 자세한 구현은 [java.lang.funcation](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html) 에서 확인하면 될 것 같다.

4가지 API 함수형 인터페이스 적절하게 사용

- Function<T, R> : 작업으로 타입 변환할 때
- Consumer<T> : 작업은 하되 딱히 리턴되는 것이 없을 때
- Predicate<T> : 작업하면서 true, false 작업이 필요할 때
- Supplier<T> : 작업을 지연시켜야할 때 혹은 특정 시점에만 작업될 수 있도록 할 때

## 3. 람다 표현식

> **[(인자 리스트) → {바디}](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)**

- 내부적으로는 익명 클래스 구현과 같다고 한다.

## 특징

1. 간결한 코드

```java
//before
int max(int a, int b){
    return a > b ? a : b;
}

//after
(int a, int b) -> { return a > b ? a : b; }
```

2. 멀티스레드환경에서 용이

람다식은 순수함수로써 같은 입력이라면 항상 같은 출력을 보장하여 Side Effect가 없으며 외부 상태를 변경하지 않기 때문에 병렬환경에서 용이하다.

3. 지연 연산이 가능하다. `Streaming/Chaning`이라고 부르는 방식으로 변수값 각각 하나에 대하여 체이닝된 함수를 순서대로 실행한다.

첫 함수에 모든 변수가 실행되고 다음 함수가 실행되는 방식이 아니다. 이런 지연연산을 통해 cpu자원을 아낄 수 있다.

```java
//홀수인지 체크
public boolean isOdd(int n){
    System.out.println("isOdd : " + n);
    return n % 2 == 1;
}

//곱하기 2
public int doubleIt(int n){
    System.out.println("doubleIt : " + n);
    return n * 2;
}

//6보다 큰지 체크
public boolean isGreaterThan6(int n){
    System.out.println("is greater than 6 : " + n);
    return n > 6;
}
```

위와 같은 메서드가 존재할 때 주어진 배열에서 `홀수이면서 곱하기 2했을때 6보다 큰 가장 첫번째 숫자`를 찾는 로직을 구현한다고 하자.

```java
int[] arr = new int[]{1, 2, 3, 4, 5, 6, 7, 8, 9};


List<Integer> list1 = new ArrayList<>();
for(int n : arr){
    if(isOdd(n))list1.add(n);
}

List<Integer> list2 = new ArrayList<>();
for(int n : list1){
    list2.add(doubleIt(n));
}

for(int n : list2){
    if(isGreaterThan6(n)) {
        System.out.println(list3.get(0));
        break;
    }
}

//print
isOdd : 1
isOdd : 2
isOdd : 3
isOdd : 4
isOdd : 5
isOdd : 6
isOdd : 7
isOdd : 8
isOdd : 9
doubleIt : 1
doubleIt : 3
doubleIt : 5
doubleIt : 7
doubleIt : 9
is greater than 6 : 2
is greater than 6 : 6
is greater than 6 : 10
10
```

만일 위와같이 코드를 작성한다면 매 함수마다 특정 변수를 모두 loop돌게 된다.

```java
System.out.println(Arrays.stream(arr)
            .filter(Lambda::isOdd)
            .map(Lambda::doubleIt)
            .filter(Lambda::isGreaterThan6)
            .findFirst().getAsInt());
//print
isOdd : 1
doubleIt : 1
is greater than 6 : 2
sOdd : 2
sOdd : 3
doublet : 3
is greater than 6 : 6
isOdd : 4
isOdd : 5
doubleIt : 5
is greater than 6 : 10
10
```

하지만 위처럼 람다식을 이용하게 되면 지연연산이 수행되면서 **체이닝**을 끝내는 함수인 `findFirst`를 만나 더이상 loop을 돌지 않고 끝내는 걸 볼 수 있다.

```
for(int n : arr){
    if(isOdd(n) && isGreaterThan6(doubleIt(n))){
        System.out.println("(End) N is " + n);
        break;
    }
}
```

물론 위와 같이 코드를 작성할 수도 있지만 함수의 조건이 많아질 수록 코드의 가독성은 람다보다 떨어지게 될 것이다.

4. loan pattern (빌려쓰기 패턴) 적용 가능

> 빌려쓰기 패턴? 람다를 입력으로 받는 메서드가 대신해서 자원을 열고 닫는 패턴

``` java
class Resource{
   public Resource(){
       System.out.println("Create resource");
   }

   public void useResource(){
       System.out.println("Use resource");
   }

   public void dispose(){
       System.out.println("Disposing resource");
   }
}

public static void main(String[] args) {
    Resource resource = new Resource();
    resource.useResource(); //자원 사용
    resource.dispose();
}
```

일반적으로 객체를 생성하여 사용하게 되면 그 자원의 관리를 자원을 빌려쓴쪽(사용하는쪽)이 하게되고 만일 Run 타임시에 에러가 발생하여 무조건 자원을 반환해야한다면 자원을 사용하는 코드블럭마다 try~finally로 묶어 dispose해주어야 되기 때문에 코드의 반복이 발생하게 된다.

이를 아래처럼 람다를 이용하면 자원의 관리를 빌려쓰는쪽이 아닌 빌려주는 쪽(자원의 주체)가 관리할 수 있게 되고 코드의 반복을 피할 수 있다.

```java
public class Resource {
    private Resource() {
        System.out.println("Create resource");
    }

    public void useResource() {
        System.out.println("Use resource");
    }

    public void dispose() {
        System.out.println("Disposing resource");
    }

     public static void withResource(Consumer<Resource> consumer) {
        Resource resource = new Resource();
        try {
            consumer.accept(resource);
        } finally {
            resource.dispose();
        }   
    }
}

public class Lambda {
    public static void main(String[] args) {
        Resource.withResource(Resource::useResource);
    }
}
```



### 3-1. 인자 리스트

- 인자 없을 때 : ()
- 인자 한 개일 때 : (one) / one
- 인자 두 개이상 일 때 : (onw, two ~)
- 인자 타입 생략 가능 -> 컴파일러가 추론(infer)

### 3-2. 바디

- 화살표 오른쪽에 함수 본문 정의
- 여러 줄인 경우 { } 사용
- 한 줄인 경우 { }, return 생략 가능

### 3-3. 변수 캡처 (Variable Capture)

- Local variable capture

  - final, effective final인 경우에만 참조 가능 → 아닌 경우 concurrency 문제 발생 가능

- effective final (JAVA 8 지원)

  ```java
  (final) int baseNumber = 10;    // final이 없지만 이 변수는 어디서도 변경하지 않는다.
  ```

  - `사실상` final인 변수
  - final 사용하지 않은 변수를 익명 클래스 구현체 또는 람다에서 참조할 수 있다.

- 람다는 익명 클래스 구현체와 달리 `Shadowing` 않는다. [참조](https://docs.oracle.com/javase/tutorial/java/javaOO/nested.html#shadowing)

  - 익명 클래스는 새로운 Scope를 만들지만 람다는 람다를 감싸고 있는 Scope와 같다.

### 3-4. Shadowing

[옳은 예]

```java
public class ShadowTest {

    public int x = 0;

    class FirstLevel {

        public int x = 1;

        void methodInFirstLevel(int x) {
            System.out.println("x = " + x);
            System.out.println("this.x = " + this.x);
            System.out.println("ShadowTest.this.x = " + ShadowTest.this.x);
        }
    }

    public static void main(String... args) {
        ShadowTest st = new ShadowTest();
        ShadowTest.FirstLevel fl = st.new FirstLevel();
        fl.methodInFirstLevel(23);
    }
}

// ---------------------- Output -------------------------------------------
    x = 23
    this.x = 1
    ShadowTest.this.x = 0
```

[틀린 예]

```java
public class Foo {

    public static void main(String[] args) {
        Foo foo = new Foo();
        foo.run();
    }

    private void run() {
        int baseNumber = 10;

        // Lambda Error : run()과 같은 scope 공유
        IntConsumer printInt = (baseNumber) -> {
            System.out.println(i + baseNumber)
        }

        // Lambda 내부 Sout부분 오류발생 : 람다는 effective final, final만 사용 가능
        baseNumber++;
    }
}
```

**[제약사항]**

> 람다식을 쓴다면 최소한 인터페이스 타입 객체가 생성될 때 타입파라미터가 있어야함

- 아무런 정보없이 람다식을 사용하면 타입추론이 어려워서 컴파일 단계에서 에러남
- 함수형 인터페이스의 메서드가 제네릭 메서드인 경우
  - 호출할 때 어떤 타입인지 알 수 있는 경우 (추론 불가)

```java
@FunctionalInterface
public interface InvaildFuncInterface {
    <T> String print(T value);
}

class InvalidFuncInterfaceUse{
    public static void main(String[] args) {

        /* 호출할 때 value가 비로소 어떤 타입인지 알 수 있음 : 추론 불가 */
        //getPrint(1, s -> s.toString());
    }

    public static <T> void getPrint(T value, InvaildFuncInterface invalidFuncInterfaceUse){
        System.out.println(invalidFuncInterfaceUse.print(value));
    }
}
```



## 4. **메소드 레퍼런스**

> 람다가 하는 일이 기존 메소드 또는 생성자를 호출하는 거라면, 메소드 레퍼런스를 사용해서 매우 간결하게 표현할 수 있다.

### 방법

1. 생성자 참조

```
Supplier<String> supplier = () -> new String();
 Supplier<String> supplier2 = String::new;
 String str = supplier.get();
 
 //사용예
 List<List<Integer>> list = new ArrayList<>();
 int[][] arr = list.stream()
        .map(l -> l.stream()
            .mapToInt(Integer::intValue)
            .toArray())
        .toArray(int[][]::new);
```

이때 생성자 참조는 실제 생성자를 이용해 객체를 만든 것이 아니라 말그대로 `참조`이기 때문에 Supplier의 `get()`, Function의 `apply()`, 위 예시의 Stream의 `toArray()`와 같이 실제로 실행하는 부분에서 생성자를 이용하게 된다.

2. Static Method 참조 Static 메서드 또한 참조가 가능하며 이도 직접적으로 실행하는 메서드를 통해 실행이 가능하다.

```
static class MethodReference{
    public MethodReference() {
        System.out.println("hi");
    }

    public static void print(String name){
        System.out.println("hi " + name);
    }

}

public static void main(String[] args) {
    String name = "홍";

    MethodReference.print(name);        //일반적인 static 메서드 사용

    Consumer<String> consumer = MethodReference::print; //static 메서드 참조
    consumer.accept(name);
}
```

3. 특정 객체의 Instance Method 참조 특정 객체(인스턴스)의 일반 메서드도 참조할 수 있는데 이때는 static method와 달리 `클래스명::static 메서드명`이 아니라 `인스턴스명::메서드명`과 같은 형태로 참조가 가능하다.

```
static class MethodReference{
    public MethodReference() {
        System.out.println("hi");
    }

    public void print(String name){
        System.out.println("hi " + name);
    }

}

public static void main(String[] args) {
    String name = "홍";

    MethodReference object = new MethodReference();     //일반적인 객체 메서드 사용
    object.print(name);

    Consumer<String> consumer = object::print;      //instance 메서드 참조
    consumer.accept(name);
}
```

4. 임의 객체의 Instance Method 참조 정렬에 이용되는 Comparator가 대표적으로 이때는 static 메서드 참조와 동일하게 `클래스명::메서드명`으로 사용된다.

```java
String[] names = {"a", "B", "c"};
Arrays.sort(names, String::compareToIgnoreCase);
```

 (a,b) -> int를 리턴하는 형태여야지만, 람다를 사용할 수 있는데 `compareToIgnoreCase` 의 Comparator 람다식이 작동할 수 있는 이유는 다음과 같다 

```java
public int compareToIgnoreCase(String str) {
    return CASE_INSENSITIVE_ORDER.compare(this, str);
}
```

얼핏보기에는 파라미터가 하나라서, 왜 가능한가? 싶겠지만. 

현재 객체인 this와 String parameter에 들어간 Str이 a와 b가 되고 Return으로 int를 리턴해주기 때문에 이것이 임의 객체의 instance Method 참조를 사용하는 방식이다

# 🏊‍♂️ Deep Dive! 🤿

## byte 코드 비교

```
//일반적인 함수형 인터페이스 구현
public class Lambda {
    @FunctionalInterface
    public interface Functional {
        public int cal(int a, int b);
    }
    public static void main(String[] args) {
        Functional functional = new Functional() {
            @Override
            public int cal(int a, int b) {
                return a+b;
            }
        };
    }
}

//byte 코드
public class ex/Lambda {

  // compiled from: Lambda.java
  NESTMEMBER ex/Lambda$Functional
  NESTMEMBER ex/Lambda$1
  // access flags 0x609
  public static abstract INNERCLASS ex/Lambda$Functional ex/Lambda Functional
  // access flags 0x0
  INNERCLASS ex/Lambda$1 null null

  // access flags 0x1
  public <init>()V
   L0
    LINENUMBER 11 L0
    ALOAD 0
    INVOKESPECIAL java/lang/Object.<init> ()V       
    RETURN
   L1
    LOCALVARIABLE this Lex/Lambda; L0 L1 0
    MAXSTACK = 1
    MAXLOCALS = 1

  // access flags 0x9
  public static main([Ljava/lang/String;)V
   L0
    LINENUMBER 17 L0
    NEW ex/Lambda$1
    DUP
    INVOKESPECIAL ex/Lambda$1.<init> ()V            //Lambda$1의 메서드 호출
    ASTORE 1
   L1
    LINENUMBER 23 L1
    RETURN
   L2
    LOCALVARIABLE args [Ljava/lang/String; L0 L2 0
    LOCALVARIABLE functional Lex/Lambda$Functional; L1 L2 1
    MAXSTACK = 2
    MAXLOCALS = 2
}

//컴파일러가 생성한 익명 클래스 Lambda$1
class Lambda$1 implements Functional {
    Lambda$1() {
    }

    public int cal(int a, int b) {
        return a + b;
    }
}
```

메서드내에서 함수형인터페이스를 override하여 직접 구현하는 방식은 build 했을때 **Functional**이라는 인터페이스를 이용하여 `Lambda$1`이라는 익명 클래스를 만들어내어 호출부에서느 **Lamda$1**의 메서드를 호출하는 것을 볼 수 있다. 내부적으로 익명클래스로 컴파일하여 사용하지 않는 이유는 java8이전의 람다를 사용하기 위한 라이브러리나, 코틀린같은 언어에서 람다를 이와 가티 단순히 익명클래스로 치환하여 사용하기 때문에 이처럼 구현이 되어있다.

이렇게 익명클래스로 사용할 경우의 문제점은 **람다식마다 클래스가 하나씩 생겨나고 항상 새로운 인스턴스로 할당되는 문제**가 있을수 있다.

```
//람다이용한 함수형 인터페이스 구현
// class version 55.0 (55)
// access flags 0x21
public class ex/Lambda2 {

  // compiled from: Lambda2.java
  NESTMEMBER ex/Lambda2$Functional
  // access flags 0x609
  public static abstract INNERCLASS ex/Lambda2$Functional ex/Lambda2 Functional
  // access flags 0x19
  public final static INNERCLASS java/lang/invoke/MethodHandles$Lookup java/lang/invoke/MethodHandles Lookup

  // access flags 0x1
  public <init>()V
   L0
    LINENUMBER 3 L0
    ALOAD 0
    INVOKESPECIAL java/lang/Object.<init> ()V
    RETURN
   L1
    LOCALVARIABLE this Lex/Lambda2; L0 L1 0
    MAXSTACK = 1
    MAXLOCALS = 1

  // access flags 0x9
  public static main([Ljava/lang/String;)V
   L0
    LINENUMBER 9 L0
    INVOKEDYNAMIC cal()Lex/Lambda2$Functional; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/LambdaMetafactory.metafactory(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
      // arguments:
      (II)I, 
      // handle kind 0x6 : INVOKESTATIC
      ex/Lambda2.lambda$main$0(II)I, 
      (II)I
    ]
    ASTORE 1
   L1
    LINENUMBER 10 L1
    RETURN
   L2
    LOCALVARIABLE args [Ljava/lang/String; L0 L2 0
    LOCALVARIABLE functional Lex/Lambda2$Functional; L1 L2 1
    MAXSTACK = 1
    MAXLOCALS = 2

  // access flags 0x100A
  private static synthetic lambda$main$0(II)I
   L0
    LINENUMBER 9 L0
    ILOAD 0
    ILOAD 1
    IADD
    IRETURN
   L1
    LOCALVARIABLE a I L0 L1 0
    LOCALVARIABLE b I L0 L1 1
    MAXSTACK = 2
    MAXLOCALS = 2
}
```

**람다를 이용한 방식**은 새로운 익명클래스를 생성시키지는 않고 람다를 사용한 메서드를 새롭게 **static**으로 생성(lambda$main$0)해서 이를 실행하는 것을 볼 수 있다.

> INVOKEDYNAMIC 이란?
>
> bootstrap영역의 lambdafactory.metafactory()를 호출하는데 이는 Java runtime library의 표준화 메서드로 객체를 어떤방법으로 생성할지 동적으로 결정한다는 의미 (새로, 재사용, 프록시, 래퍼클래스 등). 후에 `java/lang/invoke/CallSite`를 통해서 객체를 return
>
> 한마디로 람다가 변환되는 인터페이스의 인스턴스를 반환하는 코드로 한번만 생성되고 재 호출시에는 재사용이 가능하다.



## 💫 원본 글의 출처

의성: https://github.com/gowoonsori/my-tech/blob/master/java/java8/lamda.md

창섭: https://ventulus95.notion.site/JAVA-8-ab2c018bfa1a4f23b2be591c3d51fa80

영준: https://youngjun95.notion.site/Java8-1-1b3b4426d3674c33973fd0979b3c26eb