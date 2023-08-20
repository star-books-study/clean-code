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

		- 객체지향 프로그래머가 위 코드를 읽고 코웃음을 칠 수도 있음. BUT 그렇게 비웃을만하진 않음.


      - 위 코드의 장점 : Geometry 클래스에 둘레 길이를 구하는 함수를 추가하고 싶을 때 도형 클래스는 영향을 받지 않음.
      - 위 코드의 단점 : 반대로 새 도형을 추가하고 싶으면 Geometry 클래스에 속한 함수를 모두 고쳐야 함.
- 예시2 : 객체 지향적인 도형 클래스
    
    ```java
    public class Square implements Shape {
    	private Point topLeft;
    	private double side;
    	
    	public double area() {
    		return side*side;
    	}
    }
    
    public class Rectangle implements Shape {
    	private Point topLeft;
    	private double height;
    	private double width;
    	
    	public double area() {
    		return height * width;
    	}
    }
    
    public class Circle implements Shape {
    	private Point center;
    	private double radius;
    	public final double PI = 3.141592653589793;
    	
    	public double area() {
    		return PI * radius * radius;
    	}
    }
    ```
    
    - area()는 다형 메서드


    - 위 코드의 장점 : Geometry 클래스는 필요 X → 새 도형을 추가해도 기존 함수에 아무런 영향을 미치지 않음.
    - 위 코드의 단점 : 새 함수를 추가하고 싶다면 도형 클래스를 전부 고쳐야 함.
- 위 두 예제는 상호보완적. 객체와 자료 구조는 근본적으로 야분됨.

> (자료구조를 사용하는) 절차적인 코드는 기존 자료 구조를 변경하지 않으면서 새 함수를 추가하기 쉽다. 반면, 객체 지향 코드는 기존 함수를 변경하지 않으면서 새 클래스를 추가하기 쉽다.
> 

> 절차적인 코드는 새로운 자료구조를 추가하기 어렵다. 그러려면 모든 함수를 고쳐야 한다. 객체 지향 코드는 새로운 함수를 추가하기 어렵다. 그러려면 모든 클래스를 고쳐야 한다.
> 

**⇒ 새로운 자료 타입이 필요한 경우 → 객체지향 기법**

**⇒ 새로운 함수가 필요한 경우 → 절차지향 기법**

- 모든 것이 객체라는 건 **미신.** 때로는 단순한 자료구조와 절차적인 코드가 가장 적합한 상황도 있다.

## 📌 디미터 법칙

> 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다는 법칙
> 
- 객체는 자료를 숨기고 함수를 공개함.
    
    ⇒ 객체는 조회 함수로 내부 구조를 공개하면 안됨.
    
- 디미터 법칙의 정확한 표현
    
    “클래스 C의 메서드 f는 다음과 같은 객체의 메서드만 호출해야 한다.”
    
    - 클래스 C
    - f가 생성한 객체
    - f 인수로 넘어온 객체

    - C 인스턴스 변수에 저장된 객체


- 하지만 위 객체에서 허용된 메서드가 반환하는 객체의 메서드는 호출하면 안 됨.
    
    > 낯선 사람은 경계하고 친구랑만 놀아라.
    > 
- 디미터 법칙을 어기는 예시
    
    ```java
    final String outputDir = ctxt.getOptions().getScratchDir().getAbsolutePath();
    ```
    

### 기차충돌🚊💥

위와 같은 코드를 기차충돌(train wreck) 코드라고 부름. 여러 객체가 한 줄로 이어진 기차처럼 보이기 때문. 

- 코드 개선
    
    ```java
    Option opts = ctxt.getOptions();
    File scratchDir = opts.getScratchDir();
    final String outputDir = scratchDir.getAbsolutePath();
    ```
    
- 위 예제는 디미터 법칙을 위반하는가?
    - `ctxt`, `Options`, `ScrahtchDir`이 객체인지 자료구조인지에 따라 다름.


    - 🤔 객체라면? 내부 구조를 숨겨야 하므로 위반
    - 🤔 자료구조라면? 당연히 내부 구조를 노출하므로 디미터 법칙이 적용되지 않음.
- 그러나 위 예제는 조회함수를 사용하는 바람에 혼란을 일으킴.
- 코드 더 개선
    
    ```java
    final String outputDir = ctxt.options.scratchDir.absolutePath;
    ```
    
- 자료구조는 무조건 함수 없이 공개 변수만 포함하고
- 객체는 비공개 변수와 공개 함수를 포함한다면 문제는 훨씬 간단


### 잡종구조 💩

이런 혼란 때문에 때때로 절반은 객체, 절반은 자료 구조인 잡종 구조가 나옴.

- 다른 함수가 절차적인 프로그래밍의 자료 구조 접근 방식처럼 비공개 변수를 사용하고픈 유혹에 빠지기 십상(기능욕심)
- 잡종 구조는 새로운 함수는 물론이고 새로운 자료구조도 추가하기 어려움.
- **단점만 모아놓은 구조이므로 피하는 게 좋음.**

### 구조체 감추기 🙈

만약 ctxt, options, scratchDir이 진짜 객체라면?

- 내부구조를 감춰야 하므로 압선 코드 예제처럼 쓸 수 없음.
- 다음 두코드를 보자.
    
    ```java
    ctxt.getAbsolutePathOrScratchFirectoryOption();
    ```
    
    ```java
    ctxt.getScratchDirectoryOption().getAbsolutePath();
    ```
    
    - 첫 번째 코드
        
        `ctxt` 객체에 공개해야 하는 메서드가 너무 많아짐.
        
    - 두 번째 코드
    `getScratchDirectoryOption` 이 자료구조를 반환한다고 가정
    - ctxt가 객체라면 뭔가를 하라고 말해야지 속을 드러내라고 말하면 안 된다.
- 위 예제 모두 임시 디렉터리의 절대경로를 뽑는 코드인데, 절대 경로를 얻으려는 이유가 뭔지 내려가서 뜯어보니, 임시 파일을 생성하기 위한 목적임.
    - 그렇다면 `ctxt` 객체에 임시 파일을 생성하라고 시키면 어떨까?
- 코드 완전 개선
    
    ```java
    BufferedOutputStream bos = ctxt.createScratchFileStream(classFileName);
    ```
    
    - 이제 객체가 맡기기에 적당한 임무로 보임.
    - `ctxt`는 내부 구조를 드러내지 않으며, 모듈에서 해당 함수는 자신이 몰라야 하는 여러 객체를 탐색할 필요가 없음. → 디미터 법칙 위반 X

## 📌 자료 전달 객체

- 자료 구조체의 전형적인 형태 : 공개 변수만 있고 함수가 없는 클래스
    
    ⇒ 자료 전달 객체(Data Transfer Object, DTO)라고 함.
    
- DTO는 유용한 구조체
    - 데이터베이스와 통신하거나 소켓에서 받은 메시지의 구분 분석에 유용


    - 데이터베이스에 저장된 가공되지 않은 정보를 애플리케이션 코드에서 사용할 객체로 반환하는 일련의 단계에서 가장 처음으로 사용하는 구조체
- 좀 더 일반적인 형태는 빈(bean) 구조
    - 빈은 private 변수를 조회 / 설정 함수로 조작한다.


    - 일종의 사이비 캡슐화.
    
    ```java
    public class Address {
        private String street;
        private String streetExtra;
        ...
        
        public Address(String street, String streetExtra, ...) {
        	this.street = street;
            this.streetExtra = streetExtra;
            ...
        }
        
        public String getStreet() {
        	return street;
        }
        
        public String getStreetExtra() {
        	return streetExtra;
        }
        
        ...
    }
    ```
    

### 활성 레코드

DTO의 특수한 형태

- 공개 변수가 있거나 비공개 변수에 조회/설정 함수가 있는 자료구조


- 대개 save나 find 같은 탐색함수도 제공
- 활성 레코드는 DB 테이블이나 다른 소스에서 자료를 직접 반환
- 활성 레코드를 사용하는 잘못된 방식
    - 비즈니스 규칙을 추가해 이러한 자료구조를 객체로 취급하는 건 바람직하지 않음.
        - 자료구조도 객체도 아닌 잡종이 나오기 때문
- 해결책
    - 활성 레코드는 자료구조로 취급


    - 비즈니스 규칙을 담으면서 내부 자료를 숨기는 객체는 **따로 생성 (여기서 내부 자료는 활성 레코드의 인스턴스일 가능성이 높음.)**

## 📌 결론

### 객체는 동작을 공개하고 자료를 숨긴다.

- 새 객체 타입을 추가하기는 쉬움.
- 새 동작을 추가하기는 어려움.

### 자료구조는 별다른 동작 없이 자료를 노출한다.

- 새 동작을 추가하기는 쉬움.
- 새 자료구조를 추가하기는 어려움.

### 객체 vs 자료구조, 언제 사용?

- 새로운 자료 타입을 추가하는 유연성이 필요하면 객체가 적합
- 새로운 동작을 추가하는 유연성이 필요하면 자료구조와 절차적인 코드가 적합

<br>

# 추가적인 질문이나 의문점

## 🤨 Entity, DTO, VO의 차이점

> 참고
> 
> 
> [Entity, DTO, VO 바로 알기](https://velog.io/@gillog/Entity-DTO-VO-바로-알기)
> 

### 1. Entity

> Entity Class는 실제 DataBase의 테이블과 1 : 1로 Mapping 되는 Class로,
> 
> 
> **DB의 테이블내에 존재하는 컬럼만을 속성(필드)으로** 가져야 한다.
> 
> `Entity Class`는 상속을 받거나 구현체여서는 안되며, 테이블내에 존재하지 않는 컬럼을 가져서도 안된다.
> 
- 최대한 외부에서 Entity Class의 Data Field에 함부로 접근하지 못하도록 제한하여,
- 해당 Class 안에서 접근을 허용할 데이터들을 제한하며 logic method을 구현 해야하고,
- `Domain Logic`만 가지며 Presentation Logic을 가지고 있어서는 안된다.
- *Spring 3 Tier인 `Persistence`, `Buseniss`, `Presentation` Tier 중 `Persistence Tier`에서 사용*
- 구현 method는 주로 Service Layer에서 사용한다.
- *Entity를 `Persistence` Tier에서 받아와 DTO로 변환하여 `Presentation` Tier에 전달하는 것이 `Business Tier`, `Service` 단에서의 역할*

### 1.1 **`Entity`와 `DTO`를 분리해서 관리해야 하는 이유**

> **DB Layer와 View Layer 사이의 역할을 분리 하기 위해서**다.
> 
- *`DB Layer` = `Persistence Tier`, `View Layer` = `Presentation Tier`*
- `Entity`는 실제 테이블과 매핑되어 만일 변경되게 되면 여러 다른 Class에 영향을 끼치고,
- `DTO`는 View와 통신하며 자주 변경되므로 분리 해주어야 한다.
- 결국 DTO는 Domain Model 객체(`Entity`)를 그대로 두고 복사하여,
- 다양한 Presentation Logic을 추가한 정도로 사용하며 Domain Model 객체(`Entity`)는 `Persistent`만을 위해서 사용해야한다.

<br>

### **1.2 Entity `Setter` 금지 및 생성자, 접근 제어**

**Entity를 작성할 때 `Setter`를 무분별하게 사용**하면 **객체(Entity)의 값을 쉽게 변경**할 수 있으므로,

**객체의 일관성을 보장할 수 없다.**

- `Setter`사용 줄이는 법
    - 객체의 생성자에 값들을 넣어줌으로써 Setter 사용을 줄일 수 있다.
    
    ```java
    public Member(String userName, String passWord, String name) {
            this.username = username;
            this.password = password;
            this.name = name;
    }
    ```
    
- `Buiilder Pattern`**으로 객체 생성 제한**
    - 여기에 더 나아가 **`Builder Pattern`을 사용**하면
    - 해당 **`Entity` 객체를 생성 할때 그 목적과 함께 필요 용도에 따라**
    - **객체 생성을 제한**할 수 있어 **명시적이고 안전한 Core Data 접근이 가능**하다.

<br>

### 2. VO

> `VO`(Value Object)는 말 그대로 **값 객체라는 의미**를 가지고 있다.
> 
> 
> `VO`**의 핵심 역할은 equals()와 hashcode()를 오버라이딩 하는 것**이다.
> 
> 내부에 선언된 **속성(Field)의 모든 값들이** `VO` **객체마다 값이 같아야, 똑같은 객체라고 판별**
> 
> 한다.
> 
- `Getter`와 `Sette`r를 가질 수 있으며,
- 테이블 내에 있는 속성 외에 추가적인 속성을 가질 수 있으며,
- 여러 테이블(A, B, C)에 대한 공통 속성을 모아서 만든 `BaseVO` 클래스를 상속받아서 사용할 수도 있다.

<br>

### 2.1 VO 예제

```java
@Getter @Setter
@Alias("article")
class ArticleVO {
    private Long id;
    private String title;
    private String contents;

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Article article = (Article) o;
        return Objects.equals(id, article.id);
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

<br>

### 3. DTO

> `DTO`(Data Transfer Object)는 데이터 전송(이동) 객체라는 의미를 가지고 있다. DTO는 주로 비동기 처리를 할 때 사용한다.
> 
- `DTO`는 **계층간 데이터 교환을 위한 객체*이다.
- DB의 데이터를 Service나 Controller 등으로 보낼 때 사용하는 객체를 말한다.
- 즉, DB의 데이터가 `Presentation Logic Tier`로 넘어올때는 DTO로 변환되어 오고가는 것이다.
- 로직을 갖고 있지 않는 순수한 데이터 객체이며, `getter/setter` 메서드만을 갖는다.
- 또한 `Controller Layer(ViewLayer)`에서 Response DTO 형태로 Client에 전달한다.

<br>

### 3.1 DTO 예제

```java
@Getter 
@Setter
class ArticleDTO {
  private String title;
  private String content;
  private String writer;
}
```

<br>

### 4. 최종 정리

|  | Entity | VO | DTO |
| --- | --- | --- | --- |
| 생성 목적 | JPA 사용시 DB Table과 직접적으로 매핑하여DB에 접근할때 사용 | 객체안의 값을 통해서도 비교해야하는중요 로직에서 사용할 Data를 담기위해 사용 | 단순 Data 송, 수신 용도 |
| getter 사용 | O | O | O |
| setter 사용 | X | O | O |
| equals & hashCode 구현 | O | O | X |
| 데이터 민감도 | 100 | 80 | 0 |
| Spring Tier 사용 범위 | Persistence(DAO, Repository) <-> Business(Service) | Business(Service) <-> Presentation(Controller) | Business(Service) <-> Presentation(Controller) |