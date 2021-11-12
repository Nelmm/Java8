> 정리 : [영준](https://github.com/jun108059)

**📚목차**
- [인터페이스의 변화](#인터페이스의-변화)
    - [1. 인터페이스란?](#1-인터페이스란)
        - [1-1. 추상클래스와 인터페이스 차이](#1-1-추상클래스와-인터페이스-차이)
        - [1-2. 인터페이스의 장점](#1-2-인터페이스의-장점)
        - [1-3. 인터페이스 특징](#1-3-인터페이스-특징)
        - [1-4. 추상클래스의 한계](#1-4-추상클래스의-한계)
        - [1-4-2. 해결하지 못한 문제](#1-4-2-해결하지-못한-문제)
    - [2. Java8 변화: 인터페이스의 기본 메소드와 스태틱 메소드](#2-java8-변화-인터페이스의-기본-메소드와-스태틱-메소드)
        - [2-1. Default method (기본 메소드)](#2-1-default-method-기본-메소드)
        - [2-2. Static method (정적 메소드)](#2-2-static-method-정적-메소드)
    - [3. 자바8 API의 기본 메소드와 스태틱 메소드](#3-8-api의-기본-메소드와-스태틱-메소드)
        - [3-1. Iterable](#3-1-iterable)
        - [3-2. Collection](#3-2-collection)
        - [3-3. Comparator](#3-3-comparator)

# 인터페이스의 변화

## 1. 인터페이스란?

자바의 `다형성`을 극대화하여 개발코드 수정을 줄이고 `유지보수`를 용이하게 하기 위함

> 다형성? 동일한 메시지를 수신했을 때 객체의 타입에 따라 다르게 응답할 수 있는 능력

### 1-1. 추상클래스와 인터페이스 차이

추상메서드를 가짐으로써 다형성을 극대화하면서 어떤 `역할`을 구현하는 방법(객체들이 따라야 하는 책임의 집합을 서술한 것)이라는 공통점이 있다.

하지만 **추상 클래스**는 `is-a`관계로 `-는 -이다.`의 개념이며 **책임의 일부를 구현**해놓은 것이고 **인터페이스**는 `has-a`의 관계로 `-는 -를 할 수 있다`의 개념으로 **책임의 집합**들을 나열해 놓은 것.

이때 이러한 관계가 객체지향에 있어 중요한 것은 객체의 속성이 아니라 **객체의 행동간의 관계**라는 것.

한마디로 `닭은 새다`, `맥은 os이다` 와 같은 문장이 있을때 이 문장들 앞에 `클라이언트 입장에서` 라는 말이 빠져있는 것이다. 실제 세계의 관점으로 바라보며 객체간의 관계를 정의하는 것이 아닌 이 객체들을 사용하는 클라이언트 입장에서 바라보며 특정 객체의 행동을 통해 관계를 맺어야 한다.

**서브 클래싱과 서브 타이핑**

- 서브 클래싱 : 다른 클래스의 코드를 재사용할 목적으로 상속을 사용하는 경우. 자식과 부모클래스간의 행동이 호환되지 않아 자식인스턴스가 부모인스턴스를 대체할 수없고 이를 `구현 상속` / `클래스 상속`이라고 부른다.
- 서브 타이핑 : 타입 계층을 구성하기 위해 상속을 사용하는 경우. 자식과 부모클래스간의 행동이 호환되어 인스턴스를 대체할 수 있다.

<img src = "https://user-images.githubusercontent.com/42997924/140612645-c9ff4d57-52c7-44bb-b70b-e5c5d44c3a10.png" width="50%" height="50%">

🔼 조영호님의 오브젝트 서적 이미지

> 💡 다음 스터디에서 [오브젝트](https://book.naver.com/bookdb/book_detail.naver?bid=15007773) 서적으로 공부해보면 좋은 내용 같아요.

### 1-2. 인터페이스의 장점

1. 개발 기간 단축
   - 팀내에서 서로 다른 부분을 개발할 때 인터페이스만 잘 정의해두어도 서로의 개발이 끝나지 않아도 따로 코드를 작성할 수 있다.
2. 클래스간의 낮은 결합도
   - 코드의 종속을 줄이고 유지보수성을 높일 수 있다.
3. 표준화 가능
   - 협업 시에도 일정한 틀의 형태로 개발을 할 수 있게 한다.
   
### 1-3. 인터페이스 특징

1. 필드로는 상수만 가능 (변수는 불가능 하다.) : 무조건 `public static final`로 선언 되며 생략 가능
2. 추상 메서드 존재 : 무조건 public abstract으로 선언되며 생략 가능

### 1-4. 추상클래스의 한계

추상클래스는 단일 상속을 원칙으로 한다.

> 상속의 가장 큰 문제점은 캡슐화를 위반한다는 것과 설계가 유연하지 않다는 것.  
> `코드의 재사용을 위해서는 상속이 아니라 합성`을 사용하는 것이 바람직하고 `상속은 타입계층을 구조화하기 위해 사용`해야 한다.  
> 의존성에 의한 영향이 적은 경우에도 추상화에 의존하고 의존성을 명시적으로 드러내는 것이 좋은 설계 습관이다.

#### 1-4-1. 메소드 오버라이딩 문제 해결했지만..

예를 들어, Person 인터페이스에서 메서드 `a(), b(), c(), d()` 를 제공한다고 하자.

이를 구현하는 Jun, Hong, Chang 클래스가 존재하고 각각 필요한 메서드는 하나씩 밖에 없는 상황이다.

<img src = "https://user-images.githubusercontent.com/42997924/140614888-d485e2bd-cb81-4a38-9f48-77f1f4380717.png" width="50%" height="50%">

필요한 메서드가 아니라도 인터페이스 특성상, 모든 메서드를 오버라이딩 해야만 하는 이슈가 있다.

Java8 이전에는 추상클래스를 활용하여 이 문제를 해결하였다.

<img src = "https://user-images.githubusercontent.com/42997924/140615064-bc7356bf-05cf-4801-b1b2-5e3ffcbfe32e.png" width="50%" height="50%">

### 1-4-2. 해결하지 못한 문제

- 매번 Adapter 추상클래스를 만들어야한다.
- 다중 상속이 불가능하다.

> 이러한 문제를 해결하기 위해 Java8 인터페이스에는 어떤 변화가 있었는지 알아보자.

## 2. Java8 변화: 인터페이스의 기본 메소드와 스태틱 메소드

> Java 8 부터는 인터페이스에도 메소드 선언뿐만 아니라 `default method` 와 `static method`를 통해 구현이 가능해졌다.

### 2-1. Default method (기본 메소드)

> 추상 메서드가 아니라 구현이 된 메서드를 제공하는 방법으로 코드의 중복을 피하고 기능을 추가할 때 사용

- `하위 호환성` 이 가장 큰 목적으로 어떤 인터페이스에 기능을 추가했을때 이를 상속, 구현한 클래스들이 깨지지 않게 하기위한 목적으로 default 메서드를 사용할 수 있다. 대표적으로 Collection의 of나 forEach 등이 있다.
- `람다`를 활용할 수 있는 기술 중 하나로 일종의 `무한한 데이터의 흐름`. 배열 또는 컬렉션, Map과 같은 자료구조들을 하나의 데이터 흐름으로 만듦으로서 함수 여러개를 chaning하여 코드의 양을 줄이고 간결하게 표현할 수 있게 해준다.

```java
public interface Foo {
  void printName();
}
```

```java
public class DefaultFoo implements Foo{
    @Override
    public void printName() {
        System.out.println("DefaultFoo");
    }
}
```

Foo 인터페이스를 구현하는 클래스가 많아지면, 새로운 기능의 메소드를 선언했이 추가됐을 때 구현체 모두 컴파일 에러가 발생.

이를 해결하기 위해, `Default Method` 등장!

```java
public interface Foo {
    void printName();
		// 기본 메소드(Default Method)
    default void printNameUpperCase(){
        System.out.println("FOO");
    }
}
```

```java
public static void main(String[] args){
      Foo foo = new DefaultFoo();
      foo.printName();
      foo.printNameUpperCase();
}
```

- `장점` 해당 인터페이스를 구현한 클래스를 깨트리지 않고 새 기능을 추가할 수 있음.
- `주의할 점` 기본 메소드는 구현체가 모르게 추가된 기능이기 때문에, 문제가 생길 수 있음.

```java
public interface Foo {
    void printName();  
    default void printNameUpperCase() {
        System.out.println(getName().toUpperCase());
    }
    String getName(); // Name 값이 항상 있다고 보장할 수 없다.                    
    // name = null -> NullPointException
}
```

- **컴파일 에러는 아니지만 구현체에 따라 런타임 에러 발생 가능**
- **반드시 문서화** (`@ImplSpec` 자바독 태그 사용)

```java
/**
 * @ImplSpec
 * 이 구현체는 getName()으로 가져온 문자열을 대문자로 변환 후 출력한다.
 */
default void printNameUpperCase() {
    // null 방지 하여 구현
    if(getName() == null) System.out.println("");
    System.out.println(getName().toUpperCase());
}
```

이래도 문제가 발생하는 경우는 구현 클래스에서 `Overriding` 을 이용해 재정의 할 수 있다.

- `주의할 점` **Object가 제공하는 기능**은 default 메소드로 제공할 수 없다.
  - `equals`, `hashCode`, `toString` 등
  - 구현체가 재정의 해야한다!
  - 인터페이스에서 추상 메서드로 선언하는 것은 괜찮다.
    - 모두 공통으로 제공하기 때문에 추상 메서드로 분류하지 않는다.
- 본인이 수정할 수 있는 인터페이스만 수정 가능하다.
- 인터페이스를 상속받는 인터페이스에서 다시 추상 메소드로 변경할 수 있다.


<img src = "https://user-images.githubusercontent.com/42997924/140614805-36fe0bb1-6e62-4fa3-853e-6e35d2b75140.png" width="50%" height="50%">


**`Diamond Problem`**
 
- 구현체가 상속받는 두 인터페이스 모두 동일한 기본 메소드가 있는 경우?

<img src = "https://user-images.githubusercontent.com/42997924/140612960-3e5fe0c3-2904-4278-87a9-9d5b165d2d7c.png" width="50%" height="50%">


```java
public interface Foo {
    default void getNameUpperCase() {
        ...
    }
}
public interface Bar extends Foo {
    void getNameUpperCase();  // 추상 메서드로 선언한 경우 구현 클래스에서 재정의 필수  
    // 추상 메서드로 선언 하지 않은 경우 상위 인터페이스 그대로 사용 가능
}
```

```java
public interface Foo {
    default void getNameUpperCase() {
        System.out.println("Foo");  
    }
}
public interface Bar {
    default void getNameUpperCase() {
        System.out.println("Bar");  
    }
}
public class Test implements Foo, Bar {
    ...
      // @Override  
      // public void printNameUpperCase() {  
      //    ...  
      // }
}
```

> `Error`: **DefaultFoo inherits unrelated defaults for printNameUpperCase() from types Foo and Bar**

- Bar, Foo 중 무엇을 사용할지 모르기 때문에 **컴파일 에러** 발생
- 충돌하는 default method 직접 override 해야한다.

### 2-2. Static method (정적 메소드)

> 인스턴스 없이 수행할 수 있는 행동을 정의 한 것

해당 인터페이스를 구현한 모든 인스턴스나 해당 타입에 관련된 헬퍼 또는 유틸리티 메소드를 제공할 때 인터페이스에 static method 제공 가능

```java
public interface Foo {
    void printName();

    default void hello(){
        System.out.println("Foo");
    }
    static void helloAll(){
        System.out.println("인삿말");
    }
}
```

```java
public class App {
    public static void main(String[] args) {
        Foo foo = new DefaultFoo();
        foo.hello();
    
        Foo.helloAll(); // static 메소드 사용
    }
}
```

**✅ 바이트 코드**

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

접근지정자를 생략하면 기본적으로 public이 붙게되고 java8에서는 static method가 public만 가능하지만 java9 이상부터는 private도 가능하다. 당연히 private이기 때문에 외부에서는 접근이 불가능하다.

**private 메서드의 조건**

- 구현부가 존재해야 한다.
- 해당 인터페이스 내부에서만 사용이 가능하다.


## 3. 자바8 API의 기본 메소드와 스태틱 메소드

JAVA8 에서 추가된 기본 메소드로 인해 API 에 변화에 대해 알아보자

### 3-1. Iterable

**[공식 문서](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)**

> 순회를 편하게 할 수 있다!  
> 대부분 혼자서 사용하는 경우는 거의 없고 Collection에 포함되어서 사용한다.

#### 3-1-1. forEach()

```java
public static void main(String[] args) {
    List<String> names = new ArrayList<>();
    names.add("yj");
    names.add("youngjun");
    names.add("spring");
    names.add("java8");

    names.forEach(System.out::println);

    // for (String name : names) {
    //    System.out.println(name);
    // }
}
```

1. `names.forEach(System.out::println)`
   - forEach 는 내부 엘리먼트를 순회하며 각각의 요소들을 파라미터로 전달된 일급함수에 Functional Interface인 Consumer가 들어오고 처리.
   - 단순 출력만 하기 때문에 메소드 레퍼런스 기능을 이용해 간결하게 작성!
2. `for-of{...}`
   - 기존에는 for-of 문을 사용해 출력이 가능하다고 함.
   - 하지만 조금이나마 더 간결한 forEach를 사용하는 것이 좋아 보임.

### 3-2. Collection

> `Iterable` 을 상속받는 인터페이스다!

**[공식 문서](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)**

#### 3-2-1. Spliterator

> iterator 와 비슷한 개념이지만 Collection 을 분할한다는 점에서 차이가 있음.

```java
public static void main(String[] args) {
    List<String> names = new ArrayList<>();
    names.add("yj");
    names.add("youngjun");
    names.add("spring");
    names.add("java8");

    Spliterator<String> spliterator = names.spliterator();
    while(spliterator.tryAdvance(System.out::println));
}
```

1. Spliterator<String> spliterator = names.spliterator();
   - iterator와 비슷하지만 분할할 수 있는 기능을 가진 Iterator 를 만들어 반환
2. spliterator.tryAdvance(System.out::println)
   - iterator의 hasNext()메소드와 유사
   - 다만 내부 메소드에 파라미터로 forEach()와 동일하게 Functional Interface인 Consumer가 들어오고 더 이상 들어올게 없을 경우 false를 반환
   - 이 예제에서는 들어온 값을 단순 출력하는 메소드 레퍼런스를 사용

> 여기까지는 iterator와 차이가 없다. trySplit을 사용해보자.

```java
public static void main(String[] args) {
    List<String> names = new ArrayList<>();
    names.add("yj");
    names.add("youngjun");
    names.add("spring");
    names.add("java8");

    Spliterator<String> spliterator = names.spliterator();
    Spliterator<String> trySplit = spliterator.trySplit();
    while(spliterator.tryAdvance(System.out::println));
    System.out.println("=========================");
    while(trySplit.tryAdvance(System.out::println));
}

/*
[실행 결과]
spring
java8
=========================
yj
youngjun
*/
```

1. `Spliterator<String> trySplit = spliterator.trySplit();`

   - spliterator에서 trySplit()메소드를 호출하게되면 해당 spliterator에서 앞에서부터 절반의 요소를 꺼내 새로운 spliterator를 만들어 반환

   <img src = "https://user-images.githubusercontent.com/42997924/139379819-9390b364-6120-4b51-a766-79b4a6d81e8e.png" width="50%" height="50%">

   🔼 trySplit() 실행 결과
2. `while(spliterator.tryAdvance(System.out::println));`

   - trySplit()을 통해 앞의 두 String(yj, youngjun)이 분할되어 빠져나갔기 때문에 뒤의 두 값만 출력
3. `while(trySplit.tryAdvance(System.out::println));`

   - spliterator 에서 가져온 앞 두 값(yj, youngjun)을 출력

#### 3-2-2. removeIf

> Collection 요소를 순회하며 인자로 넘겨주는 함수를 Functional Interface인 Predicate에 넘겨줘서 true 이면 삭제

```java
public static void main(String[] args) {
    List<String> names = new ArrayList<>();
    names.add("yj");
    names.add("youngjun");
    names.add("spring");
    names.add("java8");

    names.removeIf(s -> s.startsWith("y"));
    names.forEach(System.out::println);
}
```

1. `names.removeIf(s -> s.startsWith("y"));`
   - names를 순회하며 각 요소들 중 단어가 'y'로 시작하는 단어를 찾아 삭제

#### 3-2-3. etc

> 이후에 자세히 다룰 예정이다.

- Stream()
- parallelStream()

### 3-3. Comparator

[공식문서](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html)

> 정렬에 사용되는 인터페이스

#### 3-3-1. reversed

> 정렬을 역순으로!

```java
public static void main(String[] args) {
    List<String> names = new ArrayList<>();
    names.add("yj");
    names.add("youngjun");
    names.add("spring");
    names.add("java8");

    Comparator<String> compareToIgnoreCase = String::compareToIgnoreCase;
    names.sort(compareToIgnoreCase.reversed());
    names.forEach(System.out::println);
}
```

1. `Comparator<String> compareToIgnoreCase = String::compareToIgnoreCase`
   - 메소드 레퍼런스를 메소드 체이닝 방식으로 사용할수는 없기에 분리하여 Comparator 타입의 Functional Interface인 compareToIgnoreCase 를 만들어준다.
2. `names.sort(compareToIgnoreCase.reversed());`
   - 미리 선언해놓은 String 의 정렬 기준 메소드 레퍼런스에 `reversed()` 메소드를 호출해 역순으로 정렬

> ❓ 만약, 여기서 다음 정렬조건을 사용하여 정렬을 이어가고싶다면 thenComparing() 메소드를 이용하여 추가적인 정렬을 할 수 있다.

#### 3-3-2. static reverseOrder() / naturalOrder()
 
- 특정 타입의 Comparable이 지정되어있으면 일단 그 기준으로 역순이든 자연순이든 세팅해서 정렬함.

#### 3-3-3. etc

- static nullIsFirst() / nullIsLast()
- static comparing()

---

#### [2주차 다음 글 - Stream](README-AFTER.md)

---

**Reference**

- [백기선 인프런 강의 : 더 자바, Java8](https://www.inflearn.com/course/the-java-java8/dashboard)
- [Catsbi's DLog : 인터페이스의 변화](https://catsbi.oopy.io/3561609e-6c05-4209-a833-c5bb632b5aaa)
- https://github.com/gowoonsori/my-tech/blob/master/java/java8/interface.md
- https://github.com/gowoonsori/my-tech/blob/master/java/java8/stream.md
- https://dev-milk.tistory.com/5
- https://futurecreator.github.io/2018/08/26/java-8-streams-advanced/
- https://mangkyu.tistory.com/115