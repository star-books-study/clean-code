# 3장 : 함수

> 어떤 프로그램이든 가장 기본적인 단위가 함수다.
> 
- 다음 코드를 보자.
    
    ```java
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
public static String renderPageWithSetupsAndTeardowns(
    PageData, pageData, boolean isSuite) throws Exception {
    if(isTestPage(pageData))
        includeSetupAndTearDownPages(pageData, isSuite);
    return pageData.getHtml();
}
```

### 블록과 들여쓰기

- if 문 / else 문 / while 문 등에 들어가는 블록은 한 줄이어야 한다.
    - 대개 거기서 함수 호출
  
    - 그럼 바깥을 감싸는 함수가 작아짐.
    - (블록 안에서 호출하는 함수 이름을 적절히 짓는다면) 코드를 이해하기도 쉬워짐.
- 중첩 구조가 생길만큼 함수가 커져서는 안됨.

    - 함수에서 들여쓰기 수준은 1단이나 2단을 넘어서면 안됨.

(엄청 명쾌하게 말해주네… 이렇게 짧아야 하는 줄 몰랐다)