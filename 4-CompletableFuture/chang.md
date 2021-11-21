# CompletableFuture

## Concurrent 프로그래밍

Concurrent → 동시에 여러가지를 할수 있도록 하는 것

자바에서 지원하는 컨커런트 프로그래밍

- 멀티프로세싱
- 멀티 쓰레드

쓰레드는 순서를 보장하지 못한다.

### 쓰레드를 사용하는 방법

1. thread 상속해서 사용
2. Runnable()사용 자바 8부터는 람다처럼 사용가능.

쓰레드 기능중  사용하는 기능

1. Sleep → 쓰레드가 자고 있으면 일단 다른 쓰레드를 먼저 실행시킨다.
2. Interrupt → Try catch를 통해서 ⇒ 자고 있는 쓰레드를 깨움. InterruptedException을 통해서 Return으로 종료시킬수도 있음.
3. Join → 다른 쓰레드를 기다림.  (대기하고 있기때문에, `InterruptedException`도 문제임.)

프로그래머가 수십, 수백개를 동시에 다 관리하는건 그걸 프로그래밍적으로 짜는건 말도 안됨. (천재야 가능할수가 있겠지만)

## Excutors

고수준(High-Level) Concurrency 프로그래밍

- 쓰레드를 만들고 관리하는 작업을 애플리케이션에서 분리, Executors에게 위임.

대부분은 Excutors를 쓸일은 없고, ExcutorService를 쓸일이 더 많음. 기능이 더 많기 때문 그리고

SecheduleExcutedService 역시 스케쥴링가능한 서비스를 만들 수 있다.

ExecutorService executorService = Executors.newSingleThreadExecutor()를 통해서 씽글스레드 서비스를 만들수 있으며, sumit을 통해서 작업을 보낼수 있다.

이 서비스는 종료를 하지 않는 한 계속 대기중.

Shutdown()을 통해서 종료시킬 수 있다.  ⇒ gracefulShutdown 을 시키는데 (일단 종료되는걸 확인하고 종료)

ShutdownNow()⇒ 일단 무조건 종료

ThreadPool을 통해서  아래처럼 작동함.

![ExecutorService.gif](img/ExecutorService.gif)

### Executors의 쓰레드 제공 방식

**SingleThreadExecutor →** 한개의 쓰레드만 사용함. 예약 순서대로 작동함

**newFixedThreadPool(n)→ 인자 갯수만큼 고정 쓰레드 생성**

**newCachedThreadPool()** → 필요할때 필요한 만큼 쓰레드 풀을 생성함. 이미 생성된 쓰레드 풀을 쓸 수 있어서 좋음.

사용할 수 있는 기존 스레드가 없는 경우 새 스레드가 만들어지고 풀에 추가됩니다. 60초 동안 사용되지 않는 것은 종료되고 에서 제거된다.

이 경우는 각 하나의 쓰레드마다 다 똑같은 특징을 가지지만, 특별한 특징을 만들고 싶으면, threadFactory를 통해서 특정 새로운 양식의 쓰레드를 만들면됨.

**newScheduledThreadPool()** → 일정시간, 일정 기간마다 반복되는 작업을 시키기에 좋은 쓰레드

## 쓰레드 풀의 쓰는 이유와 장/단점

**1. 프로그램 성능 저하를 방지하기 위해**

매번 발생하는 작업을 병렬처리하기 위해 쓰레드를 생성/수거하는 데 따른 부담은 프로그램 전체적인 퍼포먼스를 저하시킨다. 따라서 쓰레드풀을 만들어 놓고 사용한다.

**2. 다수의 사용자 요청을 처리하기 위해**

대규모 프로젝트에서 특히 중요하다. 다수의 사용자의 요청을 수용하고, 빠르게 처리하고 대응하기 위해 쓰레드풀을 사용한다.

## 쓰레드 풀의  장/단점

### 장점

생성/수거에 비용이 들지 않음.  → 이미 미리 다 만들었으니까.

