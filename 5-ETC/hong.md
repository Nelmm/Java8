# 어노테이션

## 1. 제네릭타입 선언부에도 사용가능

- 기존의 어노테이션 Target 종류
  - TYPE : 클래스 / 인터페이스 / 어노테이션 / enum / record의 선언부
  - ANNOTATION_TYPE : 어노테이션의 선언부
  - FIELD : 클래스내의 내부필드의 선언부
  - METHOD : 클래스내의 메서드의 선언부
  - PARAMETER : 메서드내의 파리미터의 선언부
  - CONSTRUCT : 생성자의 선언부
  - LOCAL_VARIABLE : 지역변수의 선언부
  - PAKCAGE : 해당 패키지의 packge-info.java에 붙일 수 있다.
- java8에 추가된 Target

  - 위의 어노테이션들과는 다르게 선언 주석이 아닌 TYPE 주석이다.
    - 선언 주석 : 주의사항, 사용방법, 사용처 등 설명
    - TYPE 주석 : 정수 값이 0보다 커야 한다, null이 아니다 와 같은 값에 대한 정보 제공함으로써 implements, thorws, new 구절에 사용하거나 제네릭에 사용함으로써 외부의 프로젝트에도 적용할 수 있도록 확장한 범위
  - TYPE_PARAMETER : 타입 선언부에 사용이 가능

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

    컴파일하면서 해당 타입이 무슨 타입인지 분석하여 CLASS_TYPE_PARAMETER / METHOD_TYPE_PARAMETER 로 변환하는 것을 볼 수 있다.

    타입 선언부에 사용이 가능한 Target이므로 아래와 같이 실제 사용하는 부분에는 적용할 수 없다.

    ```java
    public class AnnotationStudy {
        public <T> void print(@ParmeterEx T a){}
    }
    ```

    - TYPE_USE : 선언부 뿐만이 아닌 타입 사용되는 모든곳에 적용이 가능(클래스/인터페이스/내부필드/파라미터/제네릭/지역변수 등)

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

      //byte code
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

      바이트 코드를 보면 TYPE_USE를 사용해도 컴파일 결과는 컴파일러가 적절한 어노테이션 TARGET으로 바꾸는 것을 볼 수 있다.

      ```java
      @Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER, ElementType.LOCAL_VARIABLE, ElementType.TYPE_USE})
      @Retention(RetentionPolicy.CLASS)
      @Documented
      public @interface NonNull {
      }
      ```

      Lombok의 NonNull 어노테이션으로 해당 어노테이션도 Target범위가 TYPE_USE가 포함되어 있다.

## 2. 중복 사용 가능

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_USE)
public @interface Chicken {
    String value() default "후라이드";
}

//error
@Chicken
@Chicken("양념")
public class App() {

}
```

기존에는 위와 같이 같은 어노테이션을 같은범위에 중복해서 정의할 수 없었는데 java8부터는 `@Repeatable()`이 추가되어 중복해서 사용이 가능해졌다.

Reapetable은 한개의 value를 가지고 있는데 여기에 일종의 어노테이션 컨테이너 역할을 할 어노테이션 클래스를 넘겨주면 해당 컨테이너에 중복사용한 어노테이션들을 담는 방식으로 동작하게 된다. 이때 주의할 점이 Reapeatable의 value는 어노테이션의 컨테이너 역할이기 때문에 중복해서 사용할 어노테이션보다 생명주기(RetentionPolicy)가 길어야만 한다.

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_USE)
public @interface ChickenContainer {
    Chicken[] value();
}
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE_USE)
@Repeatable(ChickenContainer.class)
public @interface Chicken {
    String value() default "후라이드";
}

@Chicken("양념")
@Chicken("마늘간장")
public class App {

    public static void main(String[] args) {
        ChickenContainer chickenContainer = App.class.getAnnotation(ChickenContainer.class);
        Arrays.stream(chickenContainer.value()).forEach(c -> {
            System.out.println(c.value());
        });
    }
}

//print
양념
마늘간장

//bytecode
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

ChickenConatiner 어노테이션을 생성하여 Chicken 어노테이션들을 value로 할당하는 것을 볼 수 있다.

`MethodHandles$Lookup`라는 이름의 클래스로 innerClass가 만들어지는 것을 볼 수 있는데 이는 메서드 핸들을 생성하기위한 factory class로 ChickenContainer의 value()인 Chicken[]에 접근하기위한 class

<br><br>

# 배열 병렬 정렬

Arrays.parallerSort()가 추가되어 분산되어 정렬이 가능하다.

```java
public class Example {
    public static void main(String[] args) {
        int size = 1500;
        int[] numbers = new int[size];
        Random random = new Random();

        IntStream.range(0,size).forEach(i -> numbers[i] = random.nextInt());
        long start = System.nanoTime();
        Arrays.sort(numbers);
        System.out.println("time : " + (System.nanoTime() - start) );

        IntStream.range(0,size).forEach(i -> numbers[i] = random.nextInt());
        start = System.nanoTime();
        Arrays.parallelSort(numbers);
        System.out.println("time : " + (System.nanoTime() - start) );
    }
}
```

<br><Br>

# Metaspace

JVM의 여러 메모리 영역 중에 PermGen 메모리 영역이 없어지고 Metaspace 영역이 생겼다.

## PermGen

- Heap 영역에 속함
- permanent generation, 클래스 메타데이터를 담는 곳.
- 기본 값으로 고정되고 제한된 크기를 가지고 있다.
- PermGen초기 사이즈 설정 : `-XX:PermSize=N`
- PermGen 최대 사이즈 설정 : `-XX:MaxOermSize=N`
- Old/Eden 영역보다 적은 메모리사이즈를 가지고 있으며 os bit, jdk 버전별로 다르다.
- 고정된 크기를 가지기 때문에 클래스를 계속해서 만들게 되면 GC가 메모리를 정리해도 PermGen사이즈를 넘어서면 memory out 에러가 발생한다.

## Metaspace

- 클래스 메타데이터를 담는 곳
- Heap영역이 아니라 Native 메모리 영역이다.
- 기본값으로 제한된 크기를 가지고 있지 않다. (게속 늘어날 수 있다.)
  - 계속해서 늘어날 수 있기 때문에 자칫 잘못하면 os의 메모리를 모두 잡아먹어 서버 자체가 다운될 수 있다.
- java8부터는 PermGen 관련 java옵션은 무시
- Metaspace 초기 사이즈 설정 : `-XX:MetaspaceSize=N`
- Metaspace 최대 사이즈 설정 : `-XX:MaxMetaspaceSize=N`

<br><br>

## 후기

시작할때만해도 5주가 되게 긴 것처럼 느껴졌고, Java8하나를 너무 오래끄는게 아닌가 싶었는데 막상 진행해보니 분량이 정말 적절했으며 오히려 시간이 부족한 느낌이었다.

어떤 기능부터가 java8의 기능인지 구분이 안갔고 LocalDateTime같은 경우도 Java8에 추가된 내용인지 알지 못하고 무지성으로 사용하고 있었는데 이번 기회에 확실히 알게 되었고 특히 `람다` 부분은 많이 사용할 것 같다.

다음 주제는 디자인패턴인데 기대가 된다.
