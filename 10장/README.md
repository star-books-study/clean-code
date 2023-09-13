# 10장 : 클래스
## 📌 클래스 체계

- 표준 자바 관례에 따르면, 클래스 안에서의 순서는 아래와 같다.
    1. static public 상수
    2. static private 변수
    3. private instance 변수(public 필요한 경우는 거의 없음.
    4. public method
        - private method는 자신을 호출하는 public method 직수에 위치

### 캡슐화
- 테스트를 위해 private method나 변수를 protected로 공개해야 할 경우가 있다. 이런 경우 공개하지 않을 방법을 충분히 생각한 이후에 캡슐화를 푸는 것이 좋다.

📌 클래스는 작아야 한다!
- 얼마나 작아야 하는가?
    - 함수는 물리적인 행 수로 크기 측정
    - 클래스는 다른 척도를 사용 : **클래스가 맡은 책임**

```java
// 목록 10-1 너무 많은 책임
public class SuperDashboard extends JFrame implements MetaDataUser {
	public String getCustomizerLanguagePath()
	// ... 70개가 넘는 공개 메서드들 ...
	// ... 많은 비공개 메서드가 이어진다 ...
}
```

- 만약에 SuperDashboard가 목록 10-2와 같이 메서드 몇 개만 포함된다면?