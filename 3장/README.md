# 3장 : 함수

> 어떤 프로그램이든 가장 기본적인 단위가 함수다.
> 
- 다음 코드를 보자.
    
    ```java
    /*
	예시1
    */
    public static String testableHtml {
        PageData pageData,
        boolean includeSuiteSetup
    } throws Exception {
        Wikipage wikiPage = pageData.getWikiPage();
        StringBuffer buffer = new StringBuffer();
        if (pageData.hasAttribute("Test")) {
            if (includeSuiteSetup) {
                WikiPage suiteSetup = 
                    PageCrawlerImpl.getInheritedPage(
                            SuiteResponder.SUITE_SETUP_NAME, wikiPage
                    );
                if (suiteSetup != null) {
                    WikiPagePath pagePath =
                        suiteSetup.getPageCrawler().getFullPath(suiteSetup);
                    String pagePathName = PathParser.render(pagePath);
                    buffer.append("!include -setup .")
                                .append(pagePathName)
                                .append("\\n");
                }
            }
            WikiPage setup =
                PageCrawlerImpl.getInheritedPage("SetUp", wikiPage);
            if (setup != null) {
                WikiPagePath setupPath =
                    wikiPage.getPageCrawler().getFullPath(setup);
                String setupPathName = PathParser.render(setupPath);
                buffer.append("!include -setup .")
                            .append(setupPathName)
                            .append("\\n");
            }
        }
        buffer.append(pageData.getContent());
        if (pageData.hasAttribute("Test")) {
                WikiPage teardown = 
                    PageCrawlerImpl.getInheritedPage("TearDown", wikiPage);
                if (teardown != null) {
                    WikiPagePath teardownPath =
                        suiteSetup.getPageCrawler().getFullPath(teardown);
                    String teardownPathName = PathParser.render(teardownPath);
                    buffer.append("!include -teardown .")
                                .append(pagePathName)
                                .append("\\n");
                }
                if (includeSuiteSetup) {
                    WikiPage suiteTeardown = 
                    PageCrawlerImpl.getInheritedPage(
                            SuiteResponder.SUITE_TEARDOWN_NAME,
                            wikiPage
                    );
                if (suiteTeardown != null) {
                    WikiPagePath pagePath =
                        suiteSetup.getPageCrawler().getFullPath(suiteTeardown);
                    String pagePathName = PathParser.render(pagePath);
                    buffer.append("!include -teardown .")
                                .append(pagePathName)
                                .append("\\n");
                }
            }
        }
        pageData.setContent(buffer.toString());
        return pageData.getHtml();
    }
    ```

    - 추상화 수준도 너무 다양하고, 코드도 너무 길다.
  
    - 이상한 문자열, 이상한 함수!
- 리팩터링 버전 → 메서드 몇 개를 추출하고 이름 몇 개를 변경하고, 구조를 조금 변경함.
    
    ```java
    /*
	예시2
    */
    public static String renderPageWithSetupsAndTeardowns(
    	PageData pageData, boolean isSuite
    ) throws Exception {
    	boolean isTestPage = pageData.hasAttribute("Test");
    	if(isTestPage) {
    		Wikipage testPage = pageData.getWikiPage();
    		StringBuffer newPageContent = new StringBuffer();
    		includeSetupPages(testPage, newPageContent, isSuite);
    		newPageCintent.append(pageData.getContent());
    		includeTeardownPages(testPage, newPageContent, isSuite);
    		pageData.setContent(newPageContent.toString());
    	}
    	return pageData.getHtml();
    }
    ```
    
    - FitNesse에 익숙하지 않다면 완전히 이해하기는 어려울 것

    - 그러나 함수가 설정(setup) 페이지와 해제(teardown) 페이지를 테스트 페이지에 넣은 후 해당 테스트 페이지를 HTML로 렌더링한다는 사실은 짐작 가능
    - 이렇게 읽기 쉽고 이해하기 쉬운 함수는 어떻게 구현할 수 있을까?! 레츠고~!

<br>

## 📌 작게 만들어라!

> 함수를 만드는 첫째 규칙은 ‘작게!’다. 함수를 만드는 둘째 규칙은 ‘더 작게!’다.
> 

### 얼마나 짧아야 좋을까?

- 지인 집에서 Sparkle이라는 작은 자바/스윙 프로그램의 코드를 본 저자
    - 마우스를 움직이면 커서를 따라가며 반짝이를 뿌려주는 프로그램 🪄 ✨
