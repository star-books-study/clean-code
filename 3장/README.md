# 3장 : 함수

> 어떤 프로그램이든 가장 기본적인 단위가 함수다.
> 
- 다음 코드를 보자.
    
    ```java
    public static String testTableHtml(
        PageData pageData,
        boolean includeSuiteSetUp
    ) thorws Exception {
        WikiPage wikiPage = pageData.getWikiPage();
        if (pageData.hasAttribute("Test)) {
            if(includeSuiteSetup) {
                WikiPage suiteSetup = 
                    PageCrawlerImpl.getInheritedPage(
                        SuiteResponder.SUITE_SETUP_NAME, wikipage
                    );

                // 더 쓰기
            }
        }
    }
    ```