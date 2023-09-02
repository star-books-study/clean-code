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

### 오류를 형편 없이 분류한 사례