- 함수가 생각보다 너무나 작아서 깜짝 놀랐다고 함.
- 모든 함수가 2줄, 3줄, 4줄 정도였음.
    
    > 각 함수가 이야기 하나를 표현했다. 각 함수가 너무도 멋지게 다음 무대를 준비했다. 바로 이것이 답이다!
    > 

```java
/*
예시3
*/
public static String renderPageWithSetupsAndTeardowns(
    PageData, pageData, boolean isSuite) throws Exception {
    if(isTestPage(pageData))
        includeSetupAndTearDownPages(pageData, isSuite);
    return pageData.getHtml();
}
```

### 블록과 들여쓰기

- if 문 / else 문 / while 문 등에 들어가는 **블록**은 한 줄이어야 한다.
    - 대개 거기서 함수 호출
  
    - 그럼 바깥을 감싸는 함수가 작아짐.
    - (블록 안에서 호출하는 함수 이름을 적절히 짓는다면) 코드를 이해하기도 쉬워짐.
- 중첩 구조가 생길만큼 함수가 커져서는 안됨.

    - 함수에서 **들여쓰기** 수준은 1단이나 2단을 넘어서면 안됨.

(엄청 명쾌하게 말해주네… 이렇게 짧아야 하는 줄 몰랐다)

<br>

## 📌 한 가지만 해라!

> 함수는 한 가지를 해야 한다. 그 한 가지를 잘해야 한다. 그 한 가지만을 해야 한다.
> 

### ‘한 가지’란 무엇인가?

- `예시3`을 다시 보자. 이 함수는 한 가지가 아니라 세 가지를 한다고 주장할 수도 있다.
    
    ```java
    public static String renderPageWithSetupsAndTeardowns(
    	PageData, pageData, boolean isSuite) throws Exception {
    	if(isTestPage(pageData))
    		includeSetupAndTearDownPages(pageData, isSuite);
    	return pageData.getHtml();
    }
    ```
    
    1. 페이지가 테스트 페이지인지 확인한다.
    2. 그렇다면 설정 페이지와 해제 페이지를 넣는다.
    3. 페이지를 HTML로 렌더링한다.
- 위에서 언급하는 세 단계는 지정된 함수 이름 아래에서 **추상화 수준이 하나다.**
    - 추상화 수준이 하나인 단계만 수행한다면 그 함수는 한 가지 작업만 한다.
- 우리가 함수를 만드는 이유는 큰 개념을 다음 추상화 수준에서 여러 단계로 나눠 수행하기 위해서가 아니던가.
- if 문을 `includeSetupAndTearDownPagesIfTestPage`라는 함수로 만든다면 똑같은 내용을 다르게 표현할 뿐 추상화 수준은 바뀌지 않는다.

### 함수가 ‘한 가지’만 하는지 판단하는 또 하나의 방법

- **단순히 다른 표현이 아니라 의미 있는 이름으로 다른 함수를 추출할 수 있다면 그 함수는 여러 작업을 하는 셈이다.**
  
- 한 가지 작업만 하는 함수는 자연스럽게 섹션으로 나누기 힘들다.
    - ex) 선언, 초기화, 체(?) 등으로 섹션을 나눌 수 있다면 한 함수에서 여러 작업을 한다는 증거!

<br>

## 📌 함수 당 추상화 수준은 하나로!

- **함수가 확실히 ‘한 가지’ 작업만 하려면 함수 내 모든 문장의 추상화 수준이 동일해야 한다.**
- `예시1`은 이 규칙을 확실히 위반함.

    - `getHtml()`은 추상화 수준이 높음
  
    - `String pagePathName = PathParser.render(pagepath);`는 추상화 수준이 중간임.
    - `.append(”\n”)`는 추상화 수준이 아주 낮음.
- 한 함수 내에서 추상화 수준을 섞으면 코드 읽는 사람이 헷갈림.

    - 특정 표현이 근본 개념인지 아니면 세부사항인지 구분하기 어려움.
    - 근본 개념과 세부사항을 뒤섞기 시작하면 생기는 문제
  
        - 깨어진 창문처럼 사람들이 함수에 세부사항을 점점 더 추가함.

### 위에서 아래로 코드 읽기 : 내려가기 규칙

