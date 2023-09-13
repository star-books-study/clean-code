# 8장 : 경계
## 📌 들어가며

이번 장에서 배우는 내용은 외부 코드를 우리 코드에 깔끔하게 통합하는 기법과 기교

## 📌 외부 코드 사용하기

- 인터페이스 제공자와 인터페이스 사용자 사이에는 특유의 긴장이 존재
    
    - 제공자 : “더 많은 고객이 구매하려면 많은 환경에서 돌아가게 해야죠.”
    - 사용자 : “제 요구에 집중하는 인터페이스를 바라요.”
- 예시 : java.util.Map
    - 👍 기능성 & 유연성은 굿
    - 😱 위험이 큼.
        - Map 삭제 가능 / 특정 객체 유형만 저장할 수 있도록 제한하지 않음.
    
    ```java
    Map sensors = new HashMap();
    ```
    
    Sensor 객체가 필요한 코드는 다음과 같이 Sensor 객체를 가져옴.
    
    ```java
    Sensor s = (Sensor)sensors.get(sensorId);
    ```
    
    - 위와 같은 코드가 한 번이 아니라 여러 차례 나옴.
        - Map이 반환하는 Object를 올바른 유형으로 변환할 책임은 사용하는 클라이언트에 있음.
        - 깨끗한 코드로 보기 어려움.
    - 제네릭스를 사용하면 코드 가독성 UP
        
        ```java
        Map<String, Sensor> sensors = new HashMap<Sensor>();
        ...
        Sensor s = sensors.get(sensorId);
        ```
        
        - BUT Map<String, Sensor>가 “사용자에게 필요하지 않은 기능까지 제공”
    - Map을 좀 더 깔끔하게 사용한 코드
        
        ```java
        public class Sensors {
        	private Map sensors = new HashMap();
        	
        	public Sensor getById(String id) {
        		return (Sensor) sensors.get(id);
        	}
        
        	// 이하 생략
        }
        ```
        
        - 경계 인터페이스인 Map을 Sensors 안으로 숨김.
            
            ⇒ Map 인터페이스가 변경하더라도 나머지 프로그램에는 영향을 미치지 않음.
            
            ⇒ 프로그램에 필요한 인터페이스만 제공
            
- Map을 사용할 때마다 위와 같이 캡슐화💊 하라는 이야기는 아님. Map을 여기저기 넘기지 말라는 말!
- Map과 같은 경계 인터페이스를 이용할 때에는 이를 이용하는 클래스나 클래스 계열 밖으로 노출되지 않도록 주의
    - Map 인스턴스를 공개 API의 인수로 넘기거나 반환 값으로 사용하지 않는다.

## 📌 경계 살피고 익히기

- 외부 코드를 가져와서 쓸 때 우리 자신을 위해 코드를 테스트하는 편이 바람직
- 외부 코드를 익히기 & 통합하는 건 어려운 일
    - BUT 다르게 접근해보자.
    - 곧바로 우리쪽 코드를 작성해 외부 코드를 호출하는 대신 먼저 간단한 테스트 케이스를 작성해 외부 코드를 익히면 어떨까?
        
        ⇒ ***학습 테스트***
        
- 학습 테스트는 프로그램에서 사용하려는 방식대로 외부 API를 호출함.
    - 학습 테스트는 API를 사용하려는 목적에 초점을 맞춤.

## 📌 log4j 익히기

- 로깅 기능을 직접 구현하는 대신 아파치의 log4j 패키지를 사용한다 가정
- 여러 시행 착오(오류도 고치고 클래스도 추가하고 테스트도 돌리고) 끝에 얻은 log4j에 대한 이해를 바탕으로 얻은 지식을 단위 테스트 몇 개로 표현
    
    ```java
    public class LogTest {
    	private Logger logger;
    	
    	@Before
    	public void initialize() {
    		logger = Logger.getLogger("logger");
    		logger.removeAllAppenders();
    		Logger.getRootLogger().removeAllAppenders();
    	}
    
    	@Test
    	public void basicLogger() {
    		BasicConfigurator.configure();
    		logger.info("basicLogger");
    	}
    	
    	@Test
    	public void addAppenderWithStream() {
    		logger.addAppender(new ConsoleAppender {
    			new PatternLayout("%p %t %m%n"),
    			ConsoleAppender.SYSTEM_OUT));
    			logger.info("addAppenderWithStream");
    	}
    	
    	@Test
    	public void addAppenderWithoutStream() {
    		new PatternLayout("%p %t %m%n")));
    		logger.info("addAppenderWithoutStream");
    	}
    }
    ```
    

## 📌 학습 테스트는 공짜 이상이다.

- 학습 테스트는 투자하는 노력보다 얻는 성과가 더 큼.
- 학습 테스트를 이용한 학습이 필요하든 그렇지 않든, 실제 코드와 동일한 방식으로 인터페이스를 사용하는 테스트 케이스가 필요
- 이런 경계 테스트가 있으면 패키지의 새 버전으로 이전하기 쉬워짐.

## 📌 아직 존재하지 않는 코드를 사용하기

- 경계와 관련해 또 다른 유형 → 아는 코드와 모르는 코드 분리하는 경계
- 우리가 알려해도 알 수 없는 코드 영역이 있음.
- 우리가 통제하지 못하며 정의되지도 않은 부분이라 저쪽 코드를 모를 때에는 잘 모를 때 우리가 바라는 인터페이스를 정의
    
    ⇒ 우리가 인터페이스를 전적으로 통제함.
    
    ⇒ 코드 가독성 UP, 코드 의도 분명
    

## 📌 깨끗한 경계

- 경계에서는 흥미로운 일이 많이 벌어짐 → 대표적으로 ***변경***
- 소프트웨어 설계가 우수하면?
    
    ⇒ 많은 투자와 재작업이 필요 없음
    
- 통제하지 못하는 코드를 사용할 때
    
    ⇒ 너무 많은 투자를 하거나 향후 변경 비용이 지나치게 커지지 않도록 각별히 주의
    
- 경계에 위치하는 코드는 깔끔히 분리
- 기대치를 정의하는 테스트 케이스도 작성
- 외부 패키지에 의존하는 대신 통제가 가능한 우리 코드에 의존하는 편이 훨씬 좋음.
- 외부 패키지를 호출하는 코드를 최대한 줄여 경계를 관리하자.