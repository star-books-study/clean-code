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
    
    ```java
    // 목록 10-2 충분히 작을까?
    public class SuperDashboard extends Jframe implements MetaDataUser {
    	public Component getLastForcusedComponent()
    	public void setLastFocused(Component lastFocused)
    	public int getMajorVersionNumber()
    	public int getMinorVersionNumber()
    	public int getBuildNumber()
    }
    ```
    
    - 메서드가 다섯 개 정도면 괜찮아보이지만 아님.
    - 메서드 수가 작음에도 **책임이 너무 많다.**
- **클래스 이름은 해당 클래스 책임을 기술해야 함**
    - 작명은 클래스 크기를 줄이는 첫 번째 관문
- 클래스 이름이 모호하다면 클래스 책임이 너무 많아서임.
    - Processor, Manager, Super 등과 같이 모호한 단어가 있다?
        - 클래스에다 여러 책임을 떠안겼다는 증거
- 클래스 설명은 if, or, but 등을 제외하고 25단어 내외로 가능해야 함.


## 📌 단일 책임 원칙

- 단일 책임 원칙은 클래스나 모듈을 변경할 이유가 하나, 단 하나뿐이어야 한다는 원칙
- SRP는 ‘책임’이라는 개념을 정의하며 적절한 클래스 크기를 제시
- 책임, 즉 변경할 이유를 파악하려 애쓰다 보면 코드를 추상화하기도 쉬워짐.
- 10-2에서 버전 정보를 다루는 메서드 세 개를 따로 빼내 Version이라는 독자적인 클래스 생성

```java
// 목록 10-3 단일 책임 클래스
public class Version {
	public int getMajorVersionNumber()
	public int getMinorVersionNumber()
	public int getBuildNumber()
}
```

- 개발자들은 단일 책임 클래스가 많아지면 클래스 왔다갔다 해야 해서 큰 그림을 이해하기 어려워진다 우려
    - BUT 어느 시스템이든 익힐 내용은 그 양이 비슷
    
    > 그러므로 고민할 질문은 다음과 같다. “도구 상자를 어떻게 관리하고 싶은가? 작은 서랍을 많이 두고 기능과 이름이 명확한 컴포넌트를 나눠넣고 싶은가? 아니면 큰 서랍 몇 개를 두고 모두를 던져 넣고 싶은가?”
    > 
- 큰 클래스 몇 개가 아니라 작은 클래스 여럿으로 이뤄진 시스템이 더 바람직

### 응집도(Cohesion)

- 클래스는 인스턴스 변수 수가 작아야 한다.
- 각 클래스 메서드는 클래스 인스턴스 변수를 하나 이상 사용해야 함.
- 메서드가 변수를 많이 사용할수록 응집도가 높다
- 모든 인스턴스 변수를 메서드마다 사용하는 클래스는 응집도가 높다

```java
public class Stack {
	private int topOfStack = 0;
	List<Integer> elements = new LinkedList<Integer>();

	public int size() {
		return topOfStack();
	
	public void push(int element) {
		topOfStack++;
		elements.add(element);
	}
	
	public int pop() throws PoppedwhenEmpty {
		if(topOfStack == 0) 
			throw new PoppedWhenEmpty();
		int element = elements.get(--topOfStack);
		elements.remove(topOfStack);
		return element;
	}
}
```

### 응집도를 유지하면 작은 클래스 여럿이 나온다

- 큰 함수를 작은 함수 여럿으로 나누기만 해도 클래스 수가 많아진다.
- 왜냐!
    - 큰 함수를 쪼개면 빼내려는 코드가 그 함수에 사용된 변수를 사용함
        
        → 클래스의 인스턴스 변수로 승격
        
        → 몇몇 함수가 사용하는 인스턴스 변수가 많아짐
        
        → 어? 그럼 독자적인 클래스로 분리하면 되잖아!
        
