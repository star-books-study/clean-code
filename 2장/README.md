# 2장 : 의미있는 이름
## 의도를 분명히 밝혀라

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
			flaggedCells.ad(cell);
	return flaggedCells;
```

- `cell`을 int 배열 대신 간단한 클래스로 만들었다.
- isFlagged라는 좀 더 명시적인 함수를 사용해 FLAGGED라는 상수를 감추었다.


