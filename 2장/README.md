# 2장 : 의미있는 이름
## 📌 의도를 분명히 밝혀라

> 좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 더 많다.
>
> 
따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.


### 🤔 모호한 코드

```java
int d; // 경과 시간(단위 : 날짜)
```
경과 시간이나 날짜라는 느낌이 안든다. 측정하려는 값과 단위를 표현하는 이름 필요!

### 🙆 개선한 코드

```java
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```


### 🤔 모호한 코드

```java
public List<int[]> getThem() {
	List<int[]> list1 = new ArrayList<int[]>();
	for(int[] x : theList)
		if(x[0] == 4) 
			list1.add(x);
	return list1;
}
```

코드 맥락이 코드 자체에 명시적으로 드러나지 않는다. 위 코드는 암암리에 독자가 다음과 같은 정보를 안다고 가정한다.

1. `theList에` 무엇이 들었는가?
2. `theList`에서 `0`번째 값이 어째서 중요한가?
3. 값 `4`는 무슨 의미인가?
4. 함수가 반환하는 `list1`을 어떻게 사용하는가?

**정보 제공은 충분히 가능했었다.** 지뢰찾기 게임을 만든다고 가정하자. `theList` → `gameBoard`로 바꿔보자

### 🙆 개선한 코드

```java
public List<int[]> getFlaggedCells() {
	List<int[]> flaggedCells = new ArrayList<int[]>();
	for(int[] cell : gameBoard)
		if(cell[STATUS_VALUE) == FLAGGED)
			flaggedCells.add(cell);
	return flaggedCells;
```

### 🙆‍♂️ 더 개선한 코드

```java
public List<Cell> getFlaggedCells() {
	List<Cell> flaggedCells = new ArrayList<Cell>();
	for(Cell cell : gameBoard)
		if(cell.isFlagged())
			flaggedCells.add(cell);
	return flaggedCells;
```

- `cell`을 int 배열 대신 간단한 클래스로 만들었다.

- `isFlagged`라는 좀 더 명시적인 함수를 사용해 `FLAGGED`라는 상수를 감추었다.

<br>

## 📌 그릇된 정보를 피하라

### **나름대로 널리 쓰이는 의미가 있는 단어를 다른 의미로 사용하면 안된다.**

- 직각삼각형의 빗변(hypotenus)을 구현할 때 `hp` 라는 변수를 사용한 경우
    - hp는 유닉스 플랫폼이나 유닉스 변종을 가리킴 → 독자에게 **그릇된 정보** 제공
  
- 여러 계정을 그룹으로 묶을 때, 실제 `List`가 아니라면 `accountList`라고 명명하지 않는다.
    - 대체 → `accountGroup`, `bunchOfAccounts`, `Accounts`

### **서로** **흡사한 이름을 사용하지 않도록 주의한다.**

- 한 모듈에서 `XYZControllerForEfficientHandlingOfStrings` 라는 이름을 사용하고, 조금 떨어진 모듈에서 `XYZControllerForEfficientStorageOfStrings`라는 이름을 사용하는 경우
  
- 둘은 겁나게 비슷함.
- 유사한 개념은 유사한 표기법을 사용함.
    - 이것도 **정보**임. 일관성이 떨어지는 표기법은 **그릇된 정보**
- `O`와 `0`, `l`과 `1`을 한꺼전에 사용하는 경우 ← 끔찍한 경우!

<br>

## 📌 의미 있게 구분하라

### 이름이 달라진다면 의미도 달라져야 한다.

- 연속적인 숫자를 덧붙인 이름(a1, a2, … , aN)은 아무런 정보를 제공하지 못한다.
    
    ```cpp
    public static void copyChars(char a1[], char a2[]) {
    	for (int i = 0; i < a1.length; i++) {
    		a2[i] = a1[i];
    	}
    }
    ```
    
    - 함수 인수 이름으로 `source`와 `destination`을 사용한다면 코드 읽기가 훨씬 더 쉬워진다.
    

### 불용어를 추가한 이름 역시 아무런 정보도 제공하지 못한다.

> **불용어(Stop word) : 분석에 큰 의미가 없는 단어.**
예를 들어 the, a, an, is, I, my 등과 같이 문장을 구성하는 필수 요소지만 문맥적으로 큰 의미가 없는 단어를 말한다.
> 
- `Product`라는 클래스가 있을 때, 다른 클래스를 `ProductInfo` 혹은 `ProductData`라고 부른다면 개념을 구분하지 않은 채 이름만 달리한 경우이다.
    - `Info`나 `Data`는 a, an, the와 마찬가지로 의미가 불분명한 **불용어**이다.

- a와 the를 무조건 사용하지 말라는 의미 Xx
  
    - 의미가 분명히 다르다면 사용해도 무방하다.
  
    - `zork`라는 변수가 이미 있넹?? 그럼 `theZork`로 짓자!! <<< 이러지 말라는 거
- 변수 이름에 `variable`이라는 단어 금물 / 표 이름에 `table`이라는 단어 금물
- `Name`과 `NameString`
    - `Name`이 부동소수가 될 수 있는 것도 아닌데 굳이 String을?
- `Customer` 클래스와 `CustomerObject` 같이 만들지 말기
    - 고객 급여이력 찾을 때 어떤 클래스 뒤질건데?
  
- 이 프로젝트에 참여한 프로그래머는 어느 함수를 호출할지 어떻게 알까?
    
    ```java
    getActiveAccount();
    getActiveAccounts();
    getActiveAccountInfo();
    ```
    
- 그 외
    - 명확한 관례가 없다면 `moneyAmount`와 `money`는 구분이 안된다.
  
    - `customerInfo`와 `customer`
    - `accountData`와 `account`
    - `theMessage`와 `message`
  
<br>

## 📌 발음하기 쉬운 이름을 사용하라

### 발음하기 어려운 이름은 토론하기도 어렵다.

- 발음하기 쉬운 단어는 중요
    - 프로그래밍은 사회활동이기 때문
  
- 다음 두 예제를 비교해보자.
    
    ```java
    class DtaRcrd102 {
    	private Date genymdhms;
    	private Date modymdhms;
    	private final String pszqint = "102";
    	/* ... */
    };
    ```
    
    ```java
    class Customer {
    	private Date generationTimestamp;
    	private Date modificationTimestampl
    	private final String recordId = "102";
    };
    ```
    
    - 둘 째 코드는 지적인 대화가 가능해진다. 😎

<br>

## 📌 검색하기 쉬운 이름을 사용하라

### 문자 하나를 사용하는 이름과 상수는 쉽게 눈에 띄지 않는다

- `MAX_CLASSED_PER_STUDENT` 는 `grep`으로 찾기 쉽지만, 숫자 7은 은근히 까다롭다.

    - 7이 들어간 파일 이름이나 수식이 모두 검색되기 때문
  
- `e`라는 문자도 변수 이름으로 적합하지 못하다.
    - 영어에서 가장 많이 쓰이는 문자 → 검색이 어려움
- 이런 관점에서 **긴 이름이 짧은 이름보다 좋다.**
- **검색하기 쉬운 이름이 상수보다 좋다.**
- 필자는 간단한 메서드에서 로컬 변수만 한 문자를 사용함.
    - ⭐️ **이름 길이는 범위 크기에 비례해야 한다.**
    - **변수나 상수를 여러 곳에서 사용한다면 검색하기 쉬운 이름이 바람직하다.**
    - 다음 두 예제를 비교해보자.
        
        ```java
        for(int j=0; j<34; j++) {
        	s += (t[j]*4)/5;
        }
        ```
        
        ```java
        int realDaysPerIdealDay = 4;
        const int WORK_DAYS_PER_WEEK = 5;
        int sum = 0;
        
        for(int j=0; j < NUMBER_OF_TASKS; j++) {
        	int realTasksDays = taskEstimate[j] * realDaysPerIdealDay;
        	int realTasksWeeks = (realTaskDays / WORK_DAYS_PER_WEEK);
        	sum += realTasksWeeks;
        }
        ```
        
        (아마도 **일을 끝내는 데 몇 주가 걸리는지** 계산하는 코드인 것 같음. `realDaysPerIdealDays`는 이상적으로 1일이 소요된다고 추정되었을 때, **실제로**는 얼마나 소요될지 나타내는 변수임. 즉, 1일의 이상적인 추정치는 4일로 변환됨. `taskEstimate`는 그 일을 끝내는 데 **며칠**이 걸리는지 추정한 변수임. 계산하면 결국 `sum`에는 총 몇 주 걸리는지가 저장됨.)
        (나는 지금까지 변수를 적게 선언하면 할수록 좋은 줄 알았는데 꼭 그런 것만은 아니네…)
        
        - 위 코드에서 `sum`이 별로 유용하진 않으나 최소한 검색은 가능하다.
  
        - 이름을 의미있게 지으면 함수가 길어진다.
            - 하지만 `WORK_DAYS_PER_WEEK`를 찾기가 얼마나 쉽냐!!!
  
            - 그냥 5를 사용한다면 5가 들어가는 이름을 모두 찾은 후 의미를 분석해 원하는 상수를 가려내야 하리라.

<br>

## 📌 인코딩을 피하라

- 문제 해결에 집중하는 개발자에게 인코딩은 불필요한 정신적 부담

- 인코딩한 이름은 거의 발음하기 어려우며 오타가 생기기도 한다.

### 헝가리식 표기법

> **헝가리식 표기법 : 컴퓨터 프로그래밍에서 변수 및 함수의 이름 인자 앞에 데이터 타입을 명시하는 코딩 규칙**
> 
- 요즘 나오는 프로그래밍 언어는 많은 타입 지원
    - 컴파일러가 타입을 기억하고 강제
  
- 클래스와 함수는 점차 작아짐 → 변수를 선언한 위치와 사용하는 위치가 멀지 않음.
- 객체는 강한 타입(stronged-type)이며, IDE는 코드를 컴파일하지 않고도 타입 오류를 감지할 정도로 발전

⇒ **이제는 헝가리식 표기법이나 기타 인코딩 방식이 오히려 방해가 될 뿐이다.** 

- 변수, 함수, 클래스 이름이나 **타입을 바꾸기가 어려워지며, 읽기도 어려워짐!**
    
    ```java
    PhoneNumber phoneString;
    // 타입이 바뀌어도 이름은 바뀌지 않는다!
    ```
    

### 멤버 변수 접두어

- 이제는 멤버 변수에 `m_`이라는 접두어를 붙일 필요도 없다.
  
    - **클래스와 함수는 접두어가 필요없을 정도로 작아야 마땅하다.**

```java
public class Part {
	private String m_dsc; // 설명 문자열
	void setName(String name) {
		m_dsc = name;
	}
}
```

```java
public class Part {
	String description;
	void setDescription(String description) {
		this.description = description;
	}
}
```

### 인터페이스 클래스와 구현 클래스

**때로는 인코딩이 필요한 경우도 있다.**

- 도형을 생성하는 `ABSTRACT FACTORY`를 구현한다고 가정하자. (인터페이스 클래스)

- 인터페이스 클래스 이름과 구현 클래스 이름은 뭘로? → `IShapeFactory`와 `ShapeFactory`?
    - 필자는 개인적으로 인터페이스 이름은 접두어를 붙이지 않는 편이 좋다고 생각 (왜???
  
    - 옛날 코드에서 많이 사용하는 접두어 `I`는 (잘해봤자) 주의를 흐트리고 (나쁘게는) 과도한 정보를 제공
        - 내가 다루는 클래스가 인터페이스라는 사실을 남에게 알리고 싶지 않음 (그냥 ShapeFactory라고 생각해줬으면 하는 마음~)
    - 인터페이스 이름과 구현 클래스 이름 중 하나를 인코딩해야 한다면 **구현 클래스 이름**을 택하겠다
        - `ShapeFactoryImp`나 심지어 `CShapeFactory`가 `IShapeFactory`보다 좋다. (오호)

<br>

## 📌 자신의 기억력을 자랑하지 마라

**독자가 코드를 읽으면서 변수 이름을 자신이 아는 이름으로 변환해야 한다면 그 변수 이름은 바람직하지 못하다**

- 문자 하나만 사용하는 변수 이름은 문제가 있다.
    - 루프돌 때 쓰는 i, j, k는 갠춘,, (`l`은 절대 안됨!)
        - 단, 루프 범위가 아주 작고 다른 이름과 충돌하지 않을 때만 괜찮다.
  
    - 루프에서 반복 횟수 변수는 전통적으로 한 글자만 사용하기 때문
    - 그 외에는 대부분 적절하지 못하다.
  
- 프로그래머는 일반적으로 똑똑하지만 때때로 자신의 정신적 능력을 과시하고 싶어함.
    - ex) `r` 이라는 변수는 호스트와 프로토콜을 제외한 소문자 URL을 뜻하잖아요~ ㅋ 그것도 기억 못함?
        
        <img src="https://i.pinimg.com/564x/65/11/c3/6511c3f941ca3ab2c3e12f7914135418.jpg" width=200px>
        
- 똑똑한 프로그래머와 전문가 프로그래머 사이의 차이점
    - `명료함이 최고`라는 사실을 이해한다.

<br>

## 📌 클래스 이름

- 클래스 이름과 객체 이름은 **명사**나 **명사구**가 적합하다.

- 👍 `Customer`, `WikiPage`, `Account,` `AddressParser`
- 👿 `Manager`, `Processer`, `Data`, `Info` 등과 같은 단어는 피하고, 동사는 사용하지 않는다.

<br>

## 📌 메서드 이름

- 메서드 이름은 **동사**나 **동사구**가 적합하다.
- `postPayment`, `deletePage`, `save`
- 접근자(Accessor), 변경자(Mutator), 조건자(Predicate)는 표준에 따라 값 앞에 `get`, `set`, `is`를 붙인다.
    
    ```java
    String name = employee.getName();
    customer.setName("mike");
    if(paycheck.isPosted())...
    ```
    
- 생성자를 중복정의(overload)할 때는 정적 팩토리 메서드를 사용한다.
    - 메서드는 인수를 설명하는 이름을 사용한다.
    - 다음 두 예제를 살펴보자.
        
        ```java
        Complex fulcrumPoint = Complex.FromRealNumber(23.0);
        ```
        
        위 코드가 아래 코드보다 좋다.
        
        ```java
        Complex fulcrumPoint = new Complex(23.0);
        ```
        
        생성자 사용을 제한하려면 해당 생성자를 private로 선언한다.
        
        > **[부연 설명]**
        > 두 코드 모두 23.0 값을 사용하여 **`Complex`** 객체를 생성하지만, 첫 번째 코드는 정적 팩토리 메서드를 사용하고 두 번째 코드는 생성자를 사용한다는 점에서 차이가 있다. 정적 팩토리 메서드는 객체를 생성하는 데 사용되는 정적(static) 메서드이다. 이 메서드는 클래스의 인스턴스를 반환하며, 생성자와 달리 이름이 있고 호출할 때마다 새로운 객체를 반환할 필요는 없다.
        > 
        > - 정적 팩토리 메서드 예시 🍕
        >     
        >     ```java
        >     public class Pizza {
        >         private String type;
        >         
        >         private Pizza(String type) {
        >             this.type = type;
        >         }
        >         
        >         // 기본 피자 생성 정적 팩토리 메서드
        >         public static Pizza createBasicPizza() {
        >             return new Pizza("Basic");
        >         }
        >         
        >         // 매운 피자 생성 정적 팩토리 메서드
        >         public static Pizza createSpicyPizza() {
        >             return new Pizza("Spicy");
        >         }
        >         
        >         // 간단한 네이플 피자 생성 정적 팩토리 메서드
        >         public static Pizza createNapoliPizza() {
        >             return new Pizza("Napoli");
        >         }
        >     }
        >     ```
        >

<br>

## 📌 기발한 이름은 피해라

- 재미난 이름보다 명료한 이름을 선택하라

- 특정 문화에서만 사용하는 농담은 피하는 편이 좋다.

## 📌 한 개념에 한 단어를 사용하라

- **추상적인 개념 하나에 단어 하나를 선택해 이를 고수한다.**

- 예를 들어, 똑같은 메서드를 클래스마다 `fetch`, `retrieve`, `get`으로 제각각 부르면 혼란스럽다
- 마찬가지로 동일 코드 기반에 `controller`, `manager`, `driver`를 섞어 쓰면 혼란스럽다.
    - `DriverManager`와 `ProtocolController`는 근본적으로 어떻게 다른가?
- 이름이 다르면 독자는 당연히 클래스도 다르고 타입도 다르리라 생각한다.
- (일관성 있는 어휘를 사용하자)

<br>

## 📌 말장난을 하지 마라

- 한 단어를 두 가지 목적으로 사용하지 마라.
- 예를 들어 이런 상황
    - **여러 클래스**에 `add`라는 메서드가 생겼다.
  
    - 지금까지 구현한 `add` 메서드는 모두가 기존 값 두 개를 더하거나 이어서 **새로운 값**을 만든다고 가정하자.
    - 새로 작성하는 메서드는 **집합**에 값 하나를 추가한다.
        - 이 메서드를 `add`라고 불러도 괜찮을까?
        - 새 메서드는 기존 `add` 메서드와 **맥락이 다르다.**


            - 그러므로 `insert`나 `append`라는 이름이 적당하다


            - 새 메서드를 `add`라 부른다면 이는 **말장난**이다
    
    > 프로그래머는 코드를 최대한 이해하기 쉽게 짜야 한다. *(중략)* 의미를 해독할 책임이 독자에게 있는 논문 모델이 아니라 의도를 밝힐 책임이 저자에게 있는 잡지 모델이 바람직하다.
    > 
    
    (그러니까, 이름을 지을 때 앞서 말했듯이 한 개념에 한 단어만 사용하되 맥락이 다르다면 다른 단어를 사용하라는 이야기)
    
<br>

## 📌 해법 영역에서 가져온 이름을 사용하라

- 코드를 읽을 사람도 프로그래머라는 사실을 명심한다


    - 그러므로 전산 용어, 알고리즘 이름, 패턴 이름, 수학 용어 등을 **사용해도 괜찮다.**
- 모든 이름을 문제 영역(domain)에서 가져오는 정책은 현명하지 못하다.
- VISITOR 패턴에 친숙한 프로그래머는 `AccountVisitor` 이름을 금방 이해한다 / `JopQueue`를 모르는 프로그래머가 있을까? (전 몰ㄹ
- **기술 개념에는 기술 이름이 가장 적합한 선택이다.**

<br>

## 📌 문제 영역에서 가져온 이름을 사용하라

- **적절한 ‘프로그래머 용어’가 없다면 문제 영역에서 이름을 가져온다.**
  
- 우수한 프로그래머와 설계자라면 해법 영역과 문제 영역을 구분할 줄 알아야 한다.