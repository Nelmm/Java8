## 애노테이션의 변화

8부터애노테이션을 타입 선언부에도 사용가능, 중복 사용 가능

타입 선언부 

```java
@Retention(RetentionPolicy.Runtime)
@Target(ElementType.TYPE_PARAMETER) -> Generic에서 사용가능.
@Target(ElementType.TYPE_USE) -> 아무대나 붙일 수 있음. 

```



## 배열 병렬 정렬

Arrays.parrallelSort() ⇒ Fork/Join 프레임워크를 사용해서 배열을 정렬하는 기능 제공

병렬 정렬 알고리듬 → 배열을 두개로 쪼개고 합치면서 정렬. 실제 효율은 같음.O(nlogN) / O(n)



## Metaspace

JVM의 여러 메모리 영역중 PermGen 메모리가 없어짐 Metaspace가 생김

PermGen은 → Heap 영역 쪽임.  제한된 크기를 가지고 있어서 OOM에러 발생할 가능성 다수 

- 클래스 메타데이터를 담는 곳.
- **Heap 영역이 아니라, Native 메모리 영역이다.**
- **기본값으로 제한된 크기를 가지고 있지 않다. (필요한 만큼 계속 늘어난다.)**
- 자바 8부터는 PermGen 관련 java 옵션은 무시한다.
- XX:MetaspaceSize=N, Metaspace 초기 사이즈 설정.
- XX:MaxMetaspaceSize=N, Metaspace 최대 사이즈 설정.

