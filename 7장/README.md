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
- 어떤 면에서 try 블록은 트랜잭션과 비슷
    - try 블록에서 무슨 일이 생기든 catch 블록은 프로그램 상태를 일관성 있게 유지해야 함.