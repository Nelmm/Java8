## Compact String와 +는 정말 빠른가?

![image-20211115105914730](C:\Users\user\Desktop\Code\facebook.png)

스터디 도중에 페이스북에서 본 기억이 나서, 공유했었던 의제이다.  **+가 StringBuilder보다빠릅니다.** 라고 언급했었는데, 막상 자세히 찾아보니까 이 원글만 보고 한번 확인해보는 과정이 필요했었으나 그리고 심지어 아래쪽을 보면, 자세히 나와있음에도 불구하고 더 빠르다는 것에 심취해 있어 잘못된 정보를 전달할뻔 했었다. 

## Compact String

Java 9버전부터 업그레이드 된 방식으로 내부적으로 String의 경우 Java8 이전까지만해도 대부분의 String은 `char[]`형으로 구현되어있었으나, 9로 넘어오면서 `byte[]`로 구현이 되어졌다. 

[Baeldung](https://www.baeldung.com/java-9-compact-string)을 참고해보자면, String은 힙메모리에 많은 부분을 차지하고 있고, 어차피 영어 같은경우는 문자하나당 1byte로 처리가능한데, 이전까지 자바는 UTF-16을 사용하면서 2 바이트를 사용했었으니 -> 이걸 Latin 1로 변경하면서 업그레이드 했다.  

막상 필요없는 바이트를 써가면서까지 공간을 2배나 차지하고 있었다는 뜻이 되버렸다.

즉, 영어로 사용하는 경우 Latin1로 변경하게 되고 / 그외의 모든 언어의 경우 UTF-16으로 처리한다.

한글을 작성하는 경우에는 큰 영향이 없어서 Compact String을 적용 받을 수 없다. 한글의 특징상 Byte가 2Byte정도 차지하고 있기때문에 실제로 Latin1을 통한 영향은 없어보인다. 



## + 연산이 진짜 빠른가?

String 연산에서 코딩테스트와 여러 상황에서 + 보다 StringBuilder가 사용되는 경우는 많습니다. 특히, 백준과 같은 코딩테스트 사이트에서 일정시간 1초로 지정되어있고 정말 많은 수의 문자열 연산을 해야하는 경우 +를 하게된다면 시간 초과가 나오는 경우가 많았다.

일단, 디 컴파일링시 + 연산은 대부분 StringBuilder를 통해 append되는 모습을 볼 수 있다. 자바 내부적으로는 실제로 + 연산을 모두 .append화 시키는데 

"+" 연산자체가 빠른가에 대해서 논의 하는거 자체가 의마가 없지 않나라는 생각이 많이 들었다.

10~100만이상 for문을 돌리면 오히려 StringBuilder의 속도 격차는 진짜 미친듯이 벌어졌다. 

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

3797 vs 15 정도로 차이가 많이 났다.  200배가량 차이가 남..

특이한점은 Java 11만의 특징인지는 모르겠으나, 

"+" 연산시 Compile할때 아예 그냥 StringBuilder로 붙혀줄거라고 생각했으나 append처리가 되는줄 알았는데,  아예 컴파일시 완전히 붙혀버린다.

```java
String a = "a";
String b = "1234555";
System.out.println(a+b+"123455"+1);
System.out.println("안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"끝!");

//컴파일시 이렇게 된다. 1이나 문자열이 그냥 아예 한 문자열로 붙혀져서 처리된다.

System.out.println(a + b + "1234551");
System.out.println("안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"안녕, "+"끝!");
```



웃긴게 바이트코드를 봐도 이미 컴파일 전부터 +연산을 다 합쳐서 String화 해버린다 