- 코드는 위에서 아래로 이야기처럼 읽혀야 좋다.

  - 위에서 아래로 프로그램을 읽으면 함수 추상화 수준이 한 번에 한 단계씩 낮아진다 ⇒ **내려가기 규칙**
- TO 문단을 읽듯이 프로그램이 읽혀야 한다.

    ```java
    TO 설정 페이지와 해제 페이지를 포함하려면, 설정 페이지를 포함하고, 테스트 페이지 내용을 포함하고, 해제 페이지를 포함한다.
        TO 설정 페이지를 포함하려면, 슈트이면 슈트 설정 페이지를 포함한 후 일반 설정 페이지를 포함한다.
        TO 슈트 설정 페이지를 포함하려면, 부모 계층에서 "SuiteSetUp" 페이지를 찾아 include 문과 페이지 경로를 추가한다.
        TO 부모 계층을 검색하려면, ....
    ```

- 하지만 추상화 수준이 하나인 함수를 구현하기란 쉽지 않다. 핵심은 짧으면서도 ‘한 가지’만 하는 함수다.

## 📌 Switch 문

### Swtich 문의 단점

- 작게 만들기 어렵다.
  
- ‘한 가지’ 작업만 하는 switch 문도 만들기 어렵다. 본질적으로 switch문은 N가지를 처리한다.

### 해결방법

- switch문을 완전히 피할 수는 없다.
- 하지만 각 switch 문을 저차원 클래스에 숨기고 절대로 반복하지 않는 방법은 있다. (다형성을 이용한다.)
    
    ```java
    public Money calculatePay(Employee e)
    throws InvalidEmployeeType {
    	switch (e.type) {
    		case COMMISSIONED:
    			return calculateCommissionedPay(e);
    		case HOURLY:
    			return calculateHourlyPay(e);
    		case SALARIED:
    			return calculateSalariedPay(e);
    		default:
    			throw new InvalidEmployeeType(e.type);
    	}
    }
    ```
    
    - 이 함수의 문제점

        - 함수가 길다.
  
        - ‘한 가지’ 작업만 수행하지 않는다.


### 해결방법

- switch문을 완전히 피할 수는 없다.
- 하지만 각 switch 문을 저차원 클래스에 숨기고 절대로 반복하지 않는 방법은 있다. (다형성을 이용한다.)
    
    ```java
    /*
    	예시4
    */
    public Money calculatePay(Employee e)
    throws InvalidEmployeeType {
    	switch (e.type) {
    		case COMMISSIONED:
    			return calculateCommissionedPay(e);
    		case HOURLY:
    			return calculateHourlyPay(e);
    		case SALARIED:
    			return calculateSalariedPay(e);
    		default:
    			throw new InvalidEmployeeType(e.type);
    	}
    }
    ```
    
    - 이 함수의 문제점
        1. 함수가 길다.
        2. ‘한 가지’ 작업만 수행하지 않는다.
        3. SRP(Single Responsibility Principle)를 위반한다. 
            
            > 클래스는 단 한 개의 책임을 가져야 한다는 원칙
            > 
            - 코드를 변경할 이유가 여럿이기 때문
        4. OCP(Open-Closed Principle)를 위반한다.
            
            > '소프트웨어 개체(클래스, 모듈, 함수 등등)는 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다'는 프로그래밍 원칙
            > 
            - 새 직원 유형을 추가할 때마다 코드를 변경해야 하기 때문이다.
        5. 가장 심각한 문제는, 위 함수와 구조가 동일한 함수가 무한정 존재
            
            ```java
            isPayDay(Employee e, Date date);
            deliverPay(Employee e, Money pay);
            ```
            
            아래는 이 문제를 해결한 코드. switch 문을 추상 팩토리에 꽁꽁 숨겨 아무에게도 보여주지 않는 코드임.
            
            ```java
            /*
            	예시5
            */
            public abstract class Employee {
            	public abstract boolean isPayday();
            	public abstract Money calculatePay();
            	public abstract void deliveryPay(Money pay);
            
            ------------
            
            public interface EmployeeFactory {
            	public Employee makeEmployee(EmployeeRecode r) throws InvalidEmployeeType
            }
            
            ------------
            
            public class EmployeeFactoryImpl implements EmployeeFactory {
            	public Employee makeEmployee(EmployeeRecord r) throws InvalidEmployeeType {
            		switch(r.type) {
            			case COMMMISIONED:
            				return new CommissionedEmployee(r);
            			case HOURLY:
            				return new HourlyEmployee(r);
            			case SALARIED:
            				return new SalariedEmployee(r);
            			default:
            				throw new InvalidEmployeeType(r.type);
            		}
            	}
            }
            ```
            
            - 팩토리는 switch 문을 사용해 적절한 `Employee` 파생 클래스의 인스턴스를 생성
            - `calculatePay`, `isPayday,` `deliveryPay` 등과 같은 함수는 `Employee` 인터페이스를 거쳐 호출
            - **다형성으로 인해 실제 파생 클래스의 함수가 실행**
