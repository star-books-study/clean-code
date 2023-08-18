## 들어가며

- 변수를 비공개로 설정하는 이유 : 남들이 변수에 의존하지 않게 하고 싶어서


- 그렇다면 왜 get set 함수를 public으로 외부에 노출하는 이유가 뭘까?

## 📌 자료 추상화

두 클래스 모두 2차원 점을 표현한다.

### 👎 구현을 외부로 노출하는 코드

```java
public class Point {
	public double x;
	public double y;
}
```

- 구현을 노출함.


- 변수를 private라 선언하더라도 get함수와 set함수를 제공한다면 구현을 외부로 노출하는 셈

### 👍 추상적인 Point 클래스

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

- 직교좌표계를 사용하는지 극좌표계를 사용하는지 알 수 없음.


- 그럼에도 인터페이스는 **자료구조를 명백하게 표현**
- 변수 사이에 함수라는 계층을 넣는다고 구현이 저절로 감춰지지는 않는다. 구현을 감추려면 `추상화`가 필요하다.
- 그저 조회함수와 설정함수로 변수를 다룬다고 클래스가 되지 않는다.
    - **추상 인터페이스를 제공해 사용자가 구현을 모른 채 자료의 핵심을 조작할 수 있어야 진정한 의미의 클래스**

---

### 👎 자동차 연료 상태를 구체적인 숫자 값으로 알려주는 예시

```java
public interface Vehicle {
	double getFuelTankCapacityInGallons();
	double getGallonsOfGasoline();
}
```

### 👍 자동차 연료 상태를 백분율(추상적인 개념)으로 알려주는 예시

```java
public interface Vehicle {
	double getPercentFuelRemaining();
}
```

- 이렇게 자료를 추상적인 개념으로 표현하는 편이 좋다.


- 인터페이스나 조회/설정 함수만으로 추상화가 이뤄지는 게 아님.
    - **개발자는 객체가 포함하는 자료를 표현할 수 있는 가장 좋은 방법을 심각하게 고민해야 함.**


    - **아무 생각 없이 조회/설정 함수를 추가하는 방법이 가장 나쁨.**

## 📌 자료 / 객체 비대칭

- 앞의 두 가지 예제는 객체와 자료 구조 사이에 벌어진 차이를 보여줌.


- 객체는 추상화 뒤로 자료를 숨긴 채 자료를 다루는 함수만 공개
- 자료구조는 자료를 그대로 공개하며 별다른 함수는 제공 Xx


- 두 개념(객체, 자료구조)는 사실상 정반대
- 예시 : 절차적인 도형 클래스
    - Geometry 클래스는 세 가지 도형 클래스를 다룬다.


    - 각 도형 클래스는 간단한 자료 구조 → 아무 메서드도 제공하지 않음.
    - 도형이 동작하는 방식은 Geometry 클래스에서 구현
    
		```java
		public class Square {
			public Point topLeft;
			public double side;
		}

		public class Rectangle {
			public Point topLeft;
			public double height;
			public double width;
		}

		public class Circle {
			public Point center;
			public double radius;
		}

		public class Geometry {
			public final double PI = 3.1415926253589793;
			
			public double area(Object shape) throws NoSuchShapeException 
			{
				if (shape instanceof Square) {
					Square s = (Square)shape;
					return s.side * s.side;
				}
				else if (shape instanceof Rectangle) {
					Rectangle r = (Rectangle)shape;
					return r.height * r.width;
				}
				else if (shape instanceof Circle) {
					Circle c = (Circle)shape;
					return PI = c.radius * c.radius;
				}
				throw new NoSuchShapeException();
			}
		}
		```

		- 객체지향 프로그래머가 위 코드를 읽고 코웃음을 칠 수도 있음.