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
    - 명확한 관례가 없다면 `moneyAmount`와 `mone`y는 구분이 안된다.
    - `customerInfo`와 `customer`
    - `accountData`와 `account`
    - `theMessage`와 `message`