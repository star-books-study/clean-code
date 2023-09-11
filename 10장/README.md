# 10장 : 깨끗한 코드
## 📌 클래스 체계

- 표준 자바 관례에 따르면, 클래스 안에서의 순서는 아래와 같다.
    1. static public 상수
    2. static private 변수
    3. private instance 변수(public 필요한 경우는 거의 없음.
    4. public method
        - private method는 자신을 호출하는 public method 직수에 위치
- 테스트를 위해 private method나 변수를 protected로 공개해야 할 경우가 있다. 이런 경우 공개하지 않을 방법을 충분히 생각한 이후에 캡슐화를 푸는 것이 좋다.