> μ λ¦¬ : [μμ€](https://github.com/jun108059)

**πλͺ©μ°¨**
- [μΈν°νμ΄μ€μ λ³ν](#μΈν°νμ΄μ€μ-λ³ν)
    - [1. μΈν°νμ΄μ€λ?](#1-μΈν°νμ΄μ€λ)
        - [1-1. μΆμν΄λμ€μ μΈν°νμ΄μ€ μ°¨μ΄](#1-1-μΆμν΄λμ€μ-μΈν°νμ΄μ€-μ°¨μ΄)
        - [1-2. μΈν°νμ΄μ€μ μ₯μ ](#1-2-μΈν°νμ΄μ€μ-μ₯μ )
        - [1-3. μΈν°νμ΄μ€ νΉμ§](#1-3-μΈν°νμ΄μ€-νΉμ§)
        - [1-4. μΆμν΄λμ€μ νκ³](#1-4-μΆμν΄λμ€μ-νκ³)
    - [2. Java8 λ³ν: μΈν°νμ΄μ€μ κΈ°λ³Έ λ©μλμ μ€νν± λ©μλ](#2-java8-λ³ν-μΈν°νμ΄μ€μ-κΈ°λ³Έ-λ©μλμ-μ€νν±-λ©μλ)
        - [2-1. Default method (κΈ°λ³Έ λ©μλ)](#2-1-default-method-κΈ°λ³Έ-λ©μλ)
        - [2-2. Static method (μ μ  λ©μλ)](#2-2-static-method-μ μ -λ©μλ)
    - [3. μλ°8 APIμ κΈ°λ³Έ λ©μλμ μ€νν± λ©μλ](#3-8-apiμ-κΈ°λ³Έ-λ©μλμ-μ€νν±-λ©μλ)
        - [3-1. Iterable](#3-1-iterable)
        - [3-2. Collection](#3-2-collection)
        - [3-3. Comparator](#3-3-comparator)

# μΈν°νμ΄μ€μ λ³ν

## 1. μΈν°νμ΄μ€λ?

μλ°μ `λ€νμ±`μ κ·Ήλννμ¬ κ°λ°μ½λ μμ μ μ€μ΄κ³  `μ μ§λ³΄μ`λ₯Ό μ©μ΄νκ² νκΈ° μν¨

> λ€νμ±? λμΌν λ©μμ§λ₯Ό μμ νμ λ κ°μ²΄μ νμμ λ°λΌ λ€λ₯΄κ² μλ΅ν  μ μλ λ₯λ ₯

### 1-1. μΆμν΄λμ€μ μΈν°νμ΄μ€ μ°¨μ΄

μΆμλ©μλλ₯Ό κ°μ§μΌλ‘μ¨ λ€νμ±μ κ·Ήλννλ©΄μ μ΄λ€ `μ­ν `μ κ΅¬ννλ λ°©λ²(κ°μ²΄λ€μ΄ λ°λΌμΌ νλ μ±μμ μ§ν©μ μμ ν κ²)μ΄λΌλ κ³΅ν΅μ μ΄ μλ€.

νμ§λ§ **μΆμ ν΄λμ€**λ `is-a`κ΄κ³λ‘ `-λ -μ΄λ€.`μ κ°λμ΄λ©° **μ±μμ μΌλΆλ₯Ό κ΅¬ν**ν΄λμ κ²μ΄κ³  **μΈν°νμ΄μ€**λ `has-a`μ κ΄κ³λ‘ `-λ -λ₯Ό ν  μ μλ€`μ κ°λμΌλ‘ **μ±μμ μ§ν©**λ€μ λμ΄ν΄ λμ κ².

μ΄λ μ΄λ¬ν κ΄κ³κ° κ°μ²΄μ§ν₯μ μμ΄ μ€μν κ²μ κ°μ²΄μ μμ±μ΄ μλλΌ **κ°μ²΄μ νλκ°μ κ΄κ³**λΌλ κ².

νλ§λλ‘ `λ­μ μλ€`, `λ§₯μ osμ΄λ€` μ κ°μ λ¬Έμ₯μ΄ μμλ μ΄ λ¬Έμ₯λ€ μμ `ν΄λΌμ΄μΈνΈ μμ₯μμ` λΌλ λ§μ΄ λΉ μ Έμλ κ²μ΄λ€. μ€μ  μΈκ³μ κ΄μ μΌλ‘ λ°λΌλ³΄λ©° κ°μ²΄κ°μ κ΄κ³λ₯Ό μ μνλ κ²μ΄ μλ μ΄ κ°μ²΄λ€μ μ¬μ©νλ ν΄λΌμ΄μΈνΈ μμ₯μμ λ°λΌλ³΄λ©° νΉμ  κ°μ²΄μ νλμ ν΅ν΄ κ΄κ³λ₯Ό λ§Ίμ΄μΌ νλ€.

**μλΈ ν΄λμ±κ³Ό μλΈ νμ΄ν**

- μλΈ ν΄λμ± : λ€λ₯Έ ν΄λμ€μ μ½λλ₯Ό μ¬μ¬μ©ν  λͺ©μ μΌλ‘ μμμ μ¬μ©νλ κ²½μ°. μμκ³Ό λΆλͺ¨ν΄λμ€κ°μ νλμ΄ νΈνλμ§ μμ μμμΈμ€ν΄μ€κ° λΆλͺ¨μΈμ€ν΄μ€λ₯Ό λμ²΄ν  μμκ³  μ΄λ₯Ό `κ΅¬ν μμ` / `ν΄λμ€ μμ`μ΄λΌκ³  λΆλ₯Έλ€.
- μλΈ νμ΄ν : νμ κ³μΈ΅μ κ΅¬μ±νκΈ° μν΄ μμμ μ¬μ©νλ κ²½μ°. μμκ³Ό λΆλͺ¨ν΄λμ€κ°μ νλμ΄ νΈνλμ΄ μΈμ€ν΄μ€λ₯Ό λμ²΄ν  μ μλ€.

<img src = "https://user-images.githubusercontent.com/42997924/140612645-c9ff4d57-52c7-44bb-b70b-e5c5d44c3a10.png" width="50%" height="50%">

πΌ μ‘°μνΈλμ μ€λΈμ νΈ μμ  μ΄λ―Έμ§

> π‘ λ€μ μ€ν°λμμ [μ€λΈμ νΈ](https://book.naver.com/bookdb/book_detail.naver?bid=15007773) μμ μΌλ‘ κ³΅λΆν΄λ³΄λ©΄ μ’μ λ΄μ© κ°μμ.

### 1-2. μΈν°νμ΄μ€μ μ₯μ 

1. κ°λ° κΈ°κ° λ¨μΆ
   - νλ΄μμ μλ‘ λ€λ₯Έ λΆλΆμ κ°λ°ν  λ μΈν°νμ΄μ€λ§ μ μ μν΄λμ΄λ μλ‘μ κ°λ°μ΄ λλμ§ μμλ λ°λ‘ μ½λλ₯Ό μμ±ν  μ μλ€.
2. ν΄λμ€κ°μ λ?μ κ²°ν©λ
   - μ½λμ μ’μμ μ€μ΄κ³  μ μ§λ³΄μμ±μ λμΌ μ μλ€.
3. νμ€ν κ°λ₯
   - νμ μμλ μΌμ ν νμ ννλ‘ κ°λ°μ ν  μ μκ² νλ€.
   
### 1-3. μΈν°νμ΄μ€ νΉμ§

1. νλλ‘λ μμλ§ κ°λ₯ (λ³μλ λΆκ°λ₯ νλ€.) : λ¬΄μ‘°κ±΄ `public static final`λ‘ μ μΈ λλ©° μλ΅ κ°λ₯
2. μΆμ λ©μλ μ‘΄μ¬ : λ¬΄μ‘°κ±΄ public abstractμΌλ‘ μ μΈλλ©° μλ΅ κ°λ₯

### 1-4. μΆμν΄λμ€μ νκ³

μΆμν΄λμ€λ λ¨μΌ μμμ μμΉμΌλ‘ νλ€.

> μμμ κ°μ₯ ν° λ¬Έμ μ μ μΊ‘μνλ₯Ό μλ°νλ€λ κ²κ³Ό μ€κ³κ° μ μ°νμ§ μλ€λ κ².  
> `μ½λμ μ¬μ¬μ©μ μν΄μλ μμμ΄ μλλΌ ν©μ±`μ μ¬μ©νλ κ²μ΄ λ°λμ§νκ³  `μμμ νμκ³μΈ΅μ κ΅¬μ‘°ννκΈ° μν΄ μ¬μ©`ν΄μΌ νλ€.  
> μμ‘΄μ±μ μν μν₯μ΄ μ μ κ²½μ°μλ μΆμνμ μμ‘΄νκ³  μμ‘΄μ±μ λͺμμ μΌλ‘ λλ¬λ΄λ κ²μ΄ μ’μ μ€κ³ μ΅κ΄μ΄λ€.

#### 1-4-1. λ©μλ μ€λ²λΌμ΄λ© λ¬Έμ  ν΄κ²°νμ§λ§..

μλ₯Ό λ€μ΄, Person μΈν°νμ΄μ€μμ λ©μλ `a(), b(), c(), d()` λ₯Ό μ κ³΅νλ€κ³  νμ.

μ΄λ₯Ό κ΅¬ννλ Jun, Hong, Chang ν΄λμ€κ° μ‘΄μ¬νκ³  κ°κ° νμν λ©μλλ νλμ© λ°μ μλ μν©μ΄λ€.

<img src = "https://user-images.githubusercontent.com/42997924/140614888-d485e2bd-cb81-4a38-9f48-77f1f4380717.png" width="50%" height="50%">

νμν λ©μλκ° μλλΌλ μΈν°νμ΄μ€ νΉμ±μ, λͺ¨λ  λ©μλλ₯Ό μ€λ²λΌμ΄λ© ν΄μΌλ§ νλ μ΄μκ° μλ€.

Java8 μ΄μ μλ μΆμν΄λμ€λ₯Ό νμ©νμ¬ μ΄ λ¬Έμ λ₯Ό ν΄κ²°νμλ€.

<img src = "https://user-images.githubusercontent.com/42997924/140615064-bc7356bf-05cf-4801-b1b2-5e3ffcbfe32e.png" width="50%" height="50%">

#### 1-4-2. ν΄κ²°νμ§ λͺ»ν λ¬Έμ 

- λ§€λ² Adapter μΆμν΄λμ€λ₯Ό λ§λ€μ΄μΌνλ€.
- λ€μ€ μμμ΄ λΆκ°λ₯νλ€.

> μ΄λ¬ν λ¬Έμ λ₯Ό ν΄κ²°νκΈ° μν΄ Java8 μΈν°νμ΄μ€μλ μ΄λ€ λ³νκ° μμλμ§ μμλ³΄μ.

## 2. Java8 λ³ν: μΈν°νμ΄μ€μ κΈ°λ³Έ λ©μλμ μ€νν± λ©μλ

> Java 8 λΆν°λ μΈν°νμ΄μ€μλ λ©μλ μ μΈλΏλ§ μλλΌ `default method` μ `static method`λ₯Ό ν΅ν΄ κ΅¬νμ΄ κ°λ₯ν΄μ‘λ€.

### 2-1. Default method (κΈ°λ³Έ λ©μλ)

> μΆμ λ©μλκ° μλλΌ κ΅¬νμ΄ λ λ©μλλ₯Ό μ κ³΅νλ λ°©λ²μΌλ‘ μ½λμ μ€λ³΅μ νΌνκ³  κΈ°λ₯μ μΆκ°ν  λ μ¬μ©

- `νμ νΈνμ±` μ΄ κ°μ₯ ν° λͺ©μ μΌλ‘ μ΄λ€ μΈν°νμ΄μ€μ κΈ°λ₯μ μΆκ°νμλ μ΄λ₯Ό μμ, κ΅¬νν ν΄λμ€λ€μ΄ κΉ¨μ§μ§ μκ² νκΈ°μν λͺ©μ μΌλ‘ default λ©μλλ₯Ό μ¬μ©ν  μ μλ€. λνμ μΌλ‘ Collectionμ ofλ forEach λ±μ΄ μλ€.
- `λλ€`λ₯Ό νμ©ν  μ μλ κΈ°μ  μ€ νλλ‘ μΌμ’μ `λ¬΄νν λ°μ΄ν°μ νλ¦`. λ°°μ΄ λλ μ»¬λ μ, Mapκ³Ό κ°μ μλ£κ΅¬μ‘°λ€μ νλμ λ°μ΄ν° νλ¦μΌλ‘ λ§λ¦μΌλ‘μ ν¨μ μ¬λ¬κ°λ₯Ό chaningνμ¬ μ½λμ μμ μ€μ΄κ³  κ°κ²°νκ² ννν  μ μκ² ν΄μ€λ€.

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

Foo μΈν°νμ΄μ€λ₯Ό κ΅¬ννλ ν΄λμ€κ° λ§μμ§λ©΄, μλ‘μ΄ κΈ°λ₯μ λ©μλλ₯Ό μ μΈνμ΄ μΆκ°λμ λ κ΅¬νμ²΄ λͺ¨λ μ»΄νμΌ μλ¬κ° λ°μ.

μ΄λ₯Ό ν΄κ²°νκΈ° μν΄, `Default Method` λ±μ₯!

```java
public interface Foo {
    void printName();
		// κΈ°λ³Έ λ©μλ(Default Method)
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

- `μ₯μ ` ν΄λΉ μΈν°νμ΄μ€λ₯Ό κ΅¬νν ν΄λμ€λ₯Ό κΉ¨νΈλ¦¬μ§ μκ³  μ κΈ°λ₯μ μΆκ°ν  μ μμ.
- `μ£Όμν  μ ` κΈ°λ³Έ λ©μλλ κ΅¬νμ²΄κ° λͺ¨λ₯΄κ² μΆκ°λ κΈ°λ₯μ΄κΈ° λλ¬Έμ, λ¬Έμ κ° μκΈΈ μ μμ.

```java
public interface Foo {
    void printName();  
    default void printNameUpperCase() {
        System.out.println(getName().toUpperCase());
    }
    String getName(); // Name κ°μ΄ ν­μ μλ€κ³  λ³΄μ₯ν  μ μλ€.                    
    // name = null -> NullPointException
}
```

- **μ»΄νμΌ μλ¬λ μλμ§λ§ κ΅¬νμ²΄μ λ°λΌ λ°νμ μλ¬ λ°μ κ°λ₯**
- **λ°λμ λ¬Έμν** (`@ImplSpec` μλ°λ νκ·Έ μ¬μ©)

```java
/**
 * @ImplSpec
 * μ΄ κ΅¬νμ²΄λ getName()μΌλ‘ κ°μ Έμ¨ λ¬Έμμ΄μ λλ¬Έμλ‘ λ³ν ν μΆλ ₯νλ€.
 */
default void printNameUpperCase() {
    // null λ°©μ§ νμ¬ κ΅¬ν
    if(getName() == null) System.out.println("");
    System.out.println(getName().toUpperCase());
}
```

μ΄λλ λ¬Έμ κ° λ°μνλ κ²½μ°λ κ΅¬ν ν΄λμ€μμ `Overriding` μ μ΄μ©ν΄ μ¬μ μ ν  μ μλ€.

- `μ£Όμν  μ ` **Objectκ° μ κ³΅νλ κΈ°λ₯**μ default λ©μλλ‘ μ κ³΅ν  μ μλ€.
  - `equals`, `hashCode`, `toString` λ±
  - κ΅¬νμ²΄κ° μ¬μ μ ν΄μΌνλ€!
  - μΈν°νμ΄μ€μμ μΆμ λ©μλλ‘ μ μΈνλ κ²μ κ΄μ°?λ€.
    - λͺ¨λ κ³΅ν΅μΌλ‘ μ κ³΅νκΈ° λλ¬Έμ μΆμ λ©μλλ‘ λΆλ₯νμ§ μλλ€.
- λ³ΈμΈμ΄ μμ ν  μ μλ μΈν°νμ΄μ€λ§ μμ  κ°λ₯νλ€.
- μΈν°νμ΄μ€λ₯Ό μμλ°λ μΈν°νμ΄μ€μμ λ€μ μΆμ λ©μλλ‘ λ³κ²½ν  μ μλ€.


<img src = "https://user-images.githubusercontent.com/42997924/140614805-36fe0bb1-6e62-4fa3-853e-6e35d2b75140.png" width="50%" height="50%">


**`Diamond Problem`**
 
- κ΅¬νμ²΄κ° μμλ°λ λ μΈν°νμ΄μ€ λͺ¨λ λμΌν κΈ°λ³Έ λ©μλκ° μλ κ²½μ°?

<img src = "https://user-images.githubusercontent.com/42997924/140612960-3e5fe0c3-2904-4278-87a9-9d5b165d2d7c.png" width="50%" height="50%">


```java
public interface Foo {
    default void getNameUpperCase() {
        ...
    }
}
public interface Bar extends Foo {
    void getNameUpperCase();  // μΆμ λ©μλλ‘ μ μΈν κ²½μ° κ΅¬ν ν΄λμ€μμ μ¬μ μ νμ  
    // μΆμ λ©μλλ‘ μ μΈ νμ§ μμ κ²½μ° μμ μΈν°νμ΄μ€ κ·Έλλ‘ μ¬μ© κ°λ₯
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

- Bar, Foo μ€ λ¬΄μμ μ¬μ©ν μ§ λͺ¨λ₯΄κΈ° λλ¬Έμ **μ»΄νμΌ μλ¬** λ°μ
- μΆ©λνλ default method μ§μ  override ν΄μΌνλ€.

### 2-2. Static method (μ μ  λ©μλ)

> μΈμ€ν΄μ€ μμ΄ μνν  μ μλ νλμ μ μ ν κ²

ν΄λΉ μΈν°νμ΄μ€λ₯Ό κ΅¬νν λͺ¨λ  μΈμ€ν΄μ€λ ν΄λΉ νμμ κ΄λ ¨λ ν¬νΌ λλ μ νΈλ¦¬ν° λ©μλλ₯Ό μ κ³΅ν  λ μΈν°νμ΄μ€μ static method μ κ³΅ κ°λ₯

```java
public interface Foo {
    void printName();

    default void hello(){
        System.out.println("Foo");
    }
    static void helloAll(){
        System.out.println("μΈμΏλ§");
    }
}
```

```java
public class App {
    public static void main(String[] args) {
        Foo foo = new DefaultFoo();
        foo.hello();
    
        Foo.helloAll(); // static λ©μλ μ¬μ©
    }
}
```

**β λ°μ΄νΈ μ½λ**

```java
public interface ExInterface {
    static String staticMethod(){
        return "μ€νν± λ©μλμλλ€";
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

μ κ·Όμ§μ μλ₯Ό μλ΅νλ©΄ κΈ°λ³Έμ μΌλ‘ publicμ΄ λΆκ²λκ³  java8μμλ static methodκ° publicλ§ κ°λ₯νμ§λ§ java9 μ΄μλΆν°λ privateλ κ°λ₯νλ€. λΉμ°ν privateμ΄κΈ° λλ¬Έμ μΈλΆμμλ μ κ·Όμ΄ λΆκ°λ₯νλ€.

**private λ©μλμ μ‘°κ±΄**

- κ΅¬νλΆκ° μ‘΄μ¬ν΄μΌ νλ€.
- ν΄λΉ μΈν°νμ΄μ€ λ΄λΆμμλ§ μ¬μ©μ΄ κ°λ₯νλ€.


## 3. μλ°8 APIμ κΈ°λ³Έ λ©μλμ μ€νν± λ©μλ

JAVA8 μμ μΆκ°λ κΈ°λ³Έ λ©μλλ‘ μΈν΄ API μ λ³νμ λν΄ μμλ³΄μ

### 3-1. Iterable

**[κ³΅μ λ¬Έμ](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html)**

> μνλ₯Ό νΈνκ² ν  μ μλ€!  
> λλΆλΆ νΌμμ μ¬μ©νλ κ²½μ°λ κ±°μ μκ³  Collectionμ ν¬ν¨λμ΄μ μ¬μ©νλ€.

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
   - forEach λ λ΄λΆ μλ¦¬λ¨ΌνΈλ₯Ό μννλ©° κ°κ°μ μμλ€μ νλΌλ―Έν°λ‘ μ λ¬λ μΌκΈν¨μμ Functional InterfaceμΈ Consumerκ° λ€μ΄μ€κ³  μ²λ¦¬.
   - λ¨μ μΆλ ₯λ§ νκΈ° λλ¬Έμ λ©μλ λ νΌλ°μ€ κΈ°λ₯μ μ΄μ©ν΄ κ°κ²°νκ² μμ±!
2. `for-of{...}`
   - κΈ°μ‘΄μλ for-of λ¬Έμ μ¬μ©ν΄ μΆλ ₯μ΄ κ°λ₯νλ€κ³  ν¨.
   - νμ§λ§ μ‘°κΈμ΄λλ§ λ κ°κ²°ν forEachλ₯Ό μ¬μ©νλ κ²μ΄ μ’μ λ³΄μ.

### 3-2. Collection

> `Iterable` μ μμλ°λ μΈν°νμ΄μ€λ€!

**[κ³΅μ λ¬Έμ](https://docs.oracle.com/javase/8/docs/api/java/util/Collection.html)**

#### 3-2-1. Spliterator

> iterator μ λΉμ·ν κ°λμ΄μ§λ§ Collection μ λΆν νλ€λ μ μμ μ°¨μ΄κ° μμ.

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
   - iteratorμ λΉμ·νμ§λ§ λΆν ν  μ μλ κΈ°λ₯μ κ°μ§ Iterator λ₯Ό λ§λ€μ΄ λ°ν
2. spliterator.tryAdvance(System.out::println)
   - iteratorμ hasNext()λ©μλμ μ μ¬
   - λ€λ§ λ΄λΆ λ©μλμ νλΌλ―Έν°λ‘ forEach()μ λμΌνκ² Functional InterfaceμΈ Consumerκ° λ€μ΄μ€κ³  λ μ΄μ λ€μ΄μ¬κ² μμ κ²½μ° falseλ₯Ό λ°ν
   - μ΄ μμ μμλ λ€μ΄μ¨ κ°μ λ¨μ μΆλ ₯νλ λ©μλ λ νΌλ°μ€λ₯Ό μ¬μ©

> μ¬κΈ°κΉμ§λ iteratorμ μ°¨μ΄κ° μλ€. trySplitμ μ¬μ©ν΄λ³΄μ.

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
[μ€ν κ²°κ³Ό]
spring
java8
=========================
yj
youngjun
*/
```

1. `Spliterator<String> trySplit = spliterator.trySplit();`

   - spliteratorμμ trySplit()λ©μλλ₯Ό νΈμΆνκ²λλ©΄ ν΄λΉ spliteratorμμ μμμλΆν° μ λ°μ μμλ₯Ό κΊΌλ΄ μλ‘μ΄ spliteratorλ₯Ό λ§λ€μ΄ λ°ν

   <img src = "https://user-images.githubusercontent.com/42997924/139379819-9390b364-6120-4b51-a766-79b4a6d81e8e.png" width="50%" height="50%">

   πΌ trySplit() μ€ν κ²°κ³Ό
2. `while(spliterator.tryAdvance(System.out::println));`

   - trySplit()μ ν΅ν΄ μμ λ String(yj, youngjun)μ΄ λΆν λμ΄ λΉ μ Έλκ°κΈ° λλ¬Έμ λ€μ λ κ°λ§ μΆλ ₯
3. `while(trySplit.tryAdvance(System.out::println));`

   - spliterator μμ κ°μ Έμ¨ μ λ κ°(yj, youngjun)μ μΆλ ₯

#### 3-2-2. removeIf

> Collection μμλ₯Ό μννλ©° μΈμλ‘ λκ²¨μ£Όλ ν¨μλ₯Ό Functional InterfaceμΈ Predicateμ λκ²¨μ€μ true μ΄λ©΄ μ­μ 

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
   - namesλ₯Ό μννλ©° κ° μμλ€ μ€ λ¨μ΄κ° 'y'λ‘ μμνλ λ¨μ΄λ₯Ό μ°Ύμ μ­μ 

#### 3-2-3. etc

> μ΄νμ μμΈν λ€λ£° μμ μ΄λ€.

- Stream()
- parallelStream()

### 3-3. Comparator

[κ³΅μλ¬Έμ](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html)

> μ λ ¬μ μ¬μ©λλ μΈν°νμ΄μ€

#### 3-3-1. reversed

> μ λ ¬μ μ­μμΌλ‘!

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
   - λ©μλ λ νΌλ°μ€λ₯Ό λ©μλ μ²΄μ΄λ λ°©μμΌλ‘ μ¬μ©ν μλ μκΈ°μ λΆλ¦¬νμ¬ Comparator νμμ Functional InterfaceμΈ compareToIgnoreCase λ₯Ό λ§λ€μ΄μ€λ€.
2. `names.sort(compareToIgnoreCase.reversed());`
   - λ―Έλ¦¬ μ μΈν΄λμ String μ μ λ ¬ κΈ°μ€ λ©μλ λ νΌλ°μ€μ `reversed()` λ©μλλ₯Ό νΈμΆν΄ μ­μμΌλ‘ μ λ ¬

> β λ§μ½, μ¬κΈ°μ λ€μ μ λ ¬μ‘°κ±΄μ μ¬μ©νμ¬ μ λ ¬μ μ΄μ΄κ°κ³ μΆλ€λ©΄ thenComparing() λ©μλλ₯Ό μ΄μ©νμ¬ μΆκ°μ μΈ μ λ ¬μ ν  μ μλ€.

#### 3-3-2. static reverseOrder() / naturalOrder()
 
- νΉμ  νμμ Comparableμ΄ μ§μ λμ΄μμΌλ©΄ μΌλ¨ κ·Έ κΈ°μ€μΌλ‘ μ­μμ΄λ  μμ°μμ΄λ  μΈνν΄μ μ λ ¬ν¨.

#### 3-3-3. etc

- static nullIsFirst() / nullIsLast()
- static comparing()

---

#### [2μ£Όμ°¨ λ€μ κΈ - Stream](README-AFTER.md)

---

**Reference**

- [λ°±κΈ°μ  μΈνλ° κ°μ : λ μλ°, Java8](https://www.inflearn.com/course/the-java-java8/dashboard)
- [Catsbi's DLog : μΈν°νμ΄μ€μ λ³ν](https://catsbi.oopy.io/3561609e-6c05-4209-a833-c5bb632b5aaa)
- https://github.com/gowoonsori/my-tech/blob/master/java/java8/interface.md
- https://github.com/gowoonsori/my-tech/blob/master/java/java8/stream.md
- https://dev-milk.tistory.com/5
- https://futurecreator.github.io/2018/08/26/java-8-streams-advanced/
- https://mangkyu.tistory.com/115