- 저자는 switch 문은 다형성 객체를 생성하는 코드 안에서만 사용한다.
    - 이렇게 상속 관계로 숨긴 후에는 절대로 다른 코드에 노출하지 않는다.
    - 불가피한 상황은 ㅇㅈ

## 📌 서술적인 이름을 사용하라!

- 이름이 길어도 괜찮음.
- ⭐️ **길고 서술적인 이름이 짧고 어려운 이름보다 좋다.**
- 함수 이름을 정할 때는 여러 단어가 쉽게 읽히는 명명법을 사용한다.
    - 그런 다음, 여러 단어를 사용해 함수 기능을 잘 표현하는 이름을 선택한다.
- 이름을 정하느라 시간을 들여도 괜찮다.
    - 이런저런 이름을 넣어 코드를 읽어보면 더 좋다.
    - 서술적인 이름을 사용하면 개발자 머릿 속에서도 설계가 뚜렷해지므로 코드를 개선하기 쉬워진다.
- 이름을 붙일 때는 일관성이 있어야 한다.
    - 모듈 내에서 함수 이름은 같은 문구, 같은 명사, 동사를 사용한다.
        - 좋은 예 : `includeSetupAndTeardownPages`, `includeSetupPages`, `includeSuiteSetupPage`, `includeSetupPage`

## 📌 함수 인수

- 함수에서 이상적인 인수 개수는 0개(무항)다.
    - 3개(삼항)은 가능한 피하는 편이 좋다.
    - 4개 이상은 특별한 이유가 필요하다. 특별한 이유가 있어도 사용하면 안된다.
- 테스트 관점에서 보면 인수는 더 어렵다.
    - 갖가지 인수 조합으로 함수를 검증하는 테스트 케이스를 작성한다고 생각해보자.
        - 인수가 3개를 넘어서면 인수마다 모든 조합을 구성해 테스트하기가 상당히 부담스럽..
- 출력 인수는 입력 인수보다 이해하기 어렵다.
    - 우리는 함수에 인수로 입력을 넘기고 반환값으로 출력을 받는다는 개념에 익숙
    - 함수에서 인수로 결과를 받으리라 기대하지 않는다.
- 최선은 입력 인수가 없는 경우
    - 차선은 입력 인수가 1개 뿐인 경우
    - 좋은 예 : `SetupTeardownIncluder.render(pageData)`
        - pageData 객체 내용을 렌더링하겠다는 뜻

### 많이 쓰는 단항 형식

- 함수에 인수 1개를 넘기는 이유로 가장 흔한 경우
    1. 인수에 질문을 던지는 경우
        - `boolean fileExists(”MyFile”)`
    2. 인수를 뭔가로 변환해 결과를 반환하는 경우
        - `InputStream fileOpen(”MyFile”)`
            - String 형 파일 이름을 InputStream으로 변환
    - 함수 이름을 지을 때는 두 경우를 분명히 구분한다. (”명령과 조회를 구분하라!” 참조)
- 드물게 아주 유용한 단항 함수 형식 ⇒ **이벤트**!
    - 이벤트 함수는 입력 인수만 있다.
        - 프로그램은 함수 호출을 이벤트로 해석해 입력 인수로 시스템 상태를 바꾼다.
    - `passwordAttemptFailedNtimes(int attempts)`
    - 이벤트 함수는 이벤트라는 사실이 코드에 명확하게 드러나야 한다. ⇒ 이름과 문맥 주의해서 선택
- 지금까지 설명한 경우가 아니라면 단항 함수는 가급적 피한다.
    - 나쁜 예 - 변환 함수에서 출력 인수를 사용하는 경우
        - `void includeSetupPageInto(StringBuffer pageText)`
        - 혼란스러움~
    - 입력 인수를 반환하는 함수라면 반환 결과는 반환값으로 돌려준다.
        - 좋은 예 : `StringBuffer transform(StringBuffer in)`
        - 나쁜 예 : `void transform(StringBuffer out)`