- Literate Programming 에 나온 유서 깊은 예제
    
    ```java
    // 10-5 PrintPrimes.java
    package literatePrimes;
    
    public class PrintPrimes {
        public static void main(String[] args) {
            final int M = 1000;
            final int RR = 50;
            final int CC = 4;
            final int WW = 10;
            final int ORDMAX = 30;
            int P[] = new int[M + 1];
            int PAGENUMBER;
            int PAGEOFFSET;
            int ROWOFFSET;
            int C;
            int J;
            int K;
            boolean JPRIME;
            int ORD;
            int SQUARE;
            int N;
            int MULT[] = new int[ORDMAX + 1];
            J = 1;
            K = 1;
            P[1] = 2;
            ORD = 2;
            SQUARE = 9;
    
            while (K < M) {
                do {
                    J = J + 2;
                    if (J == SQUARE) {
                        ORD = ORD + 1;
                        SQUARE = P[ORD] * P[ORD];
                        MULT[ORD - 1] = J;
                    }
                    N = 2;
                    JPRIME = true;
                    while (N < ORD && JPRIME) {
                        while (MULT[N] < J)
                            MULT[N] = MULT[N] + P[N] + P[N];
                        if (MULT[N] == J)
                            JPRIME = false;
                        N = N + 1;
                    }
                } while (!JPRIME);
                K = K + 1;
                P[K] = J;
            } {
                PAGENUMBER = 1;
                PAGEOFFSET = 1;
                while (PAGEOFFSET <= M) {
                    System.out.println("The First " + M +
                        " Prime Numbers --- Page " + PAGENUMBER);
                    System.out.println("");
                    for (ROWOFFSET = PAGEOFFSET; ROWOFFSET < PAGEOFFSET + RR; ROWOFFSET++) {
                        for (C = 0; C < CC; C++)
                            if (ROWOFFSET + C * RR <= M)
                                System.out.format("%10d", P[ROWOFFSET + C * RR]);
                        System.out.println("");
                    }
                    System.out.println("\f");
                    PAGENUMBER = PAGENUMBER + 1;
                    PAGEOFFSET = PAGEOFFSET + RR * CC;
                }
            }
        }
    }
    ```
    
    - 엉망임. 최소한 여러 함수로 나눠야 마땅
    
    ```java
    // 10-6 PrimePrinter.java(리팩터링한 버전)
    package literatePrimes;
    public class PrimePrinter {
        public static void main(String[] args) {
            final int NUMBER_OF_PRIMES = 1000;
            int[] primes = PrimeGenerator.generate(NUMBER_OF_PRIMES);
            final int ROWS_PER_PAGE = 50;
            final int COLUMNS_PER_PAGE = 4;
            RowColumnPagePrinter tablePrinter =
                new RowColumnPagePrinter(ROWS_PER_PAGE,
                    COLUMNS_PER_PAGE,
                    "The First " + NUMBER_OF_PRIMES +
                    " Prime Numbers");
            tablePrinter.print(primes);
        }
    }
    ```
    
    ```java
    // 10-7 RowColomnPagePrinter.java
    package literatePrimes;
    import java.io.PrintStream;
    public class RowColumnPagePrinter {
        private int rowsPerPage;
        private int columnsPerPage;
        private int numbersPerPage;
        private String pageHeader;
        private PrintStream printStream;
        public RowColumnPagePrinter(int rowsPerPage,
            int columnsPerPage,
            String pageHeader) {
            this.rowsPerPage = rowsPerPage;
            this.columnsPerPage = columnsPerPage;
            this.pageHeader = pageHeader;
            numbersPerPage = rowsPerPage * columnsPerPage;
            printStream = System.out;
        }
        public void print(int data[]) {
            int pageNumber = 1;
            for (int firstIndexOnPage = 0; firstIndexOnPage < data.length; firstIndexOnPage += numbersPerPage) {
                int lastIndexOnPage =
                    Math.min(firstIndexOnPage + numbersPerPage - 1,
                        data.length - 1);
                printPageHeader(pageHeader, pageNumber);
                printPage(firstIndexOnPage, lastIndexOnPage, data);
                printStream.println("\f");
                pageNumber++;
            }
        }
        private void printPage(int firstIndexOnPage,
            int lastIndexOnPage,
            int[] data) {
            int firstIndexOfLastRowOnPage =
                firstIndexOnPage + rowsPerPage - 1;
            for (int firstIndexInRow = firstIndexOnPage; firstIndexInRow <= firstIndexOfLastRowOnPage; firstIndexInRow++) {
                printRow(firstIndexInRow, lastIndexOnPage, data);
                printStream.println("");
            }
        }
        private void printRow(int firstIndexInRow,
            int lastIndexOnPage,
            int[] data) {
            for (int column = 0; column < columnsPerPage; column++) {
                int index = firstIndexInRow + column * rowsPerPage;
                if (index <= lastIndexOnPage)
                    printStream.format("%10d", data[index]);
            }
        }
        private void printPageHeader(String pageHeader,
            int pageNumber) {
            printStream.println(pageHeader + " --- Page " + pageNumber);
            printStream.println("");
        }
        public void setOutput(PrintStream printStream) {
            this.printStream = printStream;
        }
    }
    ```