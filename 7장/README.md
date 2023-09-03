# 7장 : 오류 처리
## 📌 들어가며

깨끗한 코드와 오류 처리는 확실히 관련성이 있다.

- 상당수 코드 기반 전적으로 오류 처리 코드에 좌우된다.

## 📌 오류 코드보다 예외를 사용해라
- **잘못된 예시**
    - 예외를 지원하지 않는 경우에는 에는 오류 플래그 설정 or 호출자에게 오류 코드 반환했다.
    
    ```java
    public class DrviceController {
    	...
    	public void sendShutDown() {
    		DeviceHandle handle = getHandle(DEV1);
    		// 디바이스 상태를 점검한다.
    		if (handle != DeviceHandle.INVALID) {
    			// 레코드 필드에 디바이스 상태를 저장한다.
    			retrieveDeviceRecord(handle);
    			// 디바이스가 일시정지 상태가 아니라면 종료한다.
    			if (record.getStatus() != DEVICE_SUSPENDED) {
    				pauseDevice(handle);
    				clearDiviceWorkQueue(handle);
    				closeDivice(handle);
    			} else {
    				logger.log("Device suspended.  Unable to shut down");
    			}
    		} else {
    			logger.log("Invalid handle for: " + DEV1.toString());
    		}
    	}
    	...
    }
    ```
    
    - 함수를 호출한 즉시 오류를 확인해야 하기 때문에 호출자 코드가 복잡해짐.


    - 오류가 발생하면 예외를 던지는 게 낫다.
- **올바른 예시**
    
    ```java
    public class DeviceController {
    	...
    
    	public void sendShutDown() {
    		try {
    			tryToShutDown();
    		} catch (DeviceShutDownError e) {
    			logger.log(e);
    		}
    	}
    
    	private void tryToShutDown() throws DeviceShutDownError {
    		DeviceHandle handle = getHandle(DEV1);
    		DeviceRecord record = retrieveDeviceRecord(handle);
    		
    		pauseDevice(handle);
    		clearDiviceWorkQueue(handle);
    		closeDivice(handle);
    	}
    
    	private DeviceHandle(DeivceID id) {
    		...
    		throw new DeviceShutDownError("Invalid handle for: " + id.toString());
    		...
    	}
    	
    	...
    }
    ```
    
    - (오오 이해완료… 근데 이런 코드를 어떻게 짜지… 막막하네..)

## 📌 Try-Catch-Finally 문부터 작성하라
- try-catch-finally 문에서 try 블록에 들어가는 코드를 실행하면 어느 시점에서든 실행이 중단된 후 catch 블록으로 넘어갈 수 있다.
- try-catch-finally 문으로 작성하면 try 블록에서 무슨 일이 생기든지 호출자가 기대하는 상태를 정의하기 쉬워짐.
- 예제 : 파일이 없으면 예외를 던지는지 알아보는 단위 테스트
    
    ```java
    @Test(expected = StorageException.class)
    public void retrieveSectionShouldThrowOnInvalidFileName() {
    	sectionStore.retrieveSection("invalid - file");
    }
    ```
    
    단위 테스트에 맞춰 코드를 구현함.
    
    ```java
    public List<RecordedGrip> retrieveSectioin(String sectionName) {
    	// 실제로 구현할 때까지 비어 있는 더미를 반환한다.
    	return new ArrayList<RecordedGrip>();
    }
    ```
    
    그런데 코드가 예외를 던지지 않으므로 단위 테스트 실패…. 잘못된 파일의 접근을 시도하게 구현을 변경하자. 밑에는 예외 던지는 코드
    
    ```java
	public List<RecordedGrip> retrieveSection(String sectionName) {
		try {
			FileInputStream stream = new FileInputStream(sectionName)
		} catch (Exception e) {
			throw new StorageException("retrieval error", e);
		}
		return new ArrayList<RecordedGrip>();
	}
	```
	- 먼저 강제로 예외를 일으키는 테스트 케이스를 작성한 후 테스트를 통과하게 코드를 작성하는 방법 권장
    - 그럼 자연스럽게 `try` 블록의 트랜잭션 범위부터 구현하게 되므로 범위 내에서 트랜잭션 본질을 유지하기 쉬워짐.
    - 아직 뭔소린지 잘

## 📌 미확인(unchecked) 예외를 사용하라

- checked vs unchecked
    - 처음엔 checked 예외가 멋진 아이디어로 여겨짐.
    - 지금은 아님.
- 확인된 예외의 단점
    - OCP를 위반
    - 메서드에서 확인된 예외를 던졌는데 catch 블록이 세 단계 위에 있다면 그 사이 메서드 모두가 선언부에 해당 예외를 정의해야 함.
        - 하위 단계에서 코드를 변경하면 상위 메서드를 전부 고쳐야 함.
        - 최하위 단계에서 최상위 단계까지 연쇄적인 수정이 일어날 수도.
            
            ⇒ 캡슐화가 깨짐.
            
- 때로는 확인된 예외도 유용
    - 하지만 일반적인 애플리케이션은 의존성이라는 비용이 이익보다 크다.

## 📌 예외에 의미를 제공하라

- 예외를 던질 때는 전후 상황을 충분히 덧붙인다.
    
    ⇒ 오류 발생 원인과 위치 찾기 쉬움.
    
- 오류 메시지에 정보를 담아 예외와 함께 던진다.
- 실패한 연산 이름과 실패 유형도 언급
- 로깅 기능을 사용한다면 catch 블록에서 오류를 기록하도록 충분한 정보 넘겨주기

## 📌 호출자를 고려해 예외 클래스를 정의하라

- 오류 분류 방법은 다양함
- 가장 중요한 건 **오류를 잡아내는 방법**
- 오류를 형편 없이 분류한 사례
    
    ```java
    ACMEPort port = new ACMEPort(12);
    
    try {
    	port.open();
    } catch (DeviceResponseException e) {
    		reportPortError(e);
    		logger.log("Device response exception", e);
    } catch (ATM1212UnlockedException e) {
    		reportPortError(e);
    		logger.log("Unlocked Exception", e);
    } catch (GMXError e) {
    		reportPortError(e);
    		logger.log("Device response exception");
    } finally {
    	...
    }
    ```
    
    - 대다수 상황에서 우리가 오류를 처리하는 방식은 비교적 일정
        
        1) 오류 기록
        
        2) 프로그램을 계속 수행해도 좋은지 확인
        
- 위 경우는 예외에 대응하는 방식이 예외 유형과 무관하게 거의 동일 → 간결하게 고치기 쉬움.
    - 호출하는 라이브러리 API를 감싸면서 예외 유형 하나를 반환하면 됨.
        
        ```java
        LocalPort port = new LocalPort(12);
        try {
        	port.open();
        } catch (PortDeviceFailure e) {
        	reportError(e);
        	logger.log(e.getMessage(), e);
        } finally {
        	...
        }
        ```
        
        여기서 LocalPort 클래스는 ACMEPort 클래스가 던지는 클래스 예외를 잡아 변환하는 Wrapper 클래스
        
        ```java
        public class LocalPort {
        	private ACMEPort innerPort;
        
        	public LocalPort(int portNumber) {
        		innerPort = new ACMEPort(portNumber);
        	}
        
        	public void open() {
        		try {
        			innerPort.open();
        		} catch (DeviceResponseException e) {
        			throw new PortDeviceFailure(e);
        		} catch (ATM1212UnlockedException e) {
        	...
        ```
        
    - 이런 Wrapper 클래스는 매우 유용
    - 실제로 외부 API를 사용할 때는 감싸기 기법이 최선
        
        ⇒ 의존성이 크게 줄어듦.
        
        ⇒ 특정 업체가 API를 설계한 방식에 발목 잡히지 않음.
        

## 📌 정상 흐름을 정의하라

- 앞 절에서 충고한 지침을 충실히 따르면 비즈니스 논리와 오류 처리가 잘 분리된 코드가 나옴.
    - 하지만 그러다 보면 오류 감지가 프로그램 언저리로 밀려남.
    - 독자적인 예외 던지고, 코드 위에 처리기를 정의해 중단된 계산 처리
- 그러나 때로는 중단이 적합하지 않은 때도 있다.
- 예제 : 비용 청구 애플리케이션에서 총계를 게산하는 허술한 코드
    
    ```java
    try {
    	MealExpenses expenses = expeseReportDAO.getMeals(employee.getID());
    	m_total += expenses.getTotal();
    } catch(MealExpensedNotFound e) {
    	m_total += getMealPerDiem();
    }
    ```
    
    클래스나 객체가 예외적인 상황을 캡슐화 해서 처리한다
    
    ```java
    MealExpenses expenses = expenseReportDAO.getMeals(employee.getID());
    m_total += expenses.getTotal();
    
    ```
    
    ExpenseReportDAO를 고쳐 언제나 MealExpense 객체를 반환하게 한다. 청구한 식비가 없다면 일일 기본 식비를 반환하는 MealExpense 객체를 반환한다.
    
    ```java
    public class PerDiemMealExpenses implements MealExpenses {
    	public int getTotal() {
    		// 기본값으로 일일 기본 식비를 반환한다.
    	}
    }
    ```
    

## 📌 null을 반환하지 마라

**null을 반환하고 이를 `if(object != null)`으로 확인하는 방식은 나쁘다.**

- null 대신 예외를 던지거나 특수 사례 객체(ex. `Collections.emptyList()`)를 반환하라
- 사용하려는 외부 API가 null을 반환한다면 Wrapper 를 구현해 예외를 던지거나 특수 사례 객체를 반환하라

### 수정 전

```java
List<Employee> employees = getEmployees();
if (employees != null) {
	for(Employee e : employees) {
		totalPay += e.getPay();
	}
}
```

### 수정 후

`getEmployees`가 null 대신 빈 리스트를 반환한다.

```java
List<Employee> employees = getEmployees();
for(Employee e : employees) {
	totalPay += e.getPay();
}
```

자바의 `Collections.emptyList()`

```java
public List<Employee> getEmployees() {
	if ( ...직원이 없다면... ) {
		return Collections.emptyList();
	}
}
```

## 📌 null을 전달하지 마라

- 메서드 인수로 null을 전달하는 방식은 더 나쁨.
    - 예외를 던지거나 assert 문을 사용할 수는 있다.
    - 하지만 애초에 null을 전달하는 경우는 금지하는 것이 바람직하다.