- 입력 인수를 그대로 돌려주는 함수라 할지라도 변환함수 형식을 따르는 편이 좋다.

### 플래그 인수

- 플래그 인수는 추하다!!!!!!!!!!
- 왜냐고????? 함수가 한꺼번에 여러가지를 처리한다고 대놓고 공표하는 셈이니까!!!

### 이항 함수

- **이항함수를 사용하는 잘못된 예 : 무시되는 코드가 생기는 경우**
    - `writeField(name)`은 `writeField(outputStream, name)`보다 이해하기 쉽다.
        - 후자는 첫 인수를 무시해야 한다는 사실을 깨닫는 시간 필요
            
            ⇒ 이 사실이 문제를 일으킴. 왜???? 어떤 코드든 절대로 무시하면 안되니까, 무시한 코드에 오류가 숨어드니까!!!
            
- **이항 함수를 사용하는 적절한 예**
    - `Point p = new Point(0, 0)`
    - 여기서 인수 2개는 한 값을 표현하는 두 요소
    - 두 요소에는 자연적인 순서도 있음.
- 당연하게 여겨지는 이항함수 `assertionEquals(expected, actual)` 도 문제가 있다.
    - expected 인수에 actual 값을 집어넣는 실수가 많음.
    - 두 인수는 자연적인 순서가 없음!
- **무조건 나쁘다는 건 아님.**
    - 불가피한 경우도 있지~ 그래도 가능하면 단항함수로 바꾸자구~ 0.<
- writeField 메서드 개선방안
    - `writeField` 메서드를 `outputStream` 클래스의 구성원으로 만들어 `outputStream.writeField(name)`으로 호출하기
    - `outputStream`을 현재 클래스 구성원 변수로 만들어 인수로 넘기지 않기
    - `FieldWriter`라는 새 클래스 생성 → 구성자에서 `outputStream`을 받고 write 메서드 구현

### 삼항 함수

- 삼항 함수는 훨씬 더 이해하기 어려움
- 좋지 않은 예시 : `assertionEquals(message, expected, actual)`
    - 매번 함수를 볼 때마다 주춤! 아 message를 무시해야 한다는 사실을 상기해야 함.
- 꽤괜 예시 : assertionEquals(1.0, amount, .001)
    - 부동소수점 비교가 상대적 << 사실 잘 이해 못..

### 인수 객체

- 인수가 2-3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어본다.

```java
Circle makeCircle(double x, double y, double radius);
Circle makeCircle(Point center, double radius);
```

- 객체를 생성해 인수를 줄였음.

### 인수 목록

- 때로는 인수 개수가 가변적인 함수도 필요

```java
String.format("%s worked %.2f" hours.", name, hours);
```

- 위 예처럼 가변 인수 전부를 동등하게 취급하면 List 형 인수 하나로 취급 가능
- 실제로 String.format 함수는 이항 함수임.
    
    ```java
    public String format(String format, Object... args)
    ```
    

### 동사와 키워드

- **단항 함수는 함수와 인수가 동사 / 명사 쌍을 이뤄야 한다.**
    - `write(name)`
    - `writeField(name)`
- 함수 이름에 키워드를 추가하는 형식도 가능
    - `assertionEquals` → `assertionExpectedEqualsActual(expected, actual)`

## 📌 부수 효과를 일으키지 마라!

- 함수에서 한 가지를 하겠다고 약속하고선 남몰래 다른 짓도 하지 마!
- 예를 들어 함수로 넘어온 인수나 시스템 전역 변수 수정 같은 것!
    
    ```java
    public class UserValidator {
    	private Cryptographer crytoprahpher; 
    	
    	public boolean checkPassword(String userName, String password) {
    		User user = UserGateway.findByName(userName);
    		if (user != User.NULL) {
    			String codedPrase = user.getPhraseEncoededByPassword();
    			String phrase = crytoprahpher.decrypt(codedPhrase, password);
    			if("Valid Password".equals(phrase)) {
    				Session.initialize();
    				return true;
    			}
    		}
    		return false;
    	}	
    }
    ```
    
    - ✅ 부수 효과가 일어나는 부분 : `Session.initialize()`
