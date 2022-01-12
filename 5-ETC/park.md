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

#### 1-1-1. @Target 설정

> 타입에 애노테이션을 사용하기 위해 아래와 같은 ENUM 타입을 명시해야 함.

- `TYPE_PARAMETER` : 타입 변수에만 사용할 수 있다.
- `TYPE_USE` : 타입 변수를 포함해서 모든 타입 선언부에 사용할 수 있다.

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

### 1-2. 중복 사용할 수 있는 애노테이션을 만들기

- 중복 사용할 애노테이션 만들기
- 중복 애노테이션 컨테이너 만들기
    - 컨테이너 애노테이션은 중복 애노테이션과 `@Retention` 및 `@Target`이 같거나 더 넓어야 한다.

#### 1-2-1. 중복사용할 애노테이션 생성

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

        //2. 컨테이너 애노테이션 타입으로 읽는 방법
        ChickenContainer chickenContainer = App.class.getAnnotation(ChickenContainer.class);
        Arrays.stream(chickenContainer.value())
            .forEach(c -> System.out.println(c.value()));
    }
}
```

> `@Chicken` 은 default value가 없으면 에러가 발생한다.

# 배열 병렬 정렬

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

- permanent generation**, 클래스 메타데이터**를 담는 곳.
- **Heap** 영역에 속함.
- 기본값으로 **제한된 크기**를 가지고 있음.
    - 동적으로 클래스를 생성하는 경우 PermGen 영역이 가득 차는 경우가 발생할 수 있다. 메모리 에러 발생.
    - 위의 문제를 근본적으로 해결하려면 PermGen 사이즈를 늘리는 것이아니라 클래스를 동적으로 생성하는 코드를 수정해야한다.
- XX:PermSize=N, PermGen 초기 사이즈 설정
- XX:MaxPermSize=N, PermGen 최대 사이즈 설정

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