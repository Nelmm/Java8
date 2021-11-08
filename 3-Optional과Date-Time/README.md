# Optional

Java 8에 새로 생긴 인터페이스로 `라이브러리 메서드가 반환할 결과값이 없음을 명백하게 표현할 필요가 있는 곳에서 제한적으로 사용할 수 있는 메커니즘을 제공`하기 위해 새로 생겨났다.

Java api doc의 API 노트를 보면 다음과 같이 설명하고 있다. `Optional은 주로 결과 없음을 나타낼 필요성이 명확하고 null을 사용하면 오류가 발생할 수 있는 메소드 반환 유형으로 사용하도록 고안되었다.`

한마디로 **비어있을 수도 있고, 어떠한 값 하나만 담고 있을수도 있는 인스턴스의 타입**

<br>

## 등장 배경

### 1. 참조형 멤버변수 와 NPE

1. 런타임에 NPE(NullPointerException)라는 예외를 발생시킬 수 있다.
2. NPE 방어를 위해서 들어간 null 체크 로직 때문에 코드 가독성과 유지 보수성이 떨어진다.

```java
/* OnlineClass.java */
public Progress progress;
    public Progress getProgress() {
        return progress;
}

    public void setProgress(Progress progress) {
  	    this.progress = progress;
    }
}

/* OptionalTestApp.java */
OnlineClass spring_boot = new OnlineClass(1, "spring boot", true); // 이슈상황 → 참조형 멤버 변수 사용 시 초기화 되지 않아 null 값을 참조 할 수 있다.

Duration studyDuration = spring_boot.getProgress().getStudyDuration();  // NullPointExecption 발생
System.out.println(studyDuration);
```

모든 객체타입은 null을 갖을 수 있기 때문에 해당 값이 null인지 아닌지는 run time에 정확히 알지 못할 수가 있어 NPE를 발생시킬 여지가 있다.

<br>

#### Optional 등장 이전 해결방법

1. 사전에 null 체크

   ```java
   public Progress getProgress() {
       if (progress == null) {
   		throw new IllegalStateException();
       }
       return progress;
   }
   ```

   이는 에러에 대한 스택트레이스를 뽑게 되고 이또한 리소스 낭비가 될 수 있다.

2. 클라이언트 코드에서 null 체크

   ```java
   Progress progress = spring_boot.getProgress();
        if (progress != null) {
           System.out.println(progress.getStudyDuration());
        }
    }
   ```

   사용하는 시점에 null을 체크하는 방법도 존재하는데 이러면 get 메서드마다 null을 체크하는 코드를 작성해줘야 하고, 비즈니스로직보다 null 체크 코드가 더 길어져 가독성이 떨어질 수 있다.

이러한 문제들을 스칼라나 하스켈과 같은 함수형 언어들은 `존재할지 안 할지 모르는 값`을 표현할 수 있는 타입을 가지고 있고 자바도 처음 만들어졌을떄 `존재하지 않는 값`을 표현하기 위해 null을 만들었다면 이번에는 위 언어들의 컨셉을 모티브로 Optional API를 만들고 null 처리를 Optional에게 위임하는 방법으로 해결하고자 추가되었다.

<br>

## 장점

1. NPE를 유발할 수 있는 null을 직접 다루지 않아도 된다.
2. 수고롭게 null 체크를 직접 하지 않아도 된다.
3. 명시적으로 해당 변수가 null일 수도 있다는 가능성을 표현할 수 있다. (따라서 불필요한 방어 로직을 줄일 수 있다!)

<br>

## 주의사항 (sub. Optional 올바르게 사용하는 법)

### 1. Optional 변수에 null 대신 Optional.empty() 사용

```java
Optional<Car> optionalCar = null;

Optional<Car> optionalCar = Optional.empty();
```

Optional도 결국 객체이기 때문에 null이 들어갈 수 있는데 이는 또다른 NPE를 유발하는 코드이기 때문에 emtpy()메서드로 비어있는 값을 정의하자.

### 2. Optional 값을 꺼내쓰기 전에 값이 있는지 확인하기

```java
Optional<Car> optionalCar = Optional.empty();

Car car = optionalCar.get(); //NoSuchElementException 발생
```

Optional은 값이 비어있을 수도 있기 때문에 사용하기전에 존재한다는 것을 증명해야 하는데 일반적으로는 isPresent()후에 get()을 사용할 수 있지만 코드도 길어지고 한번에 사용할 수 있는 API를 제공하기 때문에 이를 사용하는 것이 바람직하다.

```java
//isPresent - get
if(optionalCar.isPresent()){
    return optionalCar.get();
}else {
    return null;
}

//orElse
return optionalCar.orElse(null);

//orElseGet
return optionalCar.orElseGet(() -> null);
```

이때 orElseGet()은 `Supplier`를 인자로 받으며, 값이 없을때에 해당 supplier가 수행된다. 하지만 orElse()는 Optional로 감싸고 있는 객체타입을 인자로 받으며 값이 있더라도 내부가 수행되고 사용되지 않는 경우 해당 객체를 지우게 되어 `필요없는 오버헤드가 발생`한다.