- 부수효과는 시간적인 결합을 초래함.
    - 즉, checkPassword 함수는 특정 상황에서만 호출 가능
- 만약 시간적인 결합이 필요하다면 함수 이름에 분명히 명시할 것
    - `checkPassword` → `checkPasswordAndInitializeSession`

### 출력 인수

- 일반적으로 우리는 인수를 함수 입력으로 인식 → 출력 인수 가급적 사용 ㄴㄴ
- 예시를 보자.
    
    ```java
    appendFooters(s);
    ```
    
    - 🤔 무언가에 s를 바닥글로 첨부할까? s에 바닥글을 첨부할까? 함수 선언부는 아래와 같다.
    
    ```java
    public void appendFooter(StringBuffer report)
    ```
    
    - 인수 `s`가 출력 인수인 걸 함수 선언부를 찾아봐야 알 수 있음.
    - 거슬린다는 의미니까 피해야 함.
- 객체 지향 언어에서는 출력 인수를 사용할 필요가 거의 없다.
    - 출력 인수로 사용하라고 설계한 변수가 바로 `this`
    - 따라서 `appendFooter`는 다음과 같이 호출하는 방식이 좋다.
    
    ```java
    report.appendFooter()
    ```
    
- **함수에서 상태를 변경해야 한다면 함수가 속한 객체 상태를 변경하는 방식을 택하라.**

## 📌 명령과 조회를 분리하라!

- 함수는 **뭔가를 수행**하거나 **뭔가에 답하거나** 둘 중 하나만 해야 한다.
    - 객체 상태 변경 / 객체 정보 반환을 둘 다 하지 마라.
- 예시를 보자.
    
    ```java
    public boolean set(String attribute, String value);
    // 이름이 attribute인 속성을 찾아 value로 설정한 후
    // 성공하면 true를 만환하고 실패하면 false를 반환한다.
    ```
    
    - 이 함수를 쓰면 다음과 같이 괴상한 코드가 나옴.
    
    ```java
    if(set("username", "unclebob")) ...
    ```
    
    - `setAndCheckIfExists`라고 바꾸는 방법도 있음. 근데 if문에 넣고 보면 여전히 어색
    - 해결책은 명령과 조회를 분리해 혼란을 애초에 뿌리뽑는 방법

## 📌 오류 코드보다 예외를 사용하라!

- 명령 함수에서 오류 코드를 반환하는 방식은 명령/조회 분리 규칙을 미묘하게 위반
- 자칫하면 if 문에서 명령을 표현식으로 사용하기 쉬운 탓
- 나쁜 예시
    
    ```java
    if (deletePage(page) == E_OK)
    ```
    
    - 위 코드의 문제점
        - 여러 단계로 중첩되는 코드를 야기함.
        - 오류 코드를 반환하면 호출자는 오류 코드를 곧바로 처리해야 한다는 문제에 부딪힘.
- 좋은 예시
    
    ```java
    try {
    	deletePage(page);
    	registry.deleteReference(page.name);
    	configKeys.deleteKey(page.name.makeKey());
    }
    catch (Exception e) {
    	logger.log(e.getMessage());
    }
    ```
    
    - 오류 처리 코드가 원래 코드에서 분리되므로 코드가 깔끔해진다.

### Try/Catch 블록 뽑아내기

- try/catch 블록은 추함.
    - 정상 동작과 오류처리 동작을 뒤섞는다.
- try/catch 블록을 별도 함수로 뽑아내는 편이 좋다.
    
    ```java
    public void delete(Page page) {
    	try {
    		deletePageAndAllReferences(page);
    	}
    	catch (Exception e) {
    		logError(e);
    	}
    }
    
    private void deletePageAndAllReferences(Page page) throws Exception {
    	deletePage(page);
    	registry.deleteReference(page.name);
    	configKey.deleteKey(page.name.makeKey());
    }
    
    private void logError(Exception e) {
    	logger.log(e.getMessage());
    }
    ```
    
    - 위에서 `delete` 함수는 모든 오류를 처리한다. ⇒ 코드 이해가 쉬움.
    - 실제로 페이지를 제거하는 함수는 deletePageAndAllReferences임.
        - 이 함수는 예외를 처리하지 않음.
    - 이렇게 정상 동작과 오류 처리 동작을 분리하면 코드를 이해하고 수정하기가 쉬워짐.

### 오류 처리도 한 가지 작업이다

