## 검증 안된 것에 대해서 당당하게 말하기! ㅋㅋ;

스터디 도중에 [페이스북](https://www.facebook.com/groups/codingeverybody/posts/6423007531073062/?comment_id=6427580693949079)에서 본 기억이 나서 공유했었던 의제이다. **+가 StringBuilder보다 빠릅니다!** 라고 당당하게 언급했었는데, 막상 스터디 팀원들의 질문 공세에 어버버하며 재대로 대답을 못해드리게 되었다. 그 덕분에 자세히 찾아보게 되었는데, 원글을 읽고 **글의 정확성을 한번 확인해보는 과정이 필요했었으며 심지어 더 자세히 아래쪽을 보면 더 정확하게 설명이 있음에도** 불구하고 **+가 StringBuilder보다 빠릅니다!** 더 빠르다는 것에 심취해 있어 잘못된 정보를 전달할뻔 했었다. 

그래서 그런 정보를 바로 잡기도하며, 지식 정리도 해보고 싶어서 작성하게 되었다. 



## Compact String

Java 9버전부터 업그레이드 된 방식으로 내부적으로 String의 경우 Java8 이전까지만해도 대부분의 String은 `char[]`형으로 구현되어있었으나, 9로 넘어오면서 `byte[]`로 구현이 되어졌다. 

[Baeldung](https://www.baeldung.com/java-9-compact-string)을 참고해보자면, String은 힙메모리에 많은 부분을 차지하고 있고, 어차피 영어 같은경우는 문자하나당 1byte로 처리가능한데, 이전까지 자바는 UTF-16을 사용하면서 2 바이트를 사용했었으니 -> 이걸 Latin 1로 변경하면서 업그레이드 했다.  

막상 필요없는 바이트를 써가면서까지 공간을 2배나 차지하고 있었다는 뜻이 되버렸다.

즉, 영어로 사용하는 경우 Latin1로 변경하게 되고 / 그외의 모든 언어의 경우 UTF-16으로 처리한다.

한글을 작성하는 경우에는 큰 영향이 없어서 Compact String을 적용 받을 수 없다. 한글의 특징상 Byte가 2Byte정도 차지하고 있기때문에 실제로 Latin1을 통한 영향은 없어보인다. 



## + 연산이 진짜 빠른가?

String 연산은 코딩테스트와 여러 상황에서 + 보다 StringBuilder가 사용되는 경우는 많았습니다. 특히, 백준과 같은 코딩테스트 사이트에서 일정시간 1초로 지정되어있고 정말 많은 수의 문자열 연산을 해야하는 경우 +를 하게된다면 시간 초과가 나오는 경우가 많았는데요.

예전에 여럼풋이 기억난 것중 디 컴파일링시 + 연산은 대부분 StringBuilder를 통해 append되는 모습을 볼 수 있다. 자바 내부적으로는 실제로 + 연산을 모두 .append화 시키는데 "+" 연산자체가 빠른가에 대해서 논의 하는거 자체가 의마가 없지 않나라는 생각이 많이 들었다. 

이렇게 혼자서 상상한것은 의미가 없어보이고 직접 해보는게 더 좋아보였고, 확인해보았다.

### 그래서 직접 해보았다

10~100만이상 for문을 돌리는 방식으로 실험을 해보았다. 

```java
public static void main(String[] args) {
    long start = System.currentTimeMillis();
    String test = "aa"; //StringBuilder test = new StringBuilder("aa");
    for (int i = 0; i <100000; i++) {
        test= test+"aa"; // test.append("aa");
    }
    long end = System.currentTimeMillis()-start;
    System.out.println(end);
}
```

**3797 vs 15** 정도로 **+**와 **StringBuilder** 차이가 많이 났다. **200배**가량 차이가 남..

 내가 생각했었던 문자열을 컴파일시 StringBuilder로 묶어주는게 아닌가 했었는데, 막상 Compile시에서는 합쳐지는게 보여지진 않았다. 그럼 내가 생각했던것은 뭐지?

오히려 신기한점은 문자열을 아예 하나의 문자로써 완전히 붙혀버렸다. 

```java
String a = "a";
String b = "1234555";
System.out.println(a+b+"123455"+1);
System.out.println("안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"끝!");

//컴파일시 이렇게 된다. 1이나 문자열이 그냥 아예 한 문자열로 붙혀져서 처리된다.

System.out.println(a + b + "1234551");
System.out.println("안녕, 안녕, 안녕, 안녕, 안녕, 안녕, 안녕, 안녕, 안녕, 안녕, 안녕, 안녕, 안녕, 끝!"); 
```

근데 바이트 코드를 까보니까 버전별로 다른 방식으로 처리되는 점을 확인해봤는데, 그 점은 다음과 같다.

Java 11버전은 

`#0:makeConcatWithConstants:(Ljava/lang/String;)Ljava/lang/String;`
이렇게 처리한다면...

8버전은 이렇게 StringBuilder로 붙임 

```
    19: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      22: ldc           #7                  // String 1234
      24: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
```

내가 생각했던것이 바이트 코드에서 실현이 되었던것.. 그래서 Java 8과 StringBuilder과 +를 비교해보면 더 명확하지 않을까 싶어서 또 시도해보았다.

## 덧붙이기 Java 8에서 +가 느린이유 SB에 비해서 훨씬 훨씬 느린이유

+로 붙이면 바이트 코드는 다음과 같이 나오고 

```
 public static void main(java.lang.String[]);
    Code:
       0: ldc           #2                  // String !23
       2: astore_1
       3: iconst_0
       4: istore_2
       5: iload_2
       6: ldc           #3                  // int 100000
       8: if_icmpge     37
      11: new           #4                  // class java/lang/StringBuilder
      14: dup
      15: invokespecial #5                  // Method java/lang/StringBuilder."<init>":()V
      18: aload_1
      19: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      22: ldc           #7                  // String 1234
      24: invokevirtual #6                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      27: invokevirtual #8                  // Method java/lang/StringBuilder.toString:()Ljava/lang/String;
      30: astore_1
      31: iinc          2, 1
      34: goto          5
      37: getstatic     #9                  // Field java/lang/System.out:Ljava/io/PrintStream;
      40: aload_1
      41: invokevirtual #10                 // Method java/io/PrintStream.println:(Ljava/lang/String;)V
      44: return
```

StringBuilder로 하면 아래처럼 나오는데  
```
Code:
       0: new           #2                  // class java/lang/StringBuilder
       3: dup
       4: ldc           #3                  // String !23
       6: invokespecial #4                  // Method java/lang/StringBuilder."<init>":(Ljava/lang/String;)V
       9: astore_1
      10: iconst_0
      11: istore_2
      12: iload_2
      13: ldc           #5                  // int 100000
      15: if_icmpge     31
      18: aload_1
      19: ldc           #6                  // String 1234
      21: invokevirtual #7                  // Method java/lang/StringBuilder.append:(Ljava/lang/String;)Ljava/lang/StringBuilder;
      24: pop
      25: iinc          2, 1
      28: goto          12
      31: getstatic     #8                  // Field java/lang/System.out:Ljava/io/PrintStream;
      34: aload_1
      35: invokevirtual #9                  // Method java/io/PrintStream.println:(Ljava/lang/Object;)V
      38: return
```

가장중요한 것은 + 한경우 다음 로직을 타는것 처럼 보인다.  StringBuilder를 생성  초기화 적제 -> Append후 붙혀주기 -> toString으로.. 바꾼다음에 적재 -> 그리고 다시 초기화 적제-> Append를하니까 느릴 수 밖에 없는구조...

Java 11버전그러니까 (9버전부터는 makeConcatWithConstants을 통해서 획기적인 개선 그래서 둘다 똑같음.)

+를 쓰면 이렇게 나와버림.

```
 Code:
       0: ldc           #2                  // String !23
       2: astore_1
       3: iconst_0
       4: istore_2
       5: iload_2
       6: ldc           #3                  // int 100000
       8: if_icmpge     24
      11: aload_1
      12: invokedynamic #4,  0              // InvokeDynamic #0:makeConcatWithConstants:(Ljava/lang/String;)Ljava/lang/String;
```
StringBuilder를 쓰는 경우는 똑같음 근데 속도는 StringBuilder가 좀더 빠름. 대신 9버전의 concat방식이 8버전보다는 10배이상은 빨라보임.

```Java
//이런 코드를 작성하면...
Long start = System.currentTimeMillis();
String a = "1234";
for (int i = 0; i <100000; i++) {
	a+="92292929";
}
System.out.println(System.currentTimeMillis()-start);
```

Java 11: 4318 Java 8: 31110 => 헉 진짜 10배 가량 차이가 난다.  

Compile방식의 차이가 속도의 차이가 발생한다.

이런 결과를 만들어낸 `makeConcatWithConstants` 로 개선된 이유에 대해서 찾아봤는데, 이 로직을 찾는건 좀 어려웠고, 자바 9부터 **Indify String Concatenation** 를 통해서, 개선 사항에 포함되어있었는데...

원문: http://openjdk.java.net/jeps/280

오역이 있을가능성이 있지만, 맥락적으로 파악해 보았다.

결국 8버전 바이트코드와 같이 StringBuilder를 잘못 쓰는 케이스가 있기 때문에, 이러한 String을 붙이는 것에 대해서 개선을 하였고, 그것이 makeConcatWithConstants로 조금 더 빠른 속도로 +를 개선하는 방향 버전업그레이드 방향을 잡은듯했고, 실제로 반영이 되어있다.

makeConcatWithConstants의 작동 로직역시 궁금하였으나, 부족한 검색 실력으로 찾지는 못했고... 그리고 찾더라도 너무너무 딥다이브하게 들어가는 것 같아서 일단 개선 되었다는 점 그리고 개선의 이유까지만 찾아서 마무리했다.

## 결론 

'+' 는 실제로 StringBuilder보다는 느린것은 사실... 하지만 속도 자체는 8에 비해서는 말그대로 10배나 차이날정도로 빨라졌음. 매우 많은 수(최소 10만 이상)를 붙이는 것이 아닌 이상.... +를 통해서 얻는 손해는 커지 않아 보인다. 

적은 갯수의 String을 합치는 것에 대해서는 오로지 속도적인 측면에서는 그렇게 느리지 않다고 봐도 좋을 것 같다. 

출처: 

http://wonwoo.ml/index.php/post/1039

https://stackoverflow.com/questions/46512888/how-is-string-concatenation-implemented-in-java-9