```java
//source code
Optional<String> optStr = Optional.of("Hi");
optStr.orElse(new String("hi1"));
optStr.orElseGet(() -> new String("hi1"));

//bytecode
 L1
    LINENUMBER 9 L1
    ALOAD 1
    NEW java/lang/String
    DUP
    LDC "hi"
    INVOKESPECIAL java/lang/String.<init> (Ljava/lang/String;)V
    INVOKEVIRTUAL java/util/Optional.orElse (Ljava/lang/Object;)Ljava/lang/Object;
    POP
L2
    LINENUMBER 11 L2
    ALOAD 1
    INVOKEDYNAMIC get()Ljava/util/function/Supplier; [
      // handle kind 0x6 : INVOKESTATIC
      java/lang/invoke/LambdaMetafactory.metafactory(Ljava/lang/invoke/MethodHandles$Lookup;Ljava/lang/String;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodType;Ljava/lang/invoke/MethodHandle;Ljava/lang/invoke/MethodType;)Ljava/lang/invoke/CallSite;
      // arguments:
      ()Ljava/lang/Object;,
      // handle kind 0x6 : INVOKESTATIC
      study/OptionalEx.lambda$main$0()Ljava/lang/String;,
      ()Ljava/lang/String;
    ]
    INVOKEVIRTUAL java/util/Optional.orElseGet (Ljava/util/function/Supplier;)Ljava/lang/Object;
    POP

    ...
private static synthetic lambda$main$0()Ljava/lang/String;
 L0
    LINENUMBER 11 L0
    NEW java/lang/String
    DUP
    LDC "hi"
    INVOKESPECIAL java/lang/String.<init> (Ljava/lang/String;)V
    ARETURN
    MAXSTACK = 3
    MAXLOCALS = 0
}

```

예를 들어 위와 같은 코드를 작성했을때 optStr은 null이 아니라 new String("hi1")가 실행되지 않을 것 같지만 바이트코드를 보면 새로 문자열을 생성했다가 POP하는 것을 볼 수 있고, orElseGet()은 우리가 lambda에서 봤던것처럼 static 메서드로 생성하여 호출되는 타이밍에 이를 실행해 객체를 생성하는 것을 볼수있어 orElse는 필요없는 오버헤드에 주의해야한다. 하지만 반드시 이미 생성되어있는 객체를 반환하는 것이라면 orElse()를 사용하는 것이 좋을 수도 있다.

<br>

```java
//before
if(optionalCar.isPresent()){
    return optionalCar.get();
}else {
    throw new NoSuchElementException();
}

//after
optionalCar.orElseThrow();
```

예외처리를 하는 경우에도 isPresent()를 통한 예외처리보다는 `orElseThrow()`를 이용하여 예외처리를 하는 것이 바람직하다. 인자로 `Supplier`를 통해 특정 Exception을 던질 수 있는데 아무것도 주지 않으면 기본적으로 NoSuchElementException을 던진다.

### 3. Optional이 있을때만 이를 소비하여 무언가를 할때는 isPresent()가 아닌 ifPresent()를 활용

```java
//before
if(optionalCar.isPresent()){
    System.out.println(optionalCar.get());
}

//after
optionalCar.ifPresent(System.out::println);
```

이도 orElse()와 같은 메서드들과 같은 이유로 별도로 API를 제공하기 때문에 필요없는 조건절을 추가하지 말고 API를 활용해보자.

### 4. 컬렉션은 Optional대신 비어있는 컬렉션을 사용하자.

```java
//before
List<Car> cars = carFactory.getCars();
return Optional.ofNullable(cars);

//after
List<Car> cars = carFactory.getCars();
return cars != null ? cars : Collections.emptyList();
```

컬렉션들은 자체적으로 비어있는 값을 표현할 수 있는 자료구조이다. 때문에 Optional을 사용할 필요가 없다.

### 5. 컬렉션은 Optional로 감싸지말자.

컬렉션 자체가 데이터를 감싼 형태의 객체이고 이도 충분한 API를 제공하기 때문에 한번더 Optional로 감싸면 필요없는 오버헤드가 발생한다. JPA의 메서드를 생성할때도 JPA자체적으로 비어있는 컬렉션을 반환해주므로 Optional로 감쌀 필요가 없다.

```java
//bad
Optional<List<Car>> findAllByCompanyName(String name);

//good
List<Car> findAllByCompanyName(String name);
```

### 6. 컬렉션,Map의 원소로 Optional을 사용하지 말자.

컬렉션이나 Map에 들어가는 값들은 애초에 null이 아님을 전제조건으로 만들어진 자료구조이기 때문에 Optional을 사용할 필요가 없다.

### 7. 단일 값을 얻기 위한 목적으로 메서드 체이닝을 하지말자.

