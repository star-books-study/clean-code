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

- 하지만 추상화 수준이 하나인 함수를 구현하기란 쉽지 않다. 핵심은 짧으면서도 ‘한 가지’만 하는 함수다.