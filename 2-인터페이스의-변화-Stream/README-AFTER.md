> ์ ๋ฆฌ : [์์ค](https://github.com/jun108059)

**๐๋ชฉ์ฐจ**
- [Stream](#stream)
    - [1. Stream์ ํน์ง](#1-stream์-ํน์ง)
        - [1-1. ์ปฌ๋ ์์ด ์๋๋ค.](#1-1-์ปฌ๋ ์์ด-์๋๋ค)
        - [1-2. Functional in nature.](#1-2-functional-in-nature)
        - [1-3. ์ฌ์ฌ์ฉ ๋ถ๊ฐ๋ฅ](#1-3-์ฌ์ฌ์ฉ-๋ถ๊ฐ๋ฅ)
        - [1-4. ๋ฌด์ ํ ๋ฐ์ดํฐ ๊ฐ๋ฅ](#1-4-๋ฌด์ ํ-๋ฐ์ดํฐ-๊ฐ๋ฅ)
        - [1-5. ์ง์ฐ์ฐ์ฐ(Lazy)](#1-5-์ง์ฐ์ฐ์ฐlazy)
        - [1-6. ๋ณ๋ ฌ ์ฒ๋ฆฌ](#1-6-๋ณ๋ ฌ-์ฒ๋ฆฌ)
    - [2. ์คํธ๋ฆผ ํ์ดํ๋ผ์ธ](#2-์คํธ๋ฆผ-ํ์ดํ๋ผ์ธ)
        - [2-1. Stream ์ธ์คํด์ค ์์ฑ](#2-1-stream-์ธ์คํด์ค-์์ฑ)
        - [2-2. ์ค๊ฐ ์คํผ๋ ์ด์](#2-2-์ค๊ฐ-์คํผ๋ ์ด์)
        - [2-3. ์ข๋ฃ ์คํผ๋ ์ด์](#2-3-์ข๋ฃ-์คํผ๋ ์ด์)
        - [2-4. ํ์ดํ๋ผ์ธ ์์](#2-4-ํ์ดํ๋ผ์ธ-์์)
    - [3. Stream API ์ ๋ฆฌ](#3-stream-api-์ ๋ฆฌ)

# Stream

> ์ฐ์๋ ๋ฐ์ดํฐ๋ฅผ ์ฒ๋ฆฌํ๋ ์คํผ๋ ์ด์๋ค์ ๋ชจ์

<img src = "https://user-images.githubusercontent.com/42997924/140324289-8529f61a-5c59-42aa-8075-2a92412b2960.gif" width="50%" height="50%">

๐ผ [์ด๋ฏธ์ง ์ถ์ฒ](https://weibo.com/2856003072/JnaSbeEnJ?type=comment)

- `stream` ์ `์ปจ๋ฒ ์ด์ด ๋ฒจํธ`์ ๋น์ทํ๋ค.
- ์ปจ๋ฒ ์ด์ด ๋ฒจํธ์ ๋ก์กฐ๊ฐ(๋ฐ์ดํฐ)๋ค์ ํ๋ ค๋ณด๋ด๋ฉด์ ๋ฐ์ฃฝ์ ํ๊ณ , ์๊ธ์ ์๊ณ (map), ๋ถ๋ํ์ ๋นผ๊ณ (filter) ํฌ์ฅ์ ํด์(collect) ๋ด๋ณด๋ธ๋ค.

## 1. Stream์ ํน์ง

### 1-1. ์ปฌ๋ ์์ด ์๋๋ค.

- ๋ฐ์ดํฐ๋ฅผ ๋ด๊ณ  ์๋ ์ ์ฅ์๊ฐ ์๋๋ค.(์ปฌ๋ ์์ด ์๋๋ผ๋ ์๋ฏธ) 
- ํ์ฅํด์ ์ฌ์ฌ์ฉ์ด ๋ถ๊ฐ๋ฅํ๋ฐ, ์๋์์ ์์ธํ ์ดํด๋ณด์.

### 1-2. Functional in nature.

- stream์ ์ฒ๋ฆฌํ๋ ๋ฐ์ดํฐ ์์ค๋ฅผ ๋ณ๊ฒฝํ์ง ์๋๋ค.
- ์ฆ ๋ด๊ฐ `A`๋ผ๋ ๋ฐ์ดํฐ๋ฅผ ์์ ํ๋ค๊ณ  ํด์ ์๋ณธ๋ฐ์ดํฐ๊ฐ ์์ ๋๋ ๊ฒ์ ์๋๋ผ๋ ์๋ฏธ.

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
[์คํ ๊ฒฐ๊ณผ]
yj
*/
```

### 1-3. ์ฌ์ฌ์ฉ ๋ถ๊ฐ๋ฅ

> ์คํธ๋ฆผ์ผ๋ก ์ฒ๋ฆฌํ๋ ๋ฐ์ดํฐ๋ ์ค์ง ํ ๋ฒ๋ง ์ฒ๋ฆฌํ๋ค.
    - ์ปจ๋ฒ ์ด์ด ๋ฒจํธ์์ ๋ฐ์ดํฐ๋ค์ด ํ ๋ฒ ์ง๋๊ฐ๋ค ๋ค์ ๋์์ค์ง ์์.

์คํธ๋ฆผ์ ์ข๋ฃ์ฐ์ฐ์ ํ์ง ์๋ ์ด์ ๊ทธ์ ๊น์ง๊ฐ ํ๋์ ์ธ์คํด์ค๋ก์จ ๊ณ์ ์ฌ์ฉ์ด ๊ฐ๋ฅํ์ง๋ง ์ข๋ฃ์ฐ์ฐ์ ์ํํ๋ฉด ์ฌ์ฌ์ฉ์ด ๋ถ๊ฐํ๊ฒ ๋๋๋ฐ ์ด๋ `์คํธ๋ฆผ`์ ์ ์ฅ๋ ๋ฐ์ดํฐ๋ฅผ ๊บผ๋ด์ ์ฒ๋ฆฌํ๋ ์ฉ๋์ด์ง ๋ฐ์ดํฐ๋ฅผ ์ ์ฅํ๋ ๋ชฉ์ ์ด ์๋๊ธฐ ๋๋ฌธ์ด๋ค.

```java
public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1,2,4,51,2,3);

       Stream<Integer> stream = list.stream().map(i -> i+1);

       stream.count();
       stream.forEach(System.out::println);     //Exception ๋ฐ์ : java.lang.IllegalStateException: stream has already been operated upon or closed
}
```
Stream์ AutoCloseable์ ์์๋ฐ์ BaseStream์ ์์๋ฐ์ close()๋ฉ์๋๋ฅผ ๊ฐ์ง๊ณ  ์์ด ์ข๋ฃ์ฐ์ฐ์ ์ฌ์ฉํ๊ฒ ๋๋ฉด ์ฐ๋ฆฌ๊ฐ ๋ช์ํ์ง ์์๋ ๋ด๋ถ์ ์ผ๋ก close()๋ฅผ ์์ผ์ฃผ๊ธฐ ๋๋ฌธ์ ์ฌ์ฌ์ฉ์ด ๋ถ๊ฐ๋ฅํ๋ค.

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
์์ ์์ฑํ ์ฝ๋์ ๋ฐ์ดํธ์ฝ๋ ์ผ๋ถ๋ถ์ธ๋ฐ, ์์ ์์ฑํ Stream์ ์ฐธ์กฐ์์ญ 2์ ์ ์ฅํ๊ณ , count()๋ฉ์๋์์ ์ด๋ฅผ loadํ๊ณ  popํ๋ ๊ฒ์ ๋ณผ์ ์๋ค. ๊ทธ๋ฐ๋ฐ max()๋ฉ์๋์์ ์ด popํ ์ฐธ์กฐ์์ญ 2๋ฅผ ๋ค์ loadํ๋ ค๊ณ  ํ๋ ๋ฐํ์์ ์๋ฌ๊ฐ ๋ฐ์ํ๋ ๊ฒ์ด๋ค.


### 1-4. ๋ฌด์ ํ ๋ฐ์ดํฐ ๊ฐ๋ฅ

- ๋ฌด์ ํ์ ๋ฐ์ดํฐ์ผ ์ ์๋ค.
- ์ด ๊ฒฝ์ฐ Short Circuit(limit, skip) ๋ฉ์๋๋ฅผ ์ฌ์ฉํด์ ์ ํ ๊ฐ๋ฅ

### 1-5. ์ง์ฐ์ฐ์ฐ(Lazy)

> ์ค๊ฐ ์คํผ๋ ์ด์์ ๊ทผ๋ณธ์ ์ผ๋ก `lazy`ํ๋ค.

    - ์ฌ๋ฌ ์ค๊ฐ ์คํผ๋ ์ด์๋ค์ ๋ฉ์๋ ์ฒด์ด๋์ ํ๋๋ผ๋ ๊ทธ ์์ ์์ ์ฝ๋๊ฐ ์ํ๋์ง๋ ์์.
    - ๋ชจ๋  ์ค๊ฐ ์คํผ๋ ์ด์์ ์คํ ์๊ธฐ๋ ์ข๋ฃ ์คํผ๋ ์ด์์ ํธ์ถ์์ !
    - ๋ฐ๋ผ์ ์ค๊ฐ ์คํผ๋ ์ด์์ ๋ฐํํ์์ ๋ ๋ค๋ฅธ stream
    
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
    - stream์ ์ค๊ฐ์คํผ๋ ์ดํฐ๋ฅผ ์ฌ์ฉํ๋ ์๊ฐ์๋ ์ฝ๋๊ฐ ์ํ๋์ง ์๋๋ค.
    - ๊ทธ๋ ๊ธฐ ๋๋ฌธ์ ์ค๊ฐ์คํผ๋ ์ด์ map ์์ ์๋ ์ถ๋ ฅ๋ฌธ์ด ์ํ๋์ง ์๋๋ค.
    - ์ํ์ํค๊ธฐ ์ํด์๋ ์คํธ๋ฆผ ํ์ดํ๋ผ์ธ์ ์ ์ํด์ผ ํ๋ค.

#### 1-5-1. ์ง์ฐ์ฐ์ฐ์ ํตํ ์ฑ๋ฅ ํฅ์

๋ด์ฉ์ ์ง๋์ฃผ ์ ๋ฆฌํ๋ ๋ด์ฉ์ค ๋๋ค์ ์ง์ฐ์ฐ์ฐ๊ณผ ๋์ผํ ๋ด์ฉ์ด๋ค.

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
์ถ๊ฐ์ ์ผ๋ก ์ค๊ฐ์ฐ์ฐ์ ์์์ ๋ฐ๋ผ ์์ฒ๋ผ ๋ถํ์ํ ์ฐ์ฐ์ ๋ง์ ์ฑ๋ฅํฅ์์ ๊พํ  ์ ์๊ธฐ๋๋ฌธ์ ์ค๊ฐ์ฐ์ฐ์ ์์๋ ์ ๊ฒฝ์ ์จ์ฃผ๋ฉด ์ข๋ค.

<br>

#### 1-5-2. ์ง์ฐ์ฒ๋ฆฌ ๊ฐ๋ฅ

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

์๋ฅผ ๋๋ ค๋ณด๋ฉด print๊ฐ ๋์ง ์์ ๊ฒ์ ๋ณผ ์ ์๋๋ฐ Stream์ ์ข๋ฃ์ฐ์ฐ์ด ์ํ๋ ๋๊น์ง ์ค๊ฐ์ฐ์ฐ์ ์์ํ์ง ์๊ณ  ํ๋์ Streamํํ๋ก ์ ์ฅ์ด ๋์ด ๋ฏธ๋ฆฌ Stream์ ์ ์ธํด๋๊ณ  ๋์ค์ ๊ฒฐ๊ณผ๋ฅผ ํ์ธํ  ์ ์๋ค.

byte์ฝ๋๋ฅผ ๋ณด๋ฉด Stremaํํ๋ฅผ ์ฐธ์กฐํ์์ผ๋ก ์ ์ฅํ๋ ๊ฒ์ ๋ณผ ์ ์๋ค .(ASTORE)

### 1-6. ๋ณ๋ ฌ ์ฒ๋ฆฌ

> ์์ฝ๊ฒ ๋ณ๋ ฌ ์ฒ๋ฆฌ๋ฅผ ํ  ์ ์๋๋ก parallelStream() ์ ๊ณต

- forEach๋ฅผ ํตํด ๋จ์ํ ๋ฐ๋ณต ์ถ๋ ฅ์ด ์๋ ๋ก์ง(์กฐ๊ฑด๋ฌธ)์ด ์ถ๊ฐ๋ ์๋ก stream์ ํตํด ๊ตฌํํ ์๋ก ๊ฐ๊ฒฐํด์ง๊ธฐ ๋๋ฌธ์ stream์ ์ฐ๋ ๊ฒ ์ธ์๋ ๊ธฐ๋ณธ์ ์ธ for๋ฌธ์ด๋ for-of๋ฌธ์ผ๋ก๋ ๋ก์ง๋ค์ ๋ณ๋ ฌ์ ์ผ๋ก ์ฒ๋ฆฌํ๋๊ฒ ์ฝ์ง ์๋ค.
- ํ์ง๋ง, parallelStream()์ ์ด์ฉํ๋ฉด ์์ฝ๊ฒ ๋ณ๋ ฌ์ฒ๋ฆฌ๋ฅผ ํ  ์ ์๋ค.

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

/* ์คํ ๊ฒฐ๊ณผ
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

> ์๋ฐ๋ ForkJoinPool ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ฅผ ์ด์ฉํด์ ๋ณ๋ ฌ์ฒ๋ฆฌ๋ฅผ ํ๋ค.

**ForkJoinPool?** 

- Fork(Task๋ฅผ ์๊ฒ ์ชผ๊ฐ๋ ๊ฒ)๋ฅผ ์ํํ๊ณ  ํด๋น Pool์ ์๋ ์ค๋ ๋๋ค์ด task๋ฅผ ์ฒ๋ฆฌํ๊ณ  join์ ํตํด ํฉ์น๋ค. 
- ์ด๋ ForkJoinPool์๋ task๊ฐ ์กด์ฌํ๋ queue๊ฐ ์๊ณ , ์ค๋ ๋๋ค์ด ์ด queue์์ task๋ฅผ ํ ๋น๋ฐ์ ์์ ์ queue์ ์ ์ฌํด๊ฐ๋ฉฐ ์ฒ๋ฆฌํ๋๋ฐ ๋๊ณ ์๋ ์ค๋ ๋๊ฐ ์กด์ฌํ๋ค๋ฉด ๋ค๋ฅธ ์ค๋ ๋์ queue์์ task๋ฅผ stealํ๋ ๋ฐฉ๋ฒ์ผ๋ก ๋์ํ๋ Work-Stealing๋ฉ์ปค๋์ฆ์ ์ด์ฉํ๊ธฐ ๋๋ฌธ์ task๊ฐ ํ์ task๋ฅผ ์์ฑํ๊ฑฐ๋ small task๊ฐ ๋ง์ ๊ฒฝ์ฐ ํจ๊ณผ์ ์ด๋ค.

> sequential() ์ ์ด์ฉํ๋ฉด ๋ค์ ์ํ์ ์คํธ๋ฆผ์ผ๋ก ๋ณ๊ฒฝ๋ ๊ฐ๋ฅํ๋ค.

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

/* ์คํ ๊ฒฐ๊ณผ
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

#### 1-6-1. Pool Size ์กฐ์ ๋ฐฉ๋ฒ

1. property
```java
 public static void main(String[] args) {
        System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism","4");
        List<Integer> list = Arrays.asList(1,2,4,51,2,3);
        list.parallelStream().forEach(i->System.out.println(i+ " : " + Thread.currentThread().getName()));

    }
```

2. ForkJoinPool ๊ฐ์ฒด ์ด์ฉ
```java
 public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1,2,4,51,2,3);
         ForkJoinPool pool = new ForkJoinPool(2);
         pool.submit(() -> list.parallelStream().sequential().forEach(i->System.out.println(i+ " : " + Thread.currentThread().getName()))).get();
    }
```
ForkJoinPool์์ฑ์ ๋งค๊ฐ๋ณ์๋ก size๋ฅผ ์ ๋ฌํ๊ณ  submit()๋ฉ์๋์ ๋๋ค๋ก ์์ํ  ๋ด์ฉ์ ์ ์ํด์ฃผ๊ณ  get()์ ํตํด terminate()ํด์ฃผ์ด์ผ ์คํ์ด ๋๋ค.

3. default Pool size ๋ณ๊ฒฝ
```
-Djava.util.concurrent.ForkJoinPool.common.parallelism=16
```
build์์ ์ ์ต์์ผ๋ก ๊ธฐ๋ณธ ํ ์ฌ์ด์ฆ๋ฅผ ๋ณ๊ฒฝ์ด ๊ฐ๋ฅํ๋ค.

#### 1-6-2. ๋จ์ 

์์์ ์ค๋ชํ๊ฒ๊ณผ ๊ฐ์ด ForkJoinPool ํน์ฑ์ task๋ฅผ ๋ถํ ํ ๋(fork) ๊ท ๋ฑํ๊ฒ ๋ถ๋ฐฐ๋ฅผํ๊ฒ ๋๋๋ฐ ์ด๋ ์ด ๋ถํ ํ๋ ์์์ ๋น์ฉ์ด ๋๊ฒ ๋๋ฉด ์คํ๋ ค ์ฑ๋ฅ์ด ์์ข์ ์ ์๋ค. ์๋ฅผ ๋ค์ด, ์ด๋ฏธ ์ฌ์ด์ฆ๋ฅผ ์๊ณ  ์๋ ArrayList๊ฐ์ ๊ฒฝ์ฐ๋ ๋ถํ ํ๋ ์์์ด ๋น ๋ฅผ ์ ์์ง๋ง ์ฌ์ด์ฆ๋ฅผ ๋ชจ๋ฅด๋ LinkedList๊ฐ์ ๊ฒฝ์ฐ์๋ ์์ฐจ์์๊ณผ ๊ฐ๊ฑฐ๋ ๋ ๋๋ฆด ์๊ฐ ์๋ค.

๋ณ๋ ฌ๋ก ์ฒ๋ฆฌ๋๋ ์์์ด `๋๋ฆฝ์ `์ด์ง ์๋ค๋ฉด ์ด๋ ์คํ๋ ค ์ฑ๋ฅ์ ์์ข์ ์ ์๋ค. ์๋ฅผ ๋ค์ด sorted()/distinct()์ ๊ฐ์ ์์์ ์ํํ ๋๋ ๋ด๋ถ์ ์ํ์ ๋ํ ๋ณ์๋ฅผ ์์์ด `๊ณต์ `ํด์ผ์ง๋ง ๊ฐ๋ฅํ ์์์ด๋ฏ๋ก `lock`์ ์์์ด ์ถ๊ฐ๋ก ๋ฐ์ํ๊ณ  ์ค๋ ๋๊ฐ ๋๊ธฐํ๋ ์ํฉ์ด ์๊ธฐ๊ธฐ ๋๋ฌธ์ ์์ฐจ์ ์ผ๋ก ์คํํ๋ ๊ฒฝ์ฐ๊ฐ ๋ ํจ๊ณผ์ ์ผ ์ ์๋ค.

> ์ ๋ฆฌํ์๋ฉด,  
> ์ฌ์ค์ ๋ฉํฐ ์ค๋ ๋๋ฅผ ์ด์ฉํ๋ค๊ณ  ์ฑ๋ฅ์ด ๋ฌด์กฐ๊ฑด ์ข์ ๊ฒ์ ์๋๋ฉฐ ์คํ๋ ค ๋๋ ค์ง ๊ฐ๋ฅ์ฑ์ด ๋ ๋๋ค.  
> ๋๋ถ๋ถ์ ๊ฒฝ์ฐ ๊ทธ๋ฅ `stream`์ ์ฐ๋๊ฒ ๋์ผ๋ฉฐ ์ ๋ง ํฐ๋ฐ์ดํฐ๋ฅผ ๋ค๋ฃฐ ๋ ์ฑ๋ฅ ํ์คํธ ํ `parallelStream()`์ ์ฌ์ฉํ๋ฉด ๋๋ค.


## 2. ์คํธ๋ฆผ ํ์ดํ๋ผ์ธ

> ์คํธ๋ฆผ์ด๋ผ๋ ์ปจ๋ฒ ์ด์ด ๋ฒจํธ์ 0๊ฐ ํน์ ๋ค์์ ์ค๊ฐ ์คํผ๋ ์ดํฐ(intermediate operation)๊ณผ ํ๊ฐ์ ์ข๋ฃ ์คํผ๋ ์ด์(terminal operation)์ผ๋ก ๊ตฌ์ฑ.  
> ์ด ์คํธ๋ฆผ์ ๋ฐ๋์ ํ๋์ ์ข๋ฃ ์คํผ๋ ์ด์์ด ์์ด์ผ ํ๋ฉฐ, ๋ง์ฝ ์ข๋ฃ ์คํผ๋ ์ด์์ด ์๋ค๋ฉด, ์คํธ๋ฆผ์ ์กด์ฌํ์ง๋ง ์ฝ๋ ์ํ์ ๋์ง ์๋๋ค.

์คํธ๋ฆผ์ ๋ฐ์ดํฐ ์์ค๋ ์ค์ง ํฐ๋ฏธ๋ ์คํผ๋ ์ด์์ ์คํํ  ๋์๋ง ์ฒ๋ฆฌํ๋ค!

### 2-1. Stream ์ธ์คํด์ค ์์ฑ

> ์คํธ๋ฆผ ์ธ์คํด์ค ์์ฑํ๋ ๋ฉ์๋

- `Arrays.stream(๋ฐฐ์ดํ์ ๋ณ์)` : ๋ฐฐ์ด์ Stream ํ์ ๊ฐ์ฒด๋ก ์์ฑ
- `collection.stream()` : Collectionํ์์ ๊ฐ์ฒด(Collection, List, Set)๋ฅผ Stream ํ์ ๊ฐ์ฒด๋ก ์์ฑ
- `Stream.empty()` : ๋น์ด์๋ Stream() ๊ฐ์ฒด
- `Stream.builder().add()...build()`  : ๋น๋๋ฅผ ์ด์ฉํ Stream ์์ฑ
- `Stream.generate(() -> {}).limit()` : ๋๋ค๋ฅผ ์ด์ฉํ์ฌ ์์ฑํ๋ ๋ฐฉ์์ผ๋ก ๋๋ค์ return๊ฐ์ด ๋ค์ด์๋ Stream์ ์์ฑํ๋ค. ์ด๋ ๋ฌดํํ ์์ฑํ๊ธฐ ๋๋ฌธ์ limit๋ฉ์๋๋ก ์ ํ์ ์ฃผ์ด์ผ ํ๋ค.
- `Stream.iterate(startValue, () -> {}).limit()` : ์ด๊ธฐ๊ฐ๋ถํฐ ํด๋น ๊ฐ์ input์ผ๋ก ํ๋ ๋๋ค๋ฅผ ๋ฐ๋ณตํ์ฌ ๋์จ ๊ฐ์ผ๋ก Stream์ ์์ฑ
- `IntStream.reange(1,5).boxed()` : IntStream์ ์์ฑํ boxed()๋ฉ์๋๋ฅผ ํตํด Stream ์ธ์คํด์ค๋ก ๋ฐํ
- `Random().ints().boxed()` : ๋๋คํ int๊ฐ์ ์ด์ฉํด Stream ์์ฑ
- `"String".chars()` : IntStream์ ๋ฐํ
    - char๋ค์ ascii ๊ฐ์ผ๋ก ํ๋์ ์ซ์๋ก ํํํ  ์ ์๊ธฐ ๋๋ฌธ์ IntStream์ ๋ฐํํ๋ค.
- `list.parallelStream()` : ๋ณ๋ ฌ ์คํธ๋ฆผ

### 2-2. ์ค๊ฐ ์คํผ๋ ์ด์

> Stream์ ๋ฆฌํดํ๋ค.  
> ํํฐ๋ง, ๋งตํ๊ณผ ๊ฐ์ด ์ด๋ ํ ๊ฒฐ๊ณผ๋ฅผ ๋ง๋ค์ด๊ฐ๋ ์ค๊ฐ ์์๋ค์ ๋ฉ์๋ ( Intermediate operations )

- Stateless/Stateful ์คํผ๋ ์ด์์ผ๋ก ๋ ์์ธํ๊ฒ ๊ตฌ๋ถํ  ์๋ ์๋ค.
- ๋๋ถ๋ถ Stateless์ง๋ง distinct๋ sorted์ฒ๋ผ ์ด์  ์์ค ๋ฐ์ดํฐ๋ฅผ ์ฐธ์กฐํด์ผ ํ๋ ์คํผ๋ ์ด์์ Stateful ์คํผ๋ ์ด์.
- `filter, map, limit, skip, sorted, ...`

#### 2-2-1. ๋ฉ์๋ 

- `filter(Predicate)` : Predicate์ ๋ฐํ๊ฐ์ด true์ธ ๋ฐ์ดํฐ๋ง ์ ํ
- `distinct()` : ์ค๋ณต๋ ๋ฐ์ดํฐ ์ ๊ฑฐ
- `map(Function<T,R>)` : ๋ฐ์ดํฐ๋ค์ ๊ฐ๊ณตํ์ฌ ๋ค๋ฅธ ๊ฐ์ผ๋ก ๋ณ๊ฒฝํ๊ณ ์ ํ ๋ ์ฌ์ฉ
- `flatMap(Function<T, Stream>)` : ์ค์ฒฉ๊ตฌ์กฐ๋ฅผ ์ ๊ฑฐํ๊ณ  ๋จ์ผ ์ปฌ๋ ์์ผ๋ก ๋ง๋ค์ด map์ฐ์ฐ์ ์ํ(flattening)
- `sorted(Comparator)` : Comparator์ ์ด์ฉํด ๋ฐ์ดํฐ ์ ๋ ฌ
- `peek(Consumer)` : ๋ฐ์ดํฐ๋ฅผ ๋ณ๊ฒฝํ์ง ์๊ณ  ๊ทธ์  ์ํํ๋ ๋ฉ์๋๋ก ์ค๊ฐ์ ๊ฒฐ๊ณผ ํ์ธํ 

#### 2-2-2. flatMap ํ์ฉ ์์

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
๋ง์ผ ๋ฌธ์์ด์ด ๋ค์ด์๋ ๋ฐฐ์ด์์ ๋ชจ๋  ๋ฌธ์์ด์ ๋ํด ์ฌ์ฉ๋ ์ํ๋ฒณ๋ค๋ง ์ฐพ์๋ด๋ ํจ์๋ฅผ ๊ตฌ์ฑํ๋ค๊ณ  ํ ๋ map()์ ์ด์ฉํ๋ฉด ์์ ๊ฐ์ด ๊ฒฐ๊ณผ๊ฐ ๋์ค๊ฒ ๋๋ค.

์ด๋ ์ํ๋ฒณ์ ํ๋ํ๋ ์กฐํํ๊ธฐ ์ํด String์ split()์ ์ด์ฉํ์ฌ String๋ฐฐ์ด๋ก ํ๋ฒ๋ ๋๋๋ ๊ณผ์ ์์ ์๋ฃ๊ตฌ์กฐ์ depth๊ฐ ํ๋จ๊ณ ์ฆ๊ฐํ์ฌ `Hello World`๋ผ๋ ๋จ์ด์์ distinct๊ฐ ์ํ๋๊ฒ ์๋๋ผ `Hello`, `World` ๋ฐ๋ก ์ํ๋์๊ธฐ ๋๋ฌธ์ด๋ค. ๋ฐ๋ก ์ด๋ด๋ flatMap์ ์ด์ฉํ๋ฉด ํ๋จ๊ณ ์ค์ฒฉ๊ตฌ์กฐ๋ฅผ ๋ฒ๊ฒจ๋ผ ์ ์๋ค.

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

### 2-3. ์ข๋ฃ ์คํผ๋ ์ด์

> Stream์ ๋ฆฌํดํ์ง ์๋๋ค!  
> ์ต์ข์ ์ผ๋ก ๊ฒฐ๊ณผ๋ฅผ ๋ง๋ค๊ณ  ๋ฐํํจ์ผ๋ก์จ chaining์ ๋๋ด๋ ๋ฉ์๋ ( terminal operations )

- `collect, allmatch, count, forEach, min, max...`

Stream์ AutoCloseable์ ์์๋ฐ์ BaseStream์ ์์๋ฐ์ close()๋ฉ์๋๋ฅผ ๊ฐ์ง๊ณ  ์์ด ์ข๋ฃ์ฐ์ฐ์ ์ฌ์ฉํ๊ฒ ๋๋ฉด ์ฐ๋ฆฌ๊ฐ ๋ช์ํ์ง ์์๋ ๋ด๋ถ์ ์ผ๋ก close()๋ฅผ ์์ผ์ฃผ๊ธฐ ๋๋ฌธ์ ์ฌ์ฌ์ฉ์ด ๋ถ๊ฐ๋ฅํ๋ค.

- ๋จ์ ๊ณ์ฐ
    - count() : ์ด ๊ฐฏ์
    - sum() : ํฉ
    - min() : ์ต์๊ฐ
    - max() : ์ต๋๊ฐ
    - average() : ํ๊ท ๊ฐ
- reduce() : ์ ์ํ ๋ก์ง์ ๊ณ์ฐ ํ ๊ฒฐ๊ณผ ๋ฐํ
    - reduce(BinaryOperator) : ๊ฐ ์์๋ฅผ ์ฒ๋ฆฌํ๋ ๋ก์ง
    - reduce(T,BinaryOperator) : ๊ณ์ฐ์ ์ํ ์ด๊ธฐ๊ฐ T๋ฅผ ๊ฐ์ง๊ณ  ๊ฐ ์์๋ฅผ ์ฒ๋ฆฌ
- collect : ํน์  ๋ฐ์ดํฐํํ, ์๋ฃ๊ตฌ์กฐ๋ก ๋ฐ๊พธ์ด์ฃผ๋ ํจ์
- matching
    - anyMatch(Predicate) : ํ๋๋ผ๋ ์กฐ๊ฑด์ ๋ง์กฑํ๋ ์์๊ฐ ์์ผ๋ฉด true
    - allMatch(Predicate) : ๋ชจ๋  ์์๊ฐ ์กฐ๊ฑด์ ๋ง์กฑํ๋ฉด true
    - noneMatch(Predicate) : ๋ชจ๋ ์กฐ๊ฑด์ ๋ง์กฑํ์ง ์์ผ๋ฉด ture
- forEach() : ์์๋ฅผ ๋๋ฉด์ ์คํํ๋ ๋ฉ์๋

### 2-4. ํ์ดํ๋ผ์ธ ์์

> ํ์ดํ๋ผ์ธ์ ์ด๋ป๊ฒ ์์ฑํ๋๋์ ๋ฐ๋ผ ์คํ ์์์ ์ฑ๋ฅ์ด ์ฐจ์ด๋๊ธฐ๋ ํ๋ค.

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

1. `Stream.of()` ๋ก Stream ์์ฑ
2. `map` ์ผ๋ก ๋ฐ์ดํฐ ๊ฐ๊ณต (UpperCase๋ก ๋ณํ)
3. `filter` ๋ก ํํฐ๋ง (A๋ก ์์ํ๋ String๋ง ์ถ์ถ)
4. `์ข๋ฃ ์คํผ๋ ์ด์ : forEach` ๋ก ํํฐ๋ง๋ ๋๋ ๊ฒฐ๊ณผ๋ก ํจ์ ์คํ

์คํ ๊ฒฐ๊ณผ

```bash
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

#### filter๋ฅผ ๋จผ์  ์คํํ๋๋ก ๋ณ๊ฒฝํด๋ณด์. 

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

1. `Stream.of()` ๋ก Stream ์์ฑ
2. `filter` ๋ก ํํฐ๋ง (a๋ก ์์ํ๋ String๋ง ์ถ์ถ)
3. `map` ์ผ๋ก ๋ฐ์ดํฐ ๊ฐ๊ณต (UpperCase๋ก ๋ณํ)
4. `์ข๋ฃ ์คํผ๋ ์ด์ : forEach` ๋ก ํํฐ๋ง๋ ๋๋ ๊ฒฐ๊ณผ๋ก ํจ์ ์คํ

์คํ ๊ฒฐ๊ณผ

```bash
filter: a
map: a
forEach: A
filter: b
filter: c
filter: d
filter: e
```

> ๋ ์์ ๋ชจ๋ ๋์ผํ๊ฒ forEach ๋ ํํฐ๋ง๋ String A ๋ง ์ถ๋ ฅํ ๊ฒ์ ํ์ธํ  ์ ์์.

- ์ต์ข์ ์ผ๋ก ์ข๋ฃ ์คํผ๋ ์ด์์ ํ๋ฒ๋ง ์คํ๋์ง๋ง, ์ค๊ฐ์ map ์คํ ํ์๊ฐ ์ฐจ์ด๋๊ฒ ๋๋ค.

## 3. Stream API ์ ๋ฆฌ

> stream api๋ฅผ ์ ๋ฆฌํ๊ณ  ์์ ๋ฅผ ํตํด ์์๋ณด์.

1. ๊ฑธ๋ฌ๋ด๊ธฐ
    - Filter(Predicate)
    - stream์์ ํน์  ์กฐ๊ฑด(Predicate)์ ๋ง์กฑํ๋ ์๋ฆฌ๋จผํธ๋ง ์๋ก์ด ์คํธ๋ฆผ์ผ๋ก ๋ฐํ
2. ๋ณ๊ฒฝํ๊ธฐ
    - Map(Function) ๋๋ FlatMap(Function)
    - ๊ฐ๊ฐ์ ์์์์ ํน์ ์์๋ง ๊บผ๋ด๊ฑฐ๋ ํน์ ๋ณ๊ฒฝํ์ฌ ์๋ก์ด ์คํธ๋ฆผ์ผ๋ก ๋ฐํ
    - flatMap์ ๊ฒฝ์ฐ Array๋ Object๋ก ๋ํ๋์ด์๋ ๋ด์ฉ๋ฌผ์ ๊บผ๋ด์ด ํ๋๋ก ํฉ์น  ์คํธ๋ฆผ์ผ๋ก ์์ฑ ํ ๋ฐํ
3. ์์ฑํ๊ธฐ
    - generate(Supplier) ๋๋ Iterator(T seed, UnaryOperator)
    - seed ๋ก๋ถํฐ UnaryOperator ์ ๋ฌด์ ํ์ผ๋ก ๋ฐ๋ณตํ๋ ์คํธ๋ฆผ ๋ฐํ
    - ex: ๋๋ค ๋ฌด์ ํ ์คํธ๋ฆผ
4. ์คํธ๋ฆผ์ ์๋ ๋ฐ์ดํฐ๊ฐ ํน์  ์กฐ๊ฑด์ ๋ง์กฑํ๋์ง ํ์ธ
    - anyMatch(), allMatch(), nonMatch()
    - ์คํธ๋ฆผ์ ์๋ฆฌ๋จผํธ๋ฅผ ๋๋ฉฐ ํน์  ์กฐ๊ฑด์ ๋ง์กฑํ๋์ง ํ์ธ
    - ex1:  ์คํธ๋ฆผ์ ์๋ ๋ชจ๋  ๊ฐ์ด 10๋ณด๋ค ์์์ง ํ์ธ(allMatch)
    - ex2: ์คํธ๋ฆผ์ ์ ๋ชฉ ์ค "Test"๊ฐ ๋ค์ด๊ฐ๋ ์ ๋ชฉ์ด ์๋์ง ํ์ธ(anyMatch)
5. ๊ฐ์ ์ธ๊ธฐ
    - count()
    - ex1: 10๋ณด๋ค ์์ ์์ ๊ฐฏ์๋ฅผ ์ผ๋ค.
6. ์คํธ๋ฆผ์ ๋ฐ์ดํฐ ํ๋๋ก ๋ญ์น๊ธฐ
    - reduce(identity, ByFunction), collect(), sum(), max()
    - ex1: ๋ชจ๋  ์ซ์ ํฉ ๊ตฌํ๊ธฐ
    - ex2: ๋ชจ๋  ๋ฐ์ดํฐ๋ฅผ ํ๋์ ๋ฆฌ์คํธ ํน์ Set์ ์ฎ๊ฒจ ๋ด๊ธฐ

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


    System.out.println("spring์ผ๋ก ์์ํ๋ ์์");
    springClasses.stream()
            .filter(oc -> oc.getTitle().startsWith("spring"))
            .forEach(System.out::println);

    System.out.println("closed๋์ง ์์ ์์");
    springClasses.stream()
            .filter(Predicate.not(OnlineClass::isClosed))
            .forEach(System.out::println);

    System.out.println("์์ ์ด๋ฆ๋ง ๋ชจ์์ ์คํธ๋ฆผ ๋ง๋ค๊ธฐ");
    Stream<String> springTitleStream = springClasses.stream()
            .map(OnlineClass::getTitle);
    springTitleStream.forEach(System.out::println);

    System.out.println("๋ ์์ ๋ชฉ๋ก์ ๋ค์ด์๋ ๋ชจ๋  ์์ ์์ด๋ ์ถ๋ ฅ");
    keesunEvents.stream()
            .flatMap(Collection::stream)
            .forEach(oc-> System.out.println(oc.getId()));

    System.out.println("10๋ถํฐ 1์ฉ ์ฆ๊ฐํ๋ ๋ฌด์ ํ ์คํธ๋ฆผ ์ค์์ ์์ 10๊ฐ ๋นผ๊ณ  ์ต๋ 10๊ฐ ๊น์ง๋ง");
    Stream.iterate(10, i -> i+1)
            .skip(10)
            .limit(10)
            .forEach(System.out::println);

    System.out.println("์๋ฐ ์์ ์ค์ Test๊ฐ ๋ค์ด์๋ ์์์ด ์๋์ง ํ์ธ");
    boolean test = javaClasses.stream().
            anyMatch(oc -> oc.getTitle().contains("Test"));
    System.out.println(test);

    System.out.println("์คํ๋ง ์์ ์ค์ ์ ๋ชฉ์ด spring์ด ๋ค์ด๊ฐ ์ ๋ชฉ๋ง ๋ชจ์์ List๋ก ๋ง๋ค๊ธฐ");
    List<String> spring = springClasses.stream()
            .filter(oc -> oc.getTitle().contains("spring"))
            .map(OnlineClass::getTitle)
            .collect(Collectors.toList());
    spring.forEach(System.out::println);
}
```

---

**Reference**

- [๋ฐฑ๊ธฐ์  ์ธํ๋ฐ ๊ฐ์ : ๋ ์๋ฐ, Java8](https://www.inflearn.com/course/the-java-java8/dashboard)
- [Catsbi's DLog : ์ธํฐํ์ด์ค์ ๋ณํ](https://catsbi.oopy.io/3561609e-6c05-4209-a833-c5bb632b5aaa)
- https://github.com/gowoonsori/my-tech/blob/master/java/java8/interface.md
- https://github.com/gowoonsori/my-tech/blob/master/java/java8/stream.md
- https://dev-milk.tistory.com/5
- https://futurecreator.github.io/2018/08/26/java-8-streams-advanced/
- https://mangkyu.tistory.com/115