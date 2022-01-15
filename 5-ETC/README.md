>  정리 : [영준](https://github.com/jun108059)

목차

- [애노테이션의 변화](#애노테이션의-변화)
    - [1. 애노테이션 관련 두가지 큰 변화](#1-애노테이션-관련-두가지-큰-변화)
        - [1-1. 타입 선언 부](#1-1-타입-선언-부)
        - [1-2. 중복 사용할 수 있는 애노테이션을 만들기](#1-2-중복-사용할-수-있는-애노테이션을-만들기)
- [배열 병렬 정렬](#배열-병렬-정렬)
    - [1. Arrays.parallelSort()](#1-arraysparallelsort)
    - [2. 병렬 정렬 알고리즘](#2-병렬-정렬-알고리즘)
    - [3. sort()와 parallelSort() 비교](#3-sort와-parallelsort-비교)
- [Meta space](#meta-space)
    - [1. PermGen](#1-permgen)
    - [2. Metaspace](#2-metaspace)

# 애노테이션의 변화

## 1. 애노테이션 관련 두가지 큰 변화

- 자바 8부터 애노테이션을 **타입 선언부에도 사용**할 수 있게 됨
- 자바 8부터 애노테이션을 **중복해서 사용**할 수 있게 됨

### 1-1. 타입 선언 부

- 제네릭 타입
- 변수 타입
- 매개변수 타입
    - @Target(ElementType.TYPE_PARAMETER)
- 예외 타입
- etc

#### 1-1-1. 기존의 애노이션 Target 종류

- TYPE : 클래스 / 인터페이스 / 어노테이션 / enum / record의 선언부
- ANNOTATION_TYPE : 어노테이션의 선언부
- FIELD : 클래스내의 내부필드의 선언부
- METHOD : 클래스내의 메서드의 선언부
- PARAMETER : 메서드내의 파리미터의 선언부
- CONSTRUCT : 생성자의 선언부
- LOCAL_VARIABLE : 지역변수의 선언부
- PACKAGE : 해당 패키지의 package-info.java 에 붙일 수 있다.


```java
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,

    /** Field declaration (includes enum constants) */
    FIELD,

    /** Method declaration */
    METHOD,

    /** Formal parameter declaration */
    PARAMETER,

    /** Constructor declaration */
    CONSTRUCTOR,

    /** Local variable declaration */
    LOCAL_VARIABLE,

    /** Annotation type declaration */
    ANNOTATION_TYPE,

    /** Package declaration */
    PACKAGE,
}
```

#### 1-1-2. java8에 추가된 Target

```java
public enum ElementType {

    // ... 이전과 동일 (생략)
  
    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,

    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```

> 타입에 애노테이션을 사용하기 위해 아래와 같은 ENUM 타입을 명시해야 함.

- `TYPE_PARAMETER` : 타입 변수에만 사용할 수 있다.
- `TYPE_USE` : 타입 변수를 포함해서 모든 타입 선언부에 사용할 수 있다.


- 위의 어노테이션들과는 다르게 선언 주석이 아닌 `TYPE 주석`이다.
    - 선언 주석 : 주의사항, 사용방법, 사용처 등 설명
    - TYPE 주석 : 정수 값이 0보다 커야 한다, null이 아니다 와 같은 값에 대한 정보 제공함으로써 implements, throws, new 구절에 사용하거나 제네릭에 사용함으로써 외부의 프로젝트에도 적용할 수 있도록 확장한 범위

```java
@Retention(RetentionPolicy.RUNTIME) // 이 애노테이션 정보를 언제까지 유지할 것인가
@Target(ElementType.TYPE_PARAMETER) // 이 애노테이션을 사용할 곳
public @interface Chicken {
}
```

```java
public class App {
	public static void main(@Chicken String[] args) throws @Chicken RuntimeException {
		List<@Chicken String> names = Arrays.asList("Park");
	}
	// @Target(ElementType.TYPE_PARAMETER) parameter 만 사용
	static class FeelsLikeChicken<@Chicken T> {

	}
	// @Target(ElementType.TYPE_USE) 사용
	static class FeelsLikeChicken2<@Chicken T> {
		// 전자의 <C> 는 타입 파라미터
		// 후자의 C 는 타입
		public static <@Chicken C> void print(@Chicken C c) {
			System.out.println(c);
		}
	}
}
```

`TYPE_PARAMETER`와 `TYPE_USE`에 대해 더 자세히 알아보기 위해 다음 예제를 살펴보자.

**TYPE_PARAMETER**

>  타입 선언부에 사용이 가능

```java
    //annotation
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_PARAMETER)
public @interface ParmeterEx {
}

//class에 사용
public class AnnotationStudy <@ParmeterEx T> {
    public void print( T t){}
}

//method에 사용
public class AnnotationStudy {
    public <@ParmeterEx T> void print( T t){}
}

//method에 사용
public class AnnotationStudy {
    public <@ParmeterEx T> void print(T t) throws @ParameterEx SomthingException{}
}
```

```
//BYTE CODE
public class study/AnnotationStudy {
    // compiled from: AnnotationStudy.java

    @Lstudy/ParmeterEx;() : CLASS_TYPE_PARAMETER 0, null

    // access flags 0x0
    Ljava/lang/String; name

    // access flags 0x1
    public <init>()V
    L0
        LINENUMBER 3 L0
        ALOAD 0
        INVOKESPECIAL java/lang/Object.<init> ()V
    L1
        LINENUMBER 4 L1
        ALOAD 0
        LDC "default"
        PUTFIELD study/AnnotationStudy.name : Ljava/lang/String;
        RETURN
    L2
        LOCALVARIABLE this Lstudy/AnnotationStudy; L0 L2 0
        // signature Lstudy/AnnotationStudy<TT;>;
        // declaration: this extends study.AnnotationStudy<T>
        MAXSTACK = 2
        MAXLOCALS = 1

    // access flags 0x1
    // signature <R:Ljava/lang/Object;>()V
    // declaration: void print<R>()
    public print()V
    @Lstudy/ParmeterEx;() : METHOD_TYPE_PARAMETER 0, null
```

컴파일하면서 해당 타입이 무슨 타입인지 분석하여 **CLASS_TYPE_PARAMETER / METHOD_TYPE_PARAMETER** 로 변환하는 것을 볼 수 있다.

타입 선언부에 사용이 가능한 Target이므로 아래와 같이 실제 사용하는 부분에는 적용할 수 없다.

```java
public class AnnotationStudy {
    public <T> void print(@ParmeterEx T a){}
}
```

**TYPE_USE**

> 선언부 뿐만이 아닌 타입 사용되는 모든 곳에 적용이 가능  
> (클래스/인터페이스/내부필드/파라미터/제네릭/지역변수 등)

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_USE)
public @interface ParmeterEx {
}

@ParmeterEx
public class AnnotationStudy <@ParmeterEx T>{
  @ParmeterEx
  String name = "default";

  @NonNull
  public <@ParmeterEx R> void print(@ParmeterEx String t, @ParmeterEx R r){
    @ParmeterEx
    int a=1;
  }
}
```
바이트 코드를 보면 TYPE_USE를 사용해도 컴파일 결과는 컴파일러가 적절한 어노테이션 TARGET으로 바꾸는 것을 볼 수 있다.

```
// Byte Code
// class version 61.0 (61)
// access flags 0x21
// signature <T:Ljava/lang/Object;>Ljava/lang/Object;
// declaration: study/AnnotationStudy<T>
public class study/AnnotationStudy {

// compiled from: AnnotationStudy.java

@Lstudy/ParmeterEx;()

@Lstudy/ParmeterEx;() : CLASS_TYPE_PARAMETER 0, null

// access flags 0x0
Ljava/lang/String; name
@Lstudy/ParmeterEx;() : FIELD, null

// access flags 0x1
public <init>()V
L0
    LINENUMBER 4 L0
    ALOAD 0
    INVOKESPECIAL java/lang/Object.<init> ()V
L1
    LINENUMBER 5 L1
    ALOAD 0
    LDC "default"
    PUTFIELD study/AnnotationStudy.name : Ljava/lang/String;
    RETURN
L2
    LOCALVARIABLE this Lstudy/AnnotationStudy; L0 L2 0
    // signature Lstudy/AnnotationStudy<TT;>;
    // declaration: this extends study.AnnotationStudy<T>
    MAXSTACK = 2
    MAXLOCALS = 1

// access flags 0x1
// signature <R:Ljava/lang/Object;>(Ljava/lang/String;TR;)V
// declaration: void print<R>(java.lang.String, R)
public print(Ljava/lang/String;Ljava/lang/Object;)V
@Lstudy/ParmeterEx;() : METHOD_TYPE_PARAMETER 0, null
@Lstudy/ParmeterEx;() : METHOD_FORMAL_PARAMETER 0, null
@Lstudy/ParmeterEx;() : METHOD_FORMAL_PARAMETER 1, null
L0
    LINENUMBER 10 L0
    ICONST_1
    ISTORE 3
L1
    LINENUMBER 11 L1
    RETURN
L2
    LOCALVARIABLE this Lstudy/AnnotationStudy; L0 L2 0
    // signature Lstudy/AnnotationStudy<TT;>;
    // declaration: this extends study.AnnotationStudy<T>
    LOCALVARIABLE t Ljava/lang/String; L0 L2 1
    LOCALVARIABLE r Ljava/lang/Object; L0 L2 2
    // signature TR;
    // declaration: r extends R
    LOCALVARIABLE a I L1 L2 3
    LOCALVARIABLE @Lstudy/ParmeterEx;() : LOCAL_VARIABLE, null [ L1 - L2 - 3 ]
    MAXSTACK = 1
    MAXLOCALS = 4

// access flags 0x9
public static main([Ljava/lang/String;)V
L0
    LINENUMBER 15 L0
    RETURN
L1
    LOCALVARIABLE args [Ljava/lang/String; L0 L1 0
    MAXSTACK = 0
    MAXLOCALS = 1
}

```

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.LOCAL_VARIABLE, ElementType.TYPE_USE})
@Retention(RetentionPolicy.CLASS)
@Documented
public @interface NonNull {
}
```

Lombok의 NonNull 어노테이션으로 해당 어노테이션도 Target범위가 TYPE_USE가 포함되어 있다.


### 1-2. 중복 사용할 수 있는 애노테이션을 만들기

- 중복 사용할 애노테이션 만들기
- 중복 애노테이션 컨테이너 만들기
    - 컨테이너 애노테이션은 중복 애노테이션과 `@Retention` 및 `@Target`이 같거나 더 넓어야 한다.

#### 1-2-1. 중복 사용할 애노테이션 생성

기존에는 애노테이션을 같은 범위에 중복해서 정의할 수 없었는데 java8 부터는 `@Repeatable()`이 추가되어 중복해서 사용이 가능해졌다.

`Repeatable`은 한개의 value를 가지고 있는데, 여기에 일종의 애노테이션 컨테이너 역할을 할 애노테이션 클래스를 넘겨주면 해당 컨테이너에 중복 사용한
애노테이션들을 담는 방식으로 동작하게 된다. 

이때 주의할 점이 `Repeatable`의 value는 애노테이션의 컨테이너 역할이기 때문에 중복해서 사용할 어노테이션보다 
생명주기(RetentionPolicy)가 길어야만 한다.

```java
/*
  @Target(ElementType.TYPE_PARAMETER)
    - 제네릭을 사용할 때 타입 변수 앞에 애노테이션을 사용할 수 있다.
  @Target(ElementType.TYPE_USE)
   - 타입 변수를 포함해서 타입 선언부에서 애노테이션을 사용할 수 있다.
  @Repeatable(컨테이너 성격의 애노테이션 class)
   - 여러 개의 애노테이션을 가지고 있을 컨테이너 애노테이션을 설정해주면 반복해서 애노테이션 사용이 가능하다.
   - 컨테이너의 애노테이션 설정(Retention, Target)은 반드시 자기자신이 감싸는 애노테이션의 범위보다 같거나 넓어야 한다.
 */
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_USE)
@Repeatable(ChickenContainer.class)
public @interface Chicken {
		String value() default "후라이드";
}
```



#### 1-2-2. 중복 애노테이션의 컨테이너 생성

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_USE)
public @interface ChickenContainer {
    // 컨테이너 애노테이션은 자기 자신이 감쌀 애노테이션 배열을 가지고 있어야 한다.
    Chicken[] value();
}
```

#### 1-2-3. 컨테이너 애노테이션으로 중복 애노테이션 참조

```java
@Chicken
@Chicken("양념")
@Chicken("마늘간장")
public class App {

    public static void main(String[] args) {
        // 1. 클래스에서 해당 애노테이션 타입으로 바로 읽어오는 방법
        Chicken[] chickens = App.class.getDeclaredAnnotationsByType(Chicken.class);
        Arrays.stream(chickens)
            .forEach(c -> System.out.println(c.value()));

        // 2. 컨테이너 애노테이션 타입으로 읽는 방법
        ChickenContainer chickenContainer = App.class.getAnnotation(ChickenContainer.class);
        Arrays.stream(chickenContainer.value())
            .forEach(c -> System.out.println(c.value()));
    }
}
```

> `@Chicken` 은 default value가 없으면 에러가 발생한다.

```java
// ERROR 발생
@Chicken
@Chicken("양념")
public class App() {

}
```

더 나아가서 `main`에서 2번째로 방법으로 컨테이너 애노테이션 타입으로 읽어들일 때 바이트 코드를 살펴보자.

```
// Byte Code
// class version 59.0 (59)
// access flags 0x21
public class javaStudy/Example {

  // compiled from: Example.java

  @LjavaStudy/ChickenContainer;(value={@LjavaStudy/Chicken;(value="\uc591\ub150"), @LjavaStudy/Chicken;(value="\ub9c8\ub298\uac04\uc7a5")})
  // access flags 0x19
  public final static INNERCLASS java/lang/invoke/MethodHandles$Lookup java/lang/invoke/MethodHandles Lookup

  // access flags 0x1
  public <init>()V
   L0
    LINENUMBER 7 L0
    ALOAD 0
    INVOKESPECIAL java/lang/Object.<init> ()V
    RETURN
   L1
    LOCALVARIABLE this LjavaStudy/Example; L0 L1 0
    MAXSTACK = 1
    MAXLOCALS = 1

  // access flags 0x9
  public static main([Ljava/lang/String;)V
   L0
    LINENUMBER 9 L0
    LDC LjavaStudy/Example;.class
    LDC LjavaStudy/ChickenContainer;.class
    INVOKEVIRTUAL java/lang/Class.getAnnotation (Ljava/lang/Class;)Ljava/lang/annotation/Annotation;
    CHECKCAST javaStudy/ChickenContainer
    ASTORE 1
   L1
    LINENUMBER 10 L1
    ALOAD 1
    INVOKEINTERFACE javaStudy/ChickenContainer.value ()[LjavaStudy/Chicken; (itf)
    INVOKESTATIC java/util/Arrays.stream ([Ljava/lang/Object;)Ljava/util/stream/Stream;
    INVOKEDYNAMIC accept()Ljava/util/function/Consumer; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/LambdaMetafactory.metafactory(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
      // arguments:
      (Ljava/lang/Object;)V,
      // handle kind 0x6 : INVOKESTATIC
      javaStudy/Example.lambda$main$0(LjavaStudy/Chicken;)V,
      (LjavaStudy/Chicken;)V
    ]
    INVOKEINTERFACE java/util/stream/Stream.forEach (Ljava/util/function/Consumer;)V (itf)
   L2
    LINENUMBER 13 L2
    RETURN
   L3
    LOCALVARIABLE args [Ljava/lang/String; L0 L3 0
    LOCALVARIABLE chickenContainer LjavaStudy/ChickenContainer; L1 L3 1
    MAXSTACK = 2
    MAXLOCALS = 2

  // access flags 0x100A
  private static synthetic lambda$main$0(LjavaStudy/Chicken;)V
   L0
    LINENUMBER 11 L0
    GETSTATIC java/lang/System.out : Ljava/io/PrintStream;
    ALOAD 0
    INVOKEINTERFACE javaStudy/Chicken.value ()Ljava/lang/String; (itf)
    INVOKEVIRTUAL java/io/PrintStream.println (Ljava/lang/String;)V
   L1
    LINENUMBER 12 L1
    RETURN
   L2
    LOCALVARIABLE c LjavaStudy/Chicken; L0 L2 0
    MAXSTACK = 2
    MAXLOCALS = 1
}
```

`ChickenContainer` 애노테이션을 생성하여 Chicken 애노테이션들을 value로 할당하는 것을 볼 수 있다.

`MethodHandles$Lookup`라는 이름의 클래스로 innerClass가 만들어지는 것을 볼 수 있는데 이는 메서드 핸들을 생성하기 위한 
factory class로 `ChickenContainer`의 value()인 Chicken[]에 접근하기 위한 class 이다.

# 배열 병렬 정렬

Arrays.parallelSort()가 추가되어 분산되어 정렬이 가능하다.

## 1. Arrays.parallelSort()

- Fork/Join 프레임워크를 사용해서 배열을 병렬로 정렬하는 기능을 제공한다.

## 2. 병렬 정렬 알고리즘

- 배열을 둘로 계속 쪼갠다.
- 합치면서 정렬한다.

## 3. sort()와 parallelSort() 비교

- 알고리즘 효츌성은 같다.
    - 시간복잡도 : O(n logN)
    - 공간복잡도 : O(n)

> **단, 정렬하는 배열의 크기에따라 속도가 차이날 수 있다.**

```java
public class App {
	public static void main(@Chicken String[] args) {
		int size = 1500;
		int[] numbers = new int[size];
		Random random = new Random();
		IntStream.range(0, size).forEach(i -> numbers[i] = random.nextInt());

		// 싱글 쓰레드 사용 (일반 정렬 시간 측정)
		long start = System.nanoTime();
		Arrays.sort(numbers);
		System.out.println("serial sorting took " + (System.nanoTime() - start));

		// 멀티 쓰레드 사용 (병렬 정렬 시간 측정)
		IntStream.range(0, size).forEach(i -> numbers[i] = random.nextInt());
		start = System.nanoTime();
		Arrays.parallelSort(numbers);
		System.out.println("parallel sorting took " + (System.nanoTime() - start));
	}
}
```

```bash
serial sorting took 1127801
parallel sorting took 612500
```

#### `참고`

size가 15000일 때 나오는 속도

```bash
serial sorting took 5023800
parallel sorting took 14654200
```

**Arrays.sort()**

- Arrays.sort() 알고리즘은 기본적으로 **DualPivotQuicksort**를 사용한다.

이 알고리즘은 기본적으로

1. Insertion Sort
2. Merge Sort
3. Quick Sort

위 3개의 알고리즘을 사용하는데 보통 1,2, 1,3을 섞어서 사용한다.

**Arrays.parallelSort()**

- 반면 **Arrays.parallelSort()** 는 Merge Sort를 사용함.

![img](https://user-images.githubusercontent.com/42997924/142432355-c0758fab-378e-461d-ba78-119768453867.png)

- in-place 정렬이 아니기 때문에 배열이 커지면 느려질 수 밖에 없다.
- [자세한 내용은 여길 참고하자](https://gmlwjd9405.github.io/2018/05/08/algorithm-merge-sort.html)

# Meta space

JVM의 여러 메모리 영역 중에 **PermGen 메모리 영역이 없어지고** **Metaspace** **영역이 생겼다.**

## 1. PermGen

- 메모리 구조

  ![memory](https://user-images.githubusercontent.com/42997924/142433257-9316af02-f095-4d9d-bd22-ce75ecb257e6.png)

[🔼출처](https://code-overflow.tistory.com/entry/%EC%9E%90%EB%B0%948-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%ACJava-8-Memory-Management-%EB%B3%80%ED%99%94?category=796033)

- permanent generation, **클래스 메타데이터**를 담는 곳.
- **Heap** 영역에 속함.
- 기본값으로 **제한된 크기**를 가지고 있음.
    - 동적으로 클래스를 생성하는 경우 PermGen 영역이 가득 차는 경우가 발생할 수 있다. 메모리 에러 발생.
    - 위의 문제를 근본적으로 해결하려면 PermGen 사이즈를 늘리는 것이아니라 클래스를 동적으로 생성하는 코드를 수정해야한다.
- XX:PermSize=N, PermGen 초기 사이즈 설정
- XX:MaxPermSize=N, PermGen 최대 사이즈 설정

> Old/Eden 영역보다 적은 메모리사이즈를 가지고 있으며 os bit, jdk 버전별로 다르다.   
> 고정된 크기를 가지기 때문에 클래스를 계속해서 만들게 되면 GC가 메모리를 정리해도 PermGen사이즈를 넘어서면 memory out 에러가 발생한다.

## 2. Metaspace

- 메모리 구조

  ![memory2](https://user-images.githubusercontent.com/42997924/142433263-79fb017a-97f5-4450-8b14-c470ccbe03c1.png)

[🔼출처](https://code-overflow.tistory.com/entry/%EC%9E%90%EB%B0%948-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%ACJava-8-Memory-Management-%EB%B3%80%ED%99%94?category=796033)

- **클래스 메타데이터**를 담는 곳.
- Heap 영역이 아니라, **Native 메모리 영역**이다.
- **기본값으로 제한된 크기를 가지고 있지 않다.**
    - **Native 메모리 영역이 가득찰 때까지 필요한 만큼 계속 늘어난다.**
    - Native 메모리 영역이 가득차게 되면 서버가 죽는 치명적인 문제가 발생할 수 있다.
    - 따라서 최소한 Metaspace의 최대 사이즈는 설정해주어야한다.
    - 효율적으로 사용하려면 모니터링 하여 최적의 Metaspace 값을 찾아 최대값으로 설정하는 것이다. 만약 모니터링으로 도출해낸 이 값에 메모리 문제가 생기면 어딘가의 메모리 누수를 의심해보아야 한다.
- 자바 8부터는 PermGen 관련 java 옵션은 무시한다.
- XX:MetaspaceSize=N, Metaspace 초기 사이즈 설정.
- XX:MaxMetaspaceSize=N, Metaspace 최대 사이즈 설정.

![memory-management](https://user-images.githubusercontent.com/42997924/142433643-30799d17-7afd-4e4f-8bb6-4bf9a079bd4c.png)

**Ref.**

- [https://code-overflow.tistory.com/entry/자바8-메모리-관리Java-8-Memory-Management-변화?category=796033](https://code-overflow.tistory.com/entry/%EC%9E%90%EB%B0%948-%EB%A9%94%EB%AA%A8%EB%A6%AC-%EA%B4%80%EB%A6%ACJava-8-Memory-Management-%EB%B3%80%ED%99%94?category=796033)
- [더 자바, Java 8, 백기선](https://www.inflearn.com/course/the-java-java8/dashboard)
- [PermGen Elimination project is promoting](http://mail.openjdk.java.net/pipermail/hotspot-dev/2012-September/006679.html)
- [[Java Memory Profiling에 대하여]① JVM 메모리 이해와 케이스 스터디](https://m.post.naver.com/viewer/postView.nhn?volumeNo=23726161&memberNo=36733075)
- [[Java Memory Profiling에 대하여]② 메모리 모니터링과 원인분석](https://m.post.naver.com/viewer/postView.nhn?volumeNo=24042502&memberNo=36733075)
- [Java 8: From PermGen to Metaspace](https://dzone.com/articles/java-8-permgen-metaspace)
- [One important change in Memory Management in Java 8](http://karunsubramanian.com/websphere/one-important-change-in-memory-management-in-java-8/)