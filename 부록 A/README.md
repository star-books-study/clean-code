# 부록 A. 동시성2
## 📌 들어가며

단일 스레드로 동작하던 서버를 다중 스레드로 변경하는 것과 코드를 깨끗하게 변경하는 내용을 다룬다.

## 📌 클라이언트 / 서버 예제

- 서버는 소켓을 열어놓고 클라이언트가 연결하기를 기다린다
- 클라이언트는 소켓에 연결해 요청을 보낸다

### 서버

- 서버의 성능이 만족스러운지 확인하는 테스트 케이스
    
    ```java
    @Test(timeout=10000)
    public void shouldRunUnder10Seconds() throws Exception {
    	Thread[] threads = createThreads();
    	startAllThreads(threads);
    	waitForAllThreadsToFinish(threads);
    }
    ```
    
- 만약 테스트가 실패한다면? 다중스레드 사용 시 성능을 높이기 위해서는 우선 애플리케이션이 어디서 시간을 보내는지 알아야 한다
- 가능성은 다음 두 가지다
    - I/O - 소켓 사용, 데이터베이스 연결, 가상 메모리 스와핑 기다리기 등에 시간을 보낸다
    - 프로세서 - 수치 계산, 정규 표현식 처리, 가비지 컬렉션 등에 시간을 보낸다
- 프로그램이 주로 프로세서 연산에 시간을 보낸다면, 새로운 하드웨어를 추가해 성능을 높여 테스트를 통과하는 방식이 적합하다
    - 프로세서 연산에 시간을 보내는 프로그램은 스레드를 늘인다고 빨라지지 않는다
- 프로그램이 주로 I/O 연산에 시간을 보낸다면, 동시성이 성능을 높여주기도 한다
    - 시스템 한쪽이 I/O를 기다리는 동안에 다른 쪽이 뭔가를 처리해 노는 CPU를 효과적으로 사용할 수 있다

### 스레드 추가하기

- 서버의 process 함수가 주로 I/O 연산에 시간을 보낸다면, 한 가지 방법으로, 다음처럼 스레드를 추가한다.
    
    ```java
    void process(final Socket socket) {
    	if (socket == null)
    		return;
    	
    	**Runnable clientHander = new Runnable() {**
    		public void run() {
    			try {
    				String message = MessageUtils.getMessage(socket);
    				MessageUtils.sendMessage(socket, "Processed: " + message);
    				closeIgnoringException(socket);
    			} catch (Exception e) {
    				e.printStackTrace();
    			}
    		}
    	};
    	**Thread clientConnection = new Thread(clientHandler);
    	clientConnection.start();**
    }
    ```
    

### 서버 추가하기

- 위에서 고친 서버가 만드는 스레드 수는 JVM이 허용하는 수까지 가능하다
- 대다수 간단한 시스템은 괜찮지만 공용 네트워크에 연결된 수많은 사용자를 지원하는 시스템이라면 사용자가 몰릴 때 시스템 동작이 멈출 수도 있다
- process 함수가 지는 책임 너무 많다
    - 소켓 연결 관리
    - 클라이언트 처리
    - 스레드 정책
    - 서버 종료 정책
- SRP를 위반한다
    - 스레드를 관리하는 코드는 스레드만 관리해야 한다
- 앞서 열거한 책임마다 클래스를 만든다면 스레드 관리 전략이 변할 때 전체 코드에 미치는 영향이 작아지며 다른 책임을 간섭하지 않는다
- 다음은 각 책임을 클래스로 분할한 서버 코드다
    
    ```java
    public void run() {
    	while(keepProcessing) {
    		try {
    				ClientConnection clientConnection = connectionManager.awaitClient();
    				ClientRequestProcessor requestProcessor
    					= new ClientRequestProcessor(clientConnection);
    				clientScheduler.schedule(requestProcessor);
    			} catch (Exception e) {
    				e.printStackTrace();
    			}
    		}
    	};
    	connectionManager.shutDown();
    }
    ```
    
- 이제 스레드에 관련된 코드는 모두 ClientSchedular라는 클래스 한 곳에 위치한다
- 서버에 동시성 문제가 생긴다면 살펴본 코드는 단 한 곳이다
    
    ```java
    public interface ClientScheduler {
    	void schedule(ClientRequestProcessor requestProcessor);
    }
    ```
    
- 동시성 정책은 구현하기 쉽다
    
    ```java
    public class ThreadPerRequestScheduler implements ClientScheduler {
    	public void schedul(final ClientRequestProcessor requestProcessor) {
    		Runnable runnable = new Runnable() {
    			public void run() {
    				requestProcessor.process();
    			}
    		};
    		Thread thread = new Thread(runnable);
    		thread.start();
    	}
    }
    ```
    
- 스레드 관리르 한 곳에 몰았으니 스레드를 제어하는 동시성 정책을 바꾸기도 쉬워진다
    
    ```java
    import java.util.concurrent.Executor;
    import java.util.concurrent.Executors;
    
    public class ExecutorClientScheduler implements ClientScheduler {
    	Executor executor;
    	
    	public ExecutorClientScheduler(int availableThreads) {
    		executor = Executors.newFixedThreadPool(availableThreads);
    	}
    
    	public void schedule(final ClientRequestProcessor requestProcessor) {
    		Runnable runnable = new Runnable() {
    			public void run() {
    				requestProcessor.process();
    			}
    		};
    		executor.execute(runnable);
    	}
    }
    ```
    

### 결론

- 단일 스레드 시스템을 다중 스레드 시스템으로 변환해 시스템 성능을 높이는 방법과 테스트 프레임워크에서 시스템 성능을 검증하는 방법을 소개했다

## 📌 가능한 실행 경로

```java
public class IdGenerator {
    int lastIdUsed;
    
    public int incrementValue() {
        return ++lastIdUsed;
    }
}
```

- IdGenerator 인스턴스는 그대로지만 스레드가 두 개이며, 각 스레드가 incrementValue 메서드를 한 번씩 호출한다면 가능한 결과는 무엇일까?
- `lastIdUsed` 초깃값을 93으로 가정할 때 가능한 결과는 다음과 같다
    - 스레드 1이 94를 얻고, 스레드2가 95를 얻고, lastIdUsed가 95가 된다
    - 스레드 1이 95를 얻고, 스레드2가 94를 얻고, lastIdUsed가 95가 된다
    - 스레드 1이 94를 얻고, 스레드2가 94를 얻고, lastIdUsed가 94가 된다
- 이처럼 다양한 결과가 나오는 이유를 알려면 가능한 실행 경로 수와 JVM의 동작 방식을 알아야 한다

### 경로 수

- 루프나 분기가 없는 명령 N개를 스레드 T개가 차례로 실행한다면 가능한 경로수는 다음과 같다

$$
(NT)!\over N!^T
$$

- 우리가 사용한 자바 코드 한 줄은 N=8, T=2이므로 계산하면 가능한 경로 수가 12,870개다
- 그러나 만약 메서드를 이렇게 변경한다면 **스레드가 N개라면 가능한 경로 수는 N개가 된다**
    
    ```java
    public synchronized void increamentValue() {
    	++lastIdUsed;
    }
    ```
    

### 심층 분석

- **원자적 연산(atonic operation) : 중단이 불가능한 연산**

```java
public class Example {
	int lastId;
	
	public void resetId() {
		lastID = 0;
	}
	
	public int getNextId() {
		++lastID;
	}
}
```

- int형 변수인 lastId에 0을 할당하는 연산은 원자적이다
- 그러나 타입을 int에서 long으로 바꾸면 원자적 연산이 아니다
    - 64bit 값을 할당하는 연산은 32bit 값을 할당하는 연산 두 개로 나눠진다
- 전처리 증가 연산자 `++` 는 중단이 가능하므로 원자적 연산이 아니다
- 바다음 정의 세 개를 명심하기 바란다
    - **프레임** - 모든 메서드 호출에는 프레임이 필요하다. 프레임은 반환 주소, 메서드로 넘어온 매개변수, 메서드가 정의하는 지역 변수를 포함한다. 프레임은 호출 스택을 정의할 때 사용하는 표준 기법이다. 현대 언어는 호출 스택으로 기본 함수/메서드 호출과 재귀적 호출을 지원한다.
    - **지역 변수** - 메서드 범위 내에 정의되는 모든 변수를 가리킨다. 정적 메서드를 제외한 모든 메서드는 기본적으로 this라는 지역 변수를 갖는다. this는 현재 객체, 즉 현재 스레드에 가장 최근에 메시지를 받아 메서드를 호출한 객체를 가리킨다.
    - **피연산자 스택** - JVM이 지원하는 명령 대다수는 매개변수를 받는다. 피연산자 스택은 이런 매개변수를 저장하는 장소다. 피연산자 스택은 표준 LIFO 자료구조다.

### 결론

- 어떤 연산이 안전하고 안전하지 못한지 파악할 만큼 메모리 모델을 이해하고 있어야 한다
- ++ 연산은 분명히 원자적 연산이 아니다.
- 즉, 다음을 알아야 한다는 말이다
    - 공유 / 객체 값이 있는 곳
    - 동시 읽기 / 수정 문제를 일으킬 소지가 있는 코드
    - 동시성 문제를 방지하는 방법

## 📌 라이브러리를 이해하라

### Executor 프레임워크

- 스레드 풀링으로 정교한 실행을 지원한다
- java.util.concurrent 패키지에 속하는 클래스다
- 애플리케이션에서 스레드는 생성하나 스레드 풀을 사용하지 않는다면 혹은 직접 생성한 스레드 풀을 사용한다면 고려할 클래스
- 코드가 깔끔해지고, 이해하기 쉬워지고, 크기가 작아진다

### 스레드를 차단하지 않는 방법

- 스레드를 차단하는 예제
    
    ```java
    public class ObjectWithValue {
    	private int value;
    	public void synchronized incrementValue() { ++value; }
    	public int getValue() { return value; }
    }
    ```
    
- 스레드를 차단하지 않는 코드
    
    ```java
    public class ObjectWithValue {
    	private AtomicInteger value = new AtomicInteger(0);
    	
    	private void incrementValue() {
    		value.incrementAndGet();
    	}
    	public int getValue() {
    		return value.get();
    	}
    }
    ```
    
    - 기본 유형이 아니라 객체 사용
    - ++ 연산자가 아니라 incrementAndGet() 메서드 사용
- 스레드를 차단하지 않는 코드가 항상 훨씬 더 빠르다
- `synchronized`는 둘째 스레드가 같은 값을 갱신하지 않더라도 무조건 락부터 건다
    
    ⇒ 락을 거는 대가는 비싸다
    
- 락을 거는 쪽보다 문제를 감지하는 쪽이 거의 항상 더 효율적이다

### 다중 스레드 환경에서 안전하지 않은 클래스

- 본질적으로 다중 스레드 환경에서 안전하지 않은 클래스가 있다. 몇 가지 예는 다음과 같다
    - SimpleDateFormat
    - 데이터베이스 연결
    - java.util 컨테이너 클래스
    - 서블릿
- 참고로, 몇몇 집합(collection) 클래스는 스레드에 안전한 메서드를 제공한다
- 하지만 그런 메서드 여럿을 호출하는 작업은 스레드에 안전하지 않다
- 스레드에 안전한 집합 클래스를 사용하자 (ex.`putIfAbsent()`)

## 📌 메서드 사이에 존재하는 의존성을 조심하라

- 메서드 사이에 존재하는 의존성 때문에 발생하는 버그는 시스템을 출시하고도 오랜 시간이 지나서야 발생하는 버그로, 추적하기 어렵다
- 해결방안은 세 가지다
    - 실패를 용인한다
    - 클라이언트를 바꿔 문제를 해결한다. 즉, 클라이언트-기반 잠금 메커니즘을 구현한다
    - 서버를 바꿔 문제를 해결한다. 서버에 맞춰 클라이언트ㅗ 바꾼다. 즉, 서버-기반 잠금 메커니즘을 구현한다

## 📌 작업 처리량 높이기

- URL 목록을 받아 네트워크에 연결한 다음에 각 페이지를 읽어오는 코드를 짠다고 가정하자
- 각 페이지를 읽어와 분석해 통계를 구한다
- 페이지를 모두 읽은 후에는 통계 보고서를 출력한다

### 작업 처리량 계산 - 단일 스레드 환경

- 이제 간단한 계산을 해보자. 편의상 다음을 가정한다.
    - 페이지를 읽어오는 평균 I/O 시간: 1초
    - 페이지를 분석하는 평균 처리 시간: 0.5초
    - 처리는 CPU 100% 사용, I/O는 CPU 0% 사용
- 스레드 하나가 N 페이지를 처리한다면 총 실행 시간은 1.5초 * N이다

### 작업 처리량 계산 - 다중 스레드 환경

- 여러 스레드를 사용하면 프로세서를 사용하는 페이지 분석과 I/O를 사용하는 페이지 읽기를 겹쳐 실행할 수 있다
- 페이지 읽기 한 번은 페이지 분석 두 번과 겹침이 가능하다
    
    ⇒ 단일 스레드 환경과 비교해 처리율은 세 배다
    

### 데드락

- 다음 네 가지 조건을 모두 만족하면 데드락이 발생한다
    - 상호 배제
    - 잠금 & 대기
    - 선점 불가
    - 순환 대가

### 다중 스레드 코드 테스트

- 몬테 카를로 테스트
- 스레드 코드 테스트를 도와주는 도구
    - IBM의 ConTest

### 결론

- 다중 스레드 코드를 깨끗하게 유지하는 방법을 읽혔다
- 동시 갱신을 논했으며, 동시 갱신을 방지하는 깨끗한 동기화/잠금 방법을 소개했다
- 스레드가 I/O 위주 시스템의 처리율을 높여주는 이유와 실제로 처리율을 높이는 방법을 살펴봤다
- 데드락을 논했으며, 깔끔하게 데드락을 방지하는 기법도 열거했다
- 마지막으로 보조 코드를 추가해 동시성 문제를 사전에 노출하는 전략을 소개했다

  