```java
//bad
String status = "on";
return Optional.ofNullable(status).orElse("PENDING");

//good
status == null ? "PENDING" : status;
```

Optional을 사용하는 것은 결국 또하나의 래퍼객체를 사용하는 것이므로 단순한 로직이라면 그냥 코딩하자. 구관이 명관이라했다.

### 8. Optional을 필드로 사용하지말자.

```java
public class Car {
    private Optional<Navigation> navigation = Optional.empty(); //bad
}
```

Optional은 애초에 필드로 사용할 목적으로 만들어지지 않아 Serializable도 구현하지 않았기 때문에 사용을 지양해야 한다.

### 9. Optional을 메서드,생성자 인자로 사용하지말자.

```java
//bad
public void render(Optional<Renderer> renderer){
    renderer.orElseThrow(() -> new IllegalArgumentException("null 일 수 없습니다."));
    //...
}

//good
public void render(Renderer renderer){
    if(renderer == null){
        throw new IllegalArgumentException("null 일 수 없습니다.");
    }
    //...
}
```

이러한 방법은 불필요하게 코드를 복잡하게 할 뿐아니라 이를 호출하는 쪽에서도 Optional 생성을 강제하게 하는 것이다. 또한, Optional은 하나의 객체로 이를 호출하는 것이 결코 비용이 저렴하지 않다.

### 10. null이 확실하면 ofNullable()이 아닌 of()를 사용하자.

```java
//bad
Optional.ofNullable("NULL일 수 없지!");

//good
Optional.of("NULL일 수 없지!");
```

```java
//ofNullable
public static <T> Optional<T> ofNullable(T value) {
        return value == null ? empty() : of(value);
}
```

ofNullable은 내부적으로 보면 삼항연산자를 통해 비어있지 않는 경우 of를 호출하는 것을 볼 수있다. 그렇기 때문에 이러한 연산을 조금이라도 줄일 수 있기 때문에 of를 사용하자.

### 11. Optional의 타입이 Primitive타입이면 OptionalInt,OptionalLong, OptionalDouble을 사용하자

```java
//bad
Optional<Integer> max = Optional.of(10);
for(int i=0; i < max.get(); i++)

//good
OptionalInt max = OptionalInt.of(10);
for(int i=0; i < max.getAsInt(); i++)

//byte code
L0
    LINENUMBER 10 L0
    BIPUSH 10
    INVOKESTATIC java/lang/Integer.valueOf (I)Ljava/lang/Integer;
    INVOKESTATIC java/util/Optional.of (Ljava/lang/Object;)Ljava/util/Optional;
    ASTORE 0
L1
    LINENUMBER 12 L1
    BIPUSH 10
    INVOKESTATIC java/util/OptionalInt.of (I)Ljava/util/OptionalInt;
    ASTORE 1
```

내부적으로 Integer.valueOf()를 통해 한번 boxing이 일어나는 것을 볼 수 있고 또 이를 사용할때 unboxing이 일어나기 때문에 OptionalInt를 사용하는 것이 좋다.

### 12. Optional을 리턴하는 메서드에서 null을 리턴하지 말자.

```java
public static Optional<String> hi(){
        return null;
}
```

당연한거지만 Optional도 객체이기 때문에 null을 리턴이 가능한데 이렇게 리턴하게 되면 Optional을 사용하는 것이 의미가 없기 때문에 null을 리턴하지 말자.

<br><br>

## Optional API

### 1. Optional 생성

1. 선언하기

   제네릭을 제공하기 때문에, 변수를 선언할 때 명기한 타입 파라미터에 따라서 감쌀 수 있는 객체의 타입이 결정된다.

   ```java
   Optional<Order> maybeOrder; // Order 타입의 객체를 감쌀 수 있는 Optional 타입의 변수
   Optional<Member> optMember; // Member 타입의 객체를 감쌀 수 있는 Optional 타입의 변수
   Optional<Address> address; // Address 타입의 객체를 감쌀 수 있는 Optional 타입의 변수
   ```

   변수명은 그냥 클래스 이름을 사용하기도 하지만 `maybe`나 `opt`와 같은 접두어를 붙여서 Optional 타입의 변수라는 것을 좀 더 명확히 나타내기도 한다.

2. 객체 생성하기

   - Optional.empty() : null을 담고 있는, 한 마디로 비어있는 Optional 객체를 얻어온다.

     ```java
     Optional<Member> maybeMember = Optional.empty();
     ```

     이 비어있는 객체는 Optional 내부적으로 미리 생성해놓은 싱글턴 인스턴스이다.

   - Optional.of() : null이 아닌 객체를 담고 있는 Optional 객체를 생성

     ```java
     Optional<Member> maybeMember = Optional.of(aMember);
     ```

     null이 넘어올 경우, NPE를 던지기 때문에 **주의해서 사용하자!**

   - Optional.ofNullable() : null인지 아닌지 확신할 수 없는 객체를 담고 있는 Optional 객체를 생성

     ```java
     Optional<Member> maybeMember = Optional.ofNullable(aMember);
     Optional<Member> maybeNotMember = Optional.ofNullable(null);
     ```

     `Optional.empty()`와 `Optional.ofNullable(value)`를 합쳐놓은 메소드로 이해하면 편하다. null이 넘어올 경우, NPE를 던지지 않고 `Optional.empty()`와 동일하게 비어 있는 Optional 객체를 얻어온다.

     해당 객체가 null인지 아닌지 자신이 없는 상황에서는 이 메소드를 사용하자.