재사용 가능하니까 자원을 효율적으로 쓰기도하며 실행 딜레이도 없음.

### 단점

너무많이 생성해놓으면 메모리 낭비 발생

Fork/Join 방식 → 분할 정복 방식으로 구현된 것. 멀티프로세싱 할때 좋은방식이다.

지금은 대부분 Runnable한 형식 Void타입으로 되어있고, 실제로 값전달은 불가능하므로, Callable을 사용하면 Return값이 존재한다.

## Callable과 Future

`Callable`은 리턴타입 존재

submit을 통해서 → Return타입으로 Future를 받아올 수 있다.

future를 통한 get을 받기 시작하면... 결과를 받아낼때까지 `Blocking`함.

상태 파악을 하기 위해서는 `isDone()`함수를 이용해서 ⇒ 현재 진행중인지? 끝났는지 확인함. (boolean 리턴)

cancle을 호출하면 ⇒ 무조건 종료.. cancle을 통한 다시 불러오면 값을 가져올 수 없는 runtime error 발생

`invokeAll`을 통해서 Array로 전환된 callable을 future를 리스트화 할 수 있다.  invockeAll의 경우 가장 늦게 끝나는 걸 기다리고 모두 출력함.

`invokeAny` → 가장 빠르게 나오는 걸 출력.

## CompletableFuture

비동기 프로그래밍을 가능케하는 인터페이스

- Future만가지고 하기 힘든일이 많았다.

→ 외부에서 완료시킬 수 없다. get자체가 블로킹이라서 get전에 열심히 다 끝내놔야하는데...  끝내놓고는 콜백 실행 불가능...

→ 예외 처리용 API 제공하지 않는다.

CompletableFuture 는 implements Future, CompletionStage를 가져옴.

명시적으로 implements를 붙이지 않아도 그냥 쓸 수 있음.

비동기적 작업

- 리턴 값이 없는 case → `runAsync()` ⇒ 쓰레드가 아예 달라지는 경우가 존재
- 리턴 값이 있는 case → `supplyAsync()`

위의 경우 `future.get()`를  통해서 작업이 실행이 됨.

콜백 제공하기 (Future만 사용하는 경우는 callback이 불가능했었다.

- `thenApply()` function받고 다른 값으로 던지기
- `thenAccept()` function 받아서 다른 작업으로 처리하는 콜백
- `thenRun()` 리턴값 받지 않고 그냥 내할것 하는 경우

ForkJoinpool 방식을 통해서 쓰레드에 열심히 분산해서 쭉쭉 뱉어내는 방식.

`CommonPool()`를 통해서 뱉어내는 방식

여러작업을 조합할 수 있는데,

`thenCompose()` → 두 작업을 서로 이어서 연결해서 처리  A ⇒ B

`thenCombine()` ⇒ 두 작업을 독립적으로 실행하고 둘 다 종료 했을때 콜백 실행

A →

B →

`allOf()` → 여러 작업(2개 이상)을 모두 실행하고 모든 작업결과에 콜백실행

이 여러 작업의 모든 작업이 같은 타입을 리턴할 수 도 없으며.. 결과적으로 null이 튀어나옴.

CompletableFuture을 array로 만들어서  stream.map()을 통해서 join을 만들고 list 타입으로 리턴하면 결과값이 나오게 만들 수 있다. //Blocking되지 않는 방식임.

`anyOf()` →  여러 작업(2개 이상) 중에 가장 빨리 끝난 하나의 결과에 콜백 실행

이건 아무거나 출력하면되니까  위에처럼 복잡하게 생각하지 않아도 구현가능

예외처리는

Exceptionally(function) ⇒ error 받아 return 할 수 있음.

handle(BiFunction)  ⇒ 정상 종료 / 에러 발생 경우 둘다 체킹 가능

멀티쓰레드 기술에 관심이 많다면 다음과 같은 키워드를 참고해보자.

```
ForkAndJoin`,`flow
```