# Optional
Java 8에 새로 생긴 인터페이스로 `라이브러리 메서드가 반환할 결과값이 없음을 명백하게 표현할 필요가 있는 곳에서 제한적으로 사용할 수 있는 메커니즘을 제공`하기 위해 새로 생겨났다.

Java api doc의 API 노트를 보면 다음과 같이 설명하고 있다. `Optional은 주로 결과 없음을 나타낼 필요성이 명확하고 null을 사용하면 오류가 발생할 수 있는 메소드 반환 유형으로 사용하도록 고안되었다. 유형이 옵션인 변수 자체는 null이 아니므로 항상 옵션 인스턴스를 가리켜야 한다.`


## Optional을 올바르게 사용하기
### 1. Optional 변수에 null 대신 Optional.empty() 사용
```java
Optional<Car> optionalCar = null;

Optional<Car> optionalCar = Optional.empty();
```

### 2. Optional 값을 꺼내쓰기 전에 값이 있는지 확인하기
```java
Optional<Car> optionalCar = Optional.empty();

Car car = optionalCar.get(); //NoSuchElementException 발생
```
Optional은 값이 비어있을 수도 있기 때문에 사용하기전에 존재한다는 것을 증명해야 하는데 일반적으로는 isPresent()후에 get()을 사용할 수 있지만 코드도 길어지고 한번에 사용할 수 있는 API를 제공하기 때문에 이를 사용해보자.

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
orElseGet()은 `Supplier`를 인자로 받으며, 값이 없을때에 해당 supplier가 수행된다. 하지만 orElse()는 Optional로 감싸고 있는 객체타입을 인자로 받으며 값이 있더라도 내부가 수행되고 사용되지 않는 경우 해당 객체를 지우게 되어 필요없는 오버헤드가 발생한다.

```java
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

### 3. Optional이 있을때만 이를 소비하여 무언가를 할때는 ifPresent()를 활용
```java
//before
if(optionalCar.isPresent()){
    System.out.println(optionalCar.get());
}

//after
optionalCar.ifPresent(System.out::println);
```

### 4. 컬렉션은 Optional대신 비어있는 컬렉션을 사용하자.
```java
//before
List<Car> cars = carFactory.getCars();
return Optional.ofNullable(cars);

//after
List<Car> cars = carFactory.getCars();
return cars != null ? cars : Collections.emptyList();
```

### 5. 컬렉션은 Optional로 감싸지말자.
컬렉션 자체가 데이터를 감싼 형태의 객체이고 이도 충분한 API를 제공하기 때문에 한번더 Optional로 감싸면 필요없는 오버헤드가 발생한다. JPA의 메서드를 생성할때도 JPA자체적으로 비어있는 컬렉션을 반환해주므로 Optional로 감쌀 필요가 없다.
```java
//bad
Optional<List<Car>> findAllByCompanyName(String name);

//good
List<Car> findAllByCompanyName(String name);
```

### 6. 컬렉션,Map의 원소로 Optional을 사용하지 말자.


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

# Date/Time

## 새로 추가 된 이유
### 1. java.util.Date 클래스는 mutable하여 thread safe 하지 않다.
```java
Date date = new Date();
long time = date.getTime();
System.out.println(date); //Tue Nov 02 17:34:03 KST 2021

Thread.sleep();
Date date2 = new Date();
System.out.println(date2); //Tue Nov 02 17:34:06 KST 2021
date2.setTime(time); 
System.out.println(date2); //Tue Nov 02 17:34:03 KST 2021
```
Date객체를 보면 setTime()과 같은 메서드로 내부값을 변경할 수 있는 mutable하여 멀티스레드 환경에 적합하지 않다.

2. 클래스 이름이 명확하지 않다. (Date인데 시간까지 다룬다.)
Date 라는 클래스인데 getTime()과 같이 시간까지 다룬다.

3. 타입 안정성이 없고 월이 0부터 시작하거나 등등 버그 발생의 여지가 많다.
```java
Date date = new Date(-10000,1,1,1,1);
System.out.println(date); //Thu Feb 01 01:01:00 KST 8101

//Date
 public Date(int year, int month, int date, int hrs, int min) {
        this(year, month, date, hrs, min, 0);
}
```
타입을 int로 받기 때문에 숫자면 모두 다 받을 수 있고 그에 따라 이상한 값이 올 수가 있다.

<br>

## 특징
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

## 사용 예

### 1. Instant
```java
Instant instant = Instant.now();
System.out.println(instant); //2021-11-02T14:07:19.218304100Z  (기준시 UTC 기준)

ZonedDateTime zonedDateTime = instant.atZone(ZoneId.systemDefault());
System.out.println(instant); //2021-11-02T23:08:45.188601700+09:00[Asia/Seoul]  (현재 KTC 기준)
```
### 2. LocalDate/LocalTime/LocalDateTime/ZonedDateTime
```java
LocalDateTime now = LocalDateTime.now();    //서버의 시스템 zone 기준
System.out.println(now);

LocalDateTime of = LocalDateTime.of(1982, Month.JULU, 15,0,0,0);

ZonedDateTime nowInKorea = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));
System.out.println(nowInKorea); 
```
### 3. DateTimeFormatter
```java
//formatting
LocalDateTime now = LocalDateTime.now();
DateTimeFormatter formatter = DateTimeFormatter.ISO_DATE_TIME;
System.out.println(now.format(formatter));      //2021-11-02T23:28:51.388655
formatter = DateTimeFormatter.ofPattern("MM.dd.yyyy");
System.out.println(now.format(formatter));      //11.02.2021

//parsing
LocalDate parse = LocalDate.parse("08.12.2021",formatter);
System.out.println(parse);
```
DateTimeFormatter은 ofPattern()을 이용해 특정 패턴을 지정할 수 있고 미리 정의된 포맷터들이 존재하는데 이 형식을 이용하고자하면 굳이 새로 정의해줄 필요가 없다. 정의된 포맷터들은 [여기](https://docs.oracle.com/javase/8/docs/api/java/time/format/DateTimeFormatter.html)를 참고

또 Date/Time타입의 parse()메서드를 통해서 특정 문자열을 Date/Time 타입으로 파싱할 수 있다.

### 4. Duration / Period
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

## 추가
### Immutable 한 Date/Time
```java
LocalDateTime now = LocalDateTime.now();
Lonow = now.plusDays(1);


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
Date/Time의 모든 객체는 mutable한 속성의 단점을 해결하고자 Immutable한 속성을 갖고 설계가 되었는데 이 때문에 메서드를 이용해 날짜,시간을 변경하면 위에 정의된 함수처럼 새로운 객체를 만들어 반환하고 있다.



<br><Br>

---
## Reference

https://dzone.com/articles/using-optional-correctly-is-not-optional

https://codeblog.jonskeet.uk/2017/04/23/all-about-java-util-date/