### 2. Optional 값 여부 확인

1. isPresent() : 값이 있으면 true, 없으면 false
2. isEmpty() : `Java11부터 제공`하며 값이 없으면 true, 값이 있으면 false

### 3. Optional 값 가져오기

아래 메소드들은 모두 Optional이 담고 있는 객체가 존재할 경우 동일하게 해당 값을 반환하지만, Optional이 비어있는 경우(null을 담고 있는 경우), 다르게 작동한다.

1. get() : null일 경우 NoSuchElementException 발생히고 `가급적 사용을 하지 않는 것을 권장한다.`
1. orElse(T other) : null일 경우 넘어온 인자(T) 를 반환한다.
1. orElseGet(Supplier<? extends X>) : null일 경우 넘어온 함수형 인자를 통해 생성된 객체를 반환한다.

   `orElse(T other)`의 게으른 버전으로 비어있는 경우에만 함수가 호출되기 때문에 `orElse(T other)` 대비 성능상 이점을 기대할 수 있다.

1. orElseThrow(Supplier<? extends X> exceptionSupplier) : null일 경우 넘어온 함수형 인자를 통해 생성된 예외(를 던진다. (default : NoSuchElementException)
1. ifPresent(Consumer) : 값이 있는 경우 값을 가지고 Consumer 함수 동작한다.

   ```java
   // 1. get()
   OnlineClass onlineClass = optional.get(); // NoSuchElementException 발생

   // 2. isPresent() + get() = 먼저 확인후 꺼낸다  번거롭다
   if (optional.isPresent()) {
   	OnlineClass onlineClass = optional.get();
   	System.out.println(onlineClass.getTitle());
   }

   // 3. ifPresent(Consumer) = 값이 있는 경우만 함수가 동작한다!
   optional.ifPresent(oc -> System.out.println(oc.getTitle()));
   ```

### 4. Optional 필터/변환

1. Optional map(Function)

   ```java
    // AS-WAS
    //주문을 한 회원이 살고 있는 도시를 반환하는 메소드다. (기본값은 Seoul 이다.)
    public String getCityOfMemberFromOrder(Order order) {
        if (order == null) {
            return "Seoul";
        }
        Member member = order.getMember();
        if (member == null) {
            return "Seoul";
        }
        Address address = member.getAddress();
        if (address == null) {
            return "Seoul";
        }
        String city = address.getCity();
        if (city == null) {
            return "Seoul";
        }
        return city;
    }

    // TO-BE
    public String getCityOfMemberFromOrder(Order order) {
        return Optional.ofNullable(order)
                .map(Order::getMember)
                .map(Member::getAddress)
                .map(Address::getCity)
                .orElse("Seoul");
    }
   ```

   기존의 방식으로 null check를 하면서 코드를 작성하면 사방에서 return 해줘야 하여 가독성이 떨어지고, 유지보수가 좋지 않은 코드가 있다.

   하지만 Optional과 map을 이용하면 전통적인 NPE 방어 패턴에 비해 훨씬 간결하고 명확해진다!

   우선 기존에 존재하던 조건문들이 모두 사라지고 Optional의 수려한(fluent) API에 의해서 단순한 메소드 체이닝으로 모두 대체된다.

   - **메소드 체이닝 설명**
     1. `ofNullable()` 정적 팩토리 메소드를 호출하여 Order 객체를 Optional로 감싸고 혹시 Order 객체가 null인 경우를 대비하여 `of()` 대신에 `ofNullable()`을 사용하는 것이다.
     1. 3번의 `map()` 메소드의 연쇄 호출을 통해서 Optional 객체를 3번 변환한다. 매 번 다른 메소드 레퍼런스를 인자로 넘겨서 Optional에 담긴 객체의 타입을 바꿔준다.
     1. 마무리 작업으로 `orElse()` 메소드를 호출하여 이 전 과정을 통해 얻은 Optional이 비어있을 경우, 디폴트로 사용할 도시 이름을 세팅해주면 된다.

2. Optional filter(Predicate)

   ```java
   if (obj != null && obj.do() ...)
   ```

   Java8 이 전에 NPE 방지를 위해서 위와 같이 null 체크로 시작하는 if 조건문 패턴을 많이 사용해왔고 이러한 패턴을 이용해서 주어진 시간(분) 내에 생성된 주문을 한 경우에만 해당 회원 정보를 구하는 메소드를 작성해보면 아래와 같다.

   ```java
   public Member getMemberIfOrderWithin(Order order, int min) {
       if (order != null && order.getDate().getTime() > System.currentTimeMillis() - min * 1000) {
           return order.getMember();
       }
   }
   ```

   위 코드의 문제점은 두가지가 존재하게 된다. 첫번째로 if 조건문 내에 **null 체크와 비지니스 로직이 혼재**되어 있어서 가독성이 떨어진다는 점이다. 두번째로는 null을 리턴할 수 있기 때문에 메소드 호출부에 NPE 위험을 전파하고 있다는 것이다.

   <br>

   이런 문제점 해결하고자 filter를 적용하면 아래와 같이 코드를 작성 할 수 있다.

   ```java
   public Optional<Member> getMemberIfOrderWithin(Order order, int min) {
       return Optional.ofNullable(order)
               .filter(o -> o.getDate().getTime() > System.currentTimeMillis() - min * 1000)
               .map(Order::getMember);
   }
   ```

   Optional과 filter를 이용하면 if 조건문 없이 메소드 연쇄 호출만으로도 좀 더 읽기 편한 코드를 작성할 수 있을 뿐만 아니라, 메소드의 리턴 타입을 Optional로 사용함으로써 호출자에게 해당 메소드가 null을 담고 있는 Optional을 반환할 수도 있다는 것을 명시적으로 알려준다.

3. Optional flatMap(Function) : Optional 안의 인스턴스가 Optional인 경우 사용하면 편리하며 Stream에서 사용하는 경우와 비슷하게 Optional을 한번 분리해서 쪼개주는 걸 뜻한다.

<br><br><br>

# Date / Time

> 날짜와 시간을 표현하기 위해 Java에서 사용해왔다.

## 등장 배경

### 1. 명확하지 않은 클래스 이름

- 날짜 클래스중 `Date` 는 `시간`과 `TimeStamp` 모두 표현할 수 있다. (사실상 `TimeStamp`)
- 시간 값이 `에폭타임` 이라 하여 세계 표준시(UTC)로 1970년 1월 1일 00시 00분 00초를 기준으로 현재까지 흐른 모든 시간을 초(sec)단위로 표현 하여 사람이 알아보기 어렵다.

### 2. Thread safe하지 않은 mutable한 속성

```java
public static void main(String[] args) throws InterruptedException {
    Date date = new Date();
    long time = date.getTime();
    System.out.println("date = " + date);
    Thread.sleep(1000 * 3);
    Date after3Seconds = new Date();
    System.out.println("after3Seconds = " + after3Seconds);

    after3Seconds.setTime(time);
    System.out.println("after3Seconds = " + after3Seconds);
}

/*
   [실행 결과]
   date = Thu Oct 28 20:22:24 KST 2021
   after3Seconds = Thu Oct 28 20:22:27 KST 2021
   after3Seconds = Thu Oct 28 20:22:24 KST 2021
*/
```

새로 생성한 after3Sceconds 라는 객체가 setter를 통해 다른 시간으로 변경이 된 것을 볼 수 있는데, 이는 Date 클래스가 **`mutable`** 하다는 것을 의미한다. mutable하기 때문에 **`thread unsafe`** 하다.

> thread unsafe? Date 인스턴스의 값을 각각 다른 Thread에서 접근해서 변경이 가능하면 기존에 사용하던 Thread에서 변경 되어 잘못된 Date 정보를 가져와서 버그가 발생할 위험이 있다는 뜻.

### 3. 버그 발생할 여지가 많다.

사용법 자체에서도 사용법에 대해 오해할 수 있어 버그가 발생할 여지가 있다.

```java
Calendar birthDay = new GregorianCalendar(1988, 6, 10);
```

위 코드를 보면 생일이 1988년 6월 10일임을 표현하고 싶지만, GregorianCalendar에서 month는 0부터시작하기 때문에 6을 넣으면 7월이라는 의미가 된다. 그래서 혼동하지 않기 위해서 상수 값을 쓰곤 했다. `(ex: Calendar.JUNE)`

하지만 이도 임시 방편이었고 암묵적으로 Java 8 이전에는 [Joda-Time](https://www.joda.org/joda-time/) 을 사용했었다.

<br>

## 디자인 철학

### 1. Clear

- API 메소드는 명확해야한다.
- 예를들어 클래스명이 `Date`지만 날짜 뿐 아니라 시간(`Time`)까지 다루게되면 명확하지 않다.
- 시간(`Time`)역시 사람에게 익숙한 일반 시간이 아닌 에폭타임인 것은 `Clear`하지 않다.

### 2. Fluent

메소드가 null을 반환하는 경우가 없기 때문에 메소드 체이닝이 가능하기에 코드가 유려해진다.

### 3. Immutable

기존 날짜 인스턴스의 내용이 변하지 않으며 변경메소드 호출시 값이 변경되는게 아니라 새로운 날짜 인스턴스를 생성해 반환해야한다.

```java
LocalDate today = LocalDate.of(2021, Month.OCTOBER, 28);
LocalDate nextMonth = today.plusMonths(1);

//LocalDateTime의 plusDays() 메서드
public LocalDateTime plusDays(long days) {
    LocalDate newDate = date.plusDays(days);
    return with(newDate, time);
}

private LocalDateTime with(LocalDate newDate, LocalTime newTime) {
    if (date == newDate && time == newTime) {
            return this;
    }
    return new LocalDateTime(newDate, newTime);
}
```

Date/Time의 모든 객체는 mutable한 속성의 단점을 해결하고자 Immutable한 속성을 갖고 설계가 되었는데 이 때문에 메서드를 이용해 날짜,시간을 변경하면 위에 정의된 with()함수를 사용하게 되고 with()함수는 새로운 객체를 만들어 반환하고 있는 것을 볼 수 있다.

### 4. Extensible

- 확장 가능해야 한다.
- 달력에는 윤달, 음력, 양력, 불교달력 등 다양한 종류의 달력의 확장이 가능해야 한다.

<br>

## 기존 API와 추가된 API

### 기존 API

- java.util.Date
- java.util.Calendar
- java.text.SimpleDateFormat

### 추가된 API

- java.time.Instant : 기계시간
- java.time.LocalDate : 날짜(시간x), 타임존x
- java.time.LocalTime : 시간(날짜x), 타임존x
- java.time.LocalDateTime : 날짜/시간, 타임존x
- java.time.ZonedDateTime : 날짜/시간, 타임존o
- java.time.DateTimeFormatter
- java.time.Duration
- java.time.Period
- java.time.TemporalAdjuster

<br>

## API 사용 예

### 1. Instant

```java
Instant instant = Instant.now();
System.out.println(instant); //2021-11-02T14:07:19.218304100Z  (기준시 UTC 기준)

ZonedDateTime zonedDateTime = instant.atZone(ZoneId.systemDefault());
System.out.println(instant); //2021-11-02T23:08:45.188601700+09:00[Asia/Seoul]  (현재 KTC 기준)
```

- Instant.now(): 현재 UTC(GMT)를 반환 (`Universal Time Coordinated == Greenwich Mean Time`)
- ZoneId.systemDefault() : 현재 시스템상의 zone 정보를 반환 (ex: Asia/Seoul)
- instant.atZone(zone) : UTC 기준이아닌 zone 의 위치 기반의 시간을 반환

### 2. LocalDate/LocalTime/LocalDateTime/ZonedDateTime

```java
LocalDateTime now = LocalDateTime.now();    //서버의 시스템 zone 기준
System.out.println(now);

LocalDateTime of = LocalDateTime.of(1982, Month.JULU, 15,0,0,0);

ZonedDateTime nowInKorea = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));
System.out.println(nowInKorea);
```

보통 사람이 읽고 쓰기 편한 시간 표현방식으로 표현해주는 API

- LocalDateTime.now() : 현재 시스템 Zone에 해당하는(로컬) 일시를 반환
- LocalDateTime.of(1988, Month.JUNE, 10, 0, 0, 0); : 로컬의 특정 일시를 반환
- ZonedDateTime.now(ZoneId.of("UTC")) : 특정 Zone의 현재 시간을 반환합니다.
- ZonedDateTime.of(1988, Month.JUNE.getValue(),10,0,0,0,0, ZoneId.of("UTC")) : 특정 Zone의 특정 일시를 반환합니다.

### 3. Duration / Period

```java
//Period
LocalDate today = LocalDate.now();
LocalDate thisYearBirthDay = LocalDate.of(2022, Month.FEBRUARY,7);

Period period = Period.between(today, thisYearBirthDay);
System.out.println("생일까지 남은 기간 : " + period.getYears() + " 년 " + period.getMonths() + "월 " + period.getDays() + "일" );  //생일까지 남은 기간 : 0 년 3월 5일

Period p = today.until(thisYearBirthDay);
System.out.println("생일까지 남은 기간 : " + p.getYears() + " 년 " + p.getMonths() + "월 " + p.getDays() + "일" );  //생일까지 남은 기간 : 0 년 3월 5일

//Duration
Instant now = Instant.now();
Instant plus = now.plus(10,ChronoUnit.SECONDS);
Duration between = Duration.between(now,plus);
System.out.println(between.getSeconds());   //10
```

Period는 사람이 사용하는 날짜/시간의 기간을 측정, Duration은 초단위(나노,밀리)로 반환을 하기 때문에 주로 기계용 시간간의 기간을 측정하는데 사용할 수 있다.

<br>

#### 시간 비교시 유용한 방식

위의 방식은 대부분 시간 메소드를 어떤식으로 사용해야하는 지에 대해서 이야기하는게 대부분인데, 실제로 제일 필요한 건 시간 비교가 제일 유용할 것 같아서 좀 더 정리해본다.

```java
LocalDateTime time1 = LocalDateTime.of(2021, 11, 3, 10, 18, 0);
LocalDateTime time2 = LocalDateTime.of(2021, 11, 3, 10, 19, 0);

public boolean isAfter(ChronoLocalDate other)  //주어진날짜가 other보다 크면.. True

System.out.println(time1.isAfter(time2)); //false
System.out.println(time2.isAfter(time1)); //true

public boolean isBefore(ChronoLocalDate other) //주어진날짜가 other보다 작으면.. True

System.out.println(time1.isBefore(time2)); //true
System.out.println(time2.isBefore(time1)); //false

public boolean isEqual(ChronoLocalDate other) //주어진날짜가 other보다 같으면.. True

public int compareTo(ChronoLocalDate other)  //주어진날짜가 other보다 같으면 0, 크면 + 작으면 -
System.out.println(time1.compareTo(time2)); //-1
System.out.println(time2.compareTo(time1)); // 1
//날짜를 하루정도로 바꿔서 변환해보자 11/3 11/4 시간은 동일하게
System.out.println(time1.compareTo(time2)); //-1
System.out.println(time2.compareTo(time1)); // 1

public LocalDateTime truncatedTo(TemporalUnit unit)
```

isAfter,isBefore과 같은 메서드를 통해 비교를 수행할수 있다.

이때 `truncatedTo()` 메서드를 이용해서 시간을 잘라 내고 날짜만으로 비교가 가능한데 trucatedTo()메서드는 파라미터로 지정된 단위 이후의 값들을 버린 후, 복사한 LocalDateTime 객체를 리턴하는 메서드이다. 파라미터로 전달되는 단위는 ChronoUnit 클래스에 지정된 상수를 사용하며, DAYS보다 큰 단위인 YEARS, MONTHS 등의 값은 허용되지 않는다.

```java
LocalDateTime time1 = LocalDateTime.of(2021, 11, 3, 10, 18, 3);
System.out.println(time1.truncatedTo(ChronoUnit.SECONDS)); //2021-11-03T10:18:03
System.out.println(time1.truncatedTo(ChronoUnit.MINUTES)); //2021-11-03T10:18
System.out.println(time1.truncatedTo(ChronoUnit.HOURS)); //2021-11-03T10:00
System.out.println(time1.truncatedTo(ChronoUnit.DAYS)); //2021-11-03T00:00
```

현 기준 날짜로 절삭 해서 사용가능히고 현 시간을 시점으로 + -도 ChronoUnit으로 더 효율적으로 할 수 있다.

```java
LocalDateTime time1 = LocalDateTime.of(2021, 11, 3, 10, 18, 3);
LocalDateTime time2 = LocalDateTime.of(2021, 11, 4, 10, 18, 0);

//3일이 더해짐.
System.out.println(time1.plus(3, ChronoUnit.DAYS)); //2021-11-06T10:18:03
//3일이 빠짐.
System.out.println(time1.minus(3, ChronoUnit.DAYS)); //2021-11-06T10:18:03
//구체적으로 3년이 이런식으로 추가도 가능하다.
System.out.println(time1.plusYears(3));//2024-11-03T10:18:03
//빼는것도 ㅆㄱㄴ
System.out.println(time1.minusYears(3)); //2018-11-03T10:18:03

//아니면 이런식으로 사용할수도 있다.
ChronoUnit.HOURS.between(time1, time2); //23 (23.x 분 차이이기 때문에)
```

<br>

#### truncatedTo가 날짜부터는 자를 수 없는 이유

일수부터는 생략한다는 개념이 모호하다.예를 들어, day에 0이 오는것도 말이 안되며 1이 온다고 해도 일수를 잘라내 정확히 년,월을 뜻하는게 아니라 년,월,1일을 뜻하는 것이기 때문에 매개변수로 올 수 가 없는 것이다.

하지만 해당해의 1일을 만약에 표시하고 싶을경우에는 `TemporalAdjusters(시간 조정기)`를 이용할 수 있다.

```java
date.with(TemporalAdjusters.firstDayOfMonth()).truncatedTo(ChronoUnit.DAYS); //2021-11-01T00:00
date.with(TemporalAdjusters.firstDayOfYear()) : 해당 년도의 1월 1일 //2021-01-01T17:02:22.973160900
date.with(TemporalAdjusters.firstDayOfMonth()) : 해당 월의 1일 //2021-11-01T17:03:05.777745100
date.with(TemporalAdjusters.lastDayOfYear()) : 해당 년도의 마지막 날짜 //2021-12-31T17:03:34.531656400
date.with(TemporalAdjusters.lastDayOfMonth()) : 해당 월의 마지막 날짜 //2021-11-30T17:04:03.837483400
```

<br>

#### ChronoUnit

![ChronoUint-Enum.png](img/1.png)

### 4. DateTimeFormatter

```java
//formatting
LocalDateTime now = LocalDateTime.now();

DateTimeFormatter formatter = DateTimeFormatter.ISO_DATE_TIME;
System.out.println(now.format(formatter));      //2021-11-02T23:28:51.388655

formatter = DateTimeFormatter.ofPattern("MM.dd.yyyy");
System.out.println(now.format(formatter));      //11.02.2021
```

DateTimeFormatter은 ofPattern()을 이용해 특정 패턴을 지정할 수 있고 미리 정의된 포맷터들이 존재하는데 이 형식을 이용하고자하면 굳이 새로 정의해줄 필요가 없다. 정의된 포맷터들은 [여기](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html)를 참고하자.

이때, Formatter에 사용되는 형식은 다음처럼 사용 가능하다.

```
  Symbol  Meaning                     Presentation      Examples
  ------  -------                     ------------      -------
   G       era                         text              AD; Anno Domini; A
   u       year                        year              2004; 04
   y       year-of-era                 year              2004; 04
   D       day-of-year                 number            189
   M/L     month-of-year               number/text       7; 07; Jul; July; J
   d       day-of-month                number            10

   Q/q     quarter-of-year             number/text       3; 03; Q3; 3rd quarter
   Y       week-based-year             year              1996; 96
   w       week-of-week-based-year     number            27
   W       week-of-month               number            4
   E       day-of-week                 text              Tue; Tuesday; T
   e/c     localized day-of-week       number/text       2; 02; Tue; Tuesday; T
   F       week-of-month               number            3

   a       am-pm-of-day                text              PM
   h       clock-hour-of-am-pm (1-12)  number            12
   K       hour-of-am-pm (0-11)        number            0
   k       clock-hour-of-am-pm (1-24)  number            0

   H       hour-of-day (0-23)          number            0
   m       minute-of-hour              number            30
   s       second-of-minute            number            55
   S       fraction-of-second          fraction          978
   A       milli-of-day                number            1234
   n       nano-of-second              number            987654321
   N       nano-of-day                 number            1234000000

   V       time-zone ID                zone-id           America/Los_Angeles; Z; -08:30
   z       time-zone name              zone-name         Pacific Standard Time; PST
   O       localized zone-offset       offset-O          GMT+8; GMT+08:00; UTC-08:00;
   X       zone-offset 'Z' for zero    offset-X          Z; -08; -0830; -08:30; -083015; -08:30:15;
   x       zone-offset                 offset-x          +0000; -08; -0830; -08:30; -083015; -08:30:15;
   Z       zone-offset                 offset-Z          +0000; -0800; -08:00;

   p       pad next                    pad modifier      1

   '       escape for text             delimiter
   ''      single quote                literal           '
   [       optional section start
   ]       optional section end
   #       reserved for future use
   {       reserved for future use
   }       reserved for future use
```

<br>

```java
LocalDateTime now = LocalDateTime.now();
formatter = DateTimeFormatter.ofPattern("MM.dd.yyyy");
System.out.println(now.format(formatter));      //11.02.2021

//parsing
LocalDate parse = LocalDate.parse("08.12.2021",formatter);
System.out.println(parse);
```

또한, Date/Time타입의 parse()메서드를 통해서 특정 문자열을 `ofPattern`에서 선언한 패턴 방식으로 파싱하여 `LocalDate` 타입의 인스턴스를 생성해 반환할 수도 있다.

### 5. 레거시 API 지원

> 예전에 구현 및 사용하던 날짜와 시간(Date) API와 현재 추가된 LocalDate, LocalDateTime, Instant는 서로 호환 된다!

```java
public static void main(String[] args) {
    Date date = new Date();
    Instant instant = date.toInstant();
    Date newDate = Date.from(instant);

    GregorianCalendar gregorianCalendar = new GregorianCalendar();
    LocalDateTime dateTime = gregorianCalendar.toInstant()
            .atZone(ZoneId.systemDefault())
            .toLocalDateTime();
    ZonedDateTime zonedDateTime = ZonedDateTime.from(dateTime);

    GregorianCalendar from = GregorianCalendar.from(zonedDateTime);

    ZoneId zoneId = TimeZone.getTimeZone("PST").toZoneId();
    TimeZone timeZone = TimeZone.getTimeZone(zoneId);
}
```

1. GregorianCalendar와 Date 타입의 인스턴스를 Instant나 ZonedDateTime으로 변환
2. java.util.TimeZone에서 java.time.ZoneId로 상호 변환 가능

<br><br><br>

---

## Reference

[백기선 인프런 강의 : 더 자바, Java8](https://www.inflearn.com/course/the-java-java8/dashboard)

[(Java) 날짜 비교하기 ( LocalDate, LocalDateTime, Date, Calendar)](https://hianna.tistory.com/611)

[(Java8 Time API) Duration과 Period 사용법 (+ChronoUnit)](https://www.daleseo.com/java8-duration-period/)

https://dzone.com/articles/using-optional-correctly-is-not-optional

https://codeblog.jonskeet.uk/2017/04/23/all-about-java-util-date/