- 오류 처리도 ‘한 가지’ 작업에 속한다.
    
    ⇒ 오류 처리 함수는 오류’만’ 처리해야 마땅함.
    
- 함수에 키워드 `try`가 있다면 함수는 `try`문으로 시작해 `catch/finally` 문으로 끝나야 한다는 말이다.

### Error.java 의존성 자석

- 오류코드를 반환한다는 이야기는, 어디선가 오류 코드를 정의한다는 뜻

```java
public enum Error {
	OK,
	INVALID,
	NO_SUCH,
	LOCKED,
	OUT_OF_RESOURCES,
	WAITING_FOR_EVENT;
}
```

- 위와 같은 클래스는 의존성 자석
  
- 다른 클래스에서 Error enum을 import 해서 사용해야 함
    - Error enum이 변한다면 Error enum을 사용하는 클래스 전부를 다시 컴파일하고 다시 배치해야 한다.
  
    - 재컴파일 / 재배치가 번거롭기에 프로그래머는 새 오류 코드 대신 기존 오류 코드를 재사용함.
- 오류 코드 대신 예외를 사용하면 새 예외는 Exception 클래스에서 파생된다. ⇒ 재컴파일/재배치 없이도 새 예외 클래스를 추가할 수 있다.

## 📌 반복하지 마라!

- 중복이 있으면 안됨.
  
    - 코드 길이가 늘어날 뿐 아니라 알고리즘이 변하면 손봐야 할 곳이 많아짐.
  
- 예시 : 중복을 include 방법으로 없앤 [SetupTeardownIncluder.java](http://SetupTeardownIncluder.java) 참조

## 📌 구조적 프로그래밍

- 에츠허르 데이크스트라의 구조적 프로그래밍 원칙
    - 모든 함수와 함수 내 모든 블록에 입구와 출구 하나만 존재해야 함.
  
    - 즉, 함수는 return 문이 하나여야 함.
        - 루프 안에서 break나 continue를 사용해선 안되며 goto는 절대 안됨.
  
- 그러나 함수가 작다면 위 규칙은 별 이익 제공 Xx
    - 함수를 작게 만든다면 return, break, continue를 여러 차례 사용해도 ㄱㅊ음.
  
    - 오히려 때로는 단일 입/출구 규칙보다 의도를 표현하기 쉬워짐.
- 반면 goto문은 큰 함수에서만 의미 o / 작은 함수에서는 피하자.

## 📌 함수를 어떻게 짜죠?

- 소프트웨어를 짜는 행위는 여느 글짓기와 비슷
- 함수를 짤 때에도 비슷하다.

    - 처음에는 길고 복잡. 그걸 빠짐없이 테스트하는 단위 테스트 케이스도 만듦.
  
    - 그런 다음 코드를 다듬고, 함수를 만들고, 이름을 바꾸고, 중복을 제거함.
        - 때로는 전체 클래스를 쪼개기도 함.

> 처음부터 짜내지 않는다. 그게 가능한 사람은 없으리라.
> 

## 📌 결론

- 프로그래밍의 기술은 언제나 언어 설계의 기술이다. 예전에도 그랬고 지금도 마찬가지다.

- 진짜 목표는 시스템이라는 이야기를 풀어가는 데 있다는 사실을 명심하자.
- 여러분이 작성하는 함수가 분명하고 정확한 언어로 깔끔하게 맞아 떨어져야 이야기를 풀어가기가 쉬워진다는 사실 기억하기

# 추가적인 질문이나 의문점


- 추상화 수준… 어렵군..
  
- 예시5 코드는 한 번 더 뜯어봐야 할듯

# 이 장에서 얻은 것


- 함수는 한 가지만 해야 한다.
- 짧으면 짧을 수록 좋다.
  
- 함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안됨.
- 함수 인수는 적을수록 좋다.
    - 출력 인수 되도록 사용 ㄴㄴ
  
    - 인수가 2-3개 필요하다면 일부를 독자적인 클래스 변수로 선언할 가능성을 짚어본다.
- 길고 서술적인 이름이 짧고 어려운 이름보다 좋다.
- 함수에서 한 가지를 하겠다고 약속하고선 남몰래 다른 짓도 하지 마!
- 오류 코드 대신 예외를 사용하자.
- try / catch 블록을 별도 함수로 뽑아내는 편이 좋다. <<< 이거 감명 깊었음.
- 중복 ㄴㄴ