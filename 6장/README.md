## 들어가며

- 변수를 비공개로 설정하는 이유 : 남들이 변수에 의존하지 않게 하고 싶어서
- 그렇다면 왜 get set 함수를 public으로 외부에 노출하는 이유가 뭘까?

## 📌 자료 추상화

두 클래스 모두 2차원 점을 표현한다.

- 구현을 외부로 노출하는 코드
    
    ```java
    public class Point {
    	public double x;
    	public double y;
    }
    ```
    
- 추상적인 Point 클래스
    
    ```java
    public interface Point {
    	double getX();
    	double getY();
    	void setCartesian(double x, double y);
    	double getR();
    	double getTheta();
    	void setPolar(double r, double theta);
    }
    ```