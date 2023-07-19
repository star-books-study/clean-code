# 2장 : 의미있는 이름
## 📌 의도를 분명히 밝혀라

> 좋은 이름을 지으려면 시간이 걸리지만 좋은 이름으로 절약하는 시간이 훨씬 더 많다.
>
> 
따로 주석이 필요하다면 의도를 분명히 드러내지 못했다는 말이다.


### 🤔 모호한 코드
---

```java
int d; // 경과 시간(단위 : 날짜)
```
경과 시간이나 날짜라는 느낌이 안든다. 측정하려는 값과 단위를 표현하는 이름 필요!

### 🙆 개선한 코드
---

```java
int elapsedTimeInDays;
int daysSinceCreation;
int daysSinceModification;
int fileAgeInDays;
```

<br>

### 🤔 모호한 코드
---

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
---

```java
public List<int[]> getFlaggedCells() {
	List<int[]> flaggedCells = new ArrayList<int[]>();
	for(int[] cell : gameBoard)
		if(cell[STATUS_VALUE) == FLAGGED)
			flaggedCells.add(cell);
	return flaggedCells;
```

### 🙆‍♂️ 더 개선한 코드
---

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
    
    함수 인수 이름으로 `source`와 `destination`을 사용한다면 코드 읽기가 훨씬 더 쉬워진다.
    

### 불용어를 추가한 이름 역시 아무런 정보도 제공하지 못한다.

> **불용어(Stop word) : 분석에 큰 의미가 없는 단어.**
예를 들어 the, a, an, is, I, my 등과 같이 문장을 구성하는 필수 요소지만 문맥적으로 큰 의미가 없는 단어를 말한다.
> 
- `Product`라는 클래스가 있을 때, 다른 클래스를 `ProductInfo` 혹은 `ProductData`라고 부른다면 개념을 구분하지 않은 채 이름만 달리한 경우이다.
    - `Info`나 `Data`는 a, an, the와 마찬가지로 의미가 불분명한 **불용어**이다.
- a와 the를 무조건 사용하지 말라는 의미 Xx
    - 의미가 분명히 다르다면 사용해도 무방하다.
    - 요지는 zork라는 변수가 이미 있넹?? 그럼 theZork로 짓자!! <<< 이러지 말라는 거
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

## 📌 인코딩을 피하라

- 문제 해결에 집중하는 개발자에게 인코딩은 불필요한 정신적 부담
- 인코딩한 이름은 거의 발음하기 어려우며 오타가 생기기도 한다.

### 헝가리식 표기법

```java

```