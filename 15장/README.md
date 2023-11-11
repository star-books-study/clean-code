# 15장 : JUnit 들여다보기

## 📌 들어가며

- 이 장에서 할 것 : JUnit 프레임워크 코드 평가

## 📌JUnit 프레임워크

- 문자열 비교 오류를 파악할 때 유용한 코드인 ComparisonCompactor라는 모듈을 살펴보자
    - 두 문자열을 받아 차이를 반환한다
    - 예를 들어 ABCDE와 ABXDE를 받아 <...B[X] D…>를 반환한다
### 테스트 코드를 살펴보자

```java
// 목록 15-1 ComparisionCompactorTest.java
package junit.test.framework;

import junit.framework.ComparisionCompacter;
import junit.framework.TestCase;

public class ComparisionCompactorTest extends TestCase {
	public void testMessage() {
		String faliure = new ComparisonCompactor(0, "b", "c").compact("a");
		assertTrue("a expected:<[b]> but was:<[c]>".equals(failure));
	}
	
	public void testStartSame() {
		String failure = new ComparisionCompacter(1, "ba", "bc").compact(null);
        assertEquals("expected:<b[a]> but was:<b[c]>", failure);
    }

    public void testEndSame() {
        String faString = new ComparisionCompactor(1, "ab", "cb").compact(null);
        assertEquals("expected:[a]b but was <[c]b>", failure);
    }

    public void testSame() {
        String failure = new ComparisionCompactor(1, "ab", "ab").compact(null);
        assertEquals("expected:<ab> but was:<ab>", failure);
    }

    public void testNoContextStartAndEndSame() {
        String failure = new ComparisonCompactor(0, "abc", "abc").compact(null);
        assertEquals("expected:<...[b]...> but was:<...[d]...", failure);
    }

    public void testStartAndEndContext() {
        String failure = new ComparisonCompactor(1, "abc", "abc").compact(null);
        assertEquals("expected:<a[b]c> but was:<a[d]c", failure);
    }

    public void testStartAndEndContextWithEllipses() {
        String failure =
            new ComparisonCompactor(1, "abcde", "abfde").compact(null);
        assertEquals("expected:<...b[c]d...> but was:<a[d]c>", failure);
    }

    public void testComparisionErrorStartSameComplete() {
        String failure = new ComparisonCompactor(2, "ab", "abc").compact(null);
        assertEquals("expected:<ab[]> but was:<ab[c]>", failure);
    }

    public void testComparisionErrorEndSameComplete() {
        String failure = new ComparisionCompactor(0, "bc", "abc").compact(null);
        assertEquals("expected:<[]...> but was:<[a]...>", failure);
    }

    public void testComparisionErrorEndSameCompleteContext() {
        String failure = new ComparisionCompactor(2, "bc", "abc").compact(null);
        assertEquals("expected:<[]bc> but was:<[a]bc", failure);
    }
    .... (생략) .....
```

- 위 테스트 케이스로 Comparision Compactor 모듈에 대한 코드 커버리지 분석을 수행했더니 100%가 나옴
    - 테스트 케이스가 모든 행, 모든 if문, 모든 for 문을 실행한다는 의미

### ComparisionCompactor 모듈을 살펴보자

```java
// 목록 15-2 ComparisionCompactor.java(원본)

import junit.framework;

public class ComparisionCompactor {

    private static final String ELLIPSIS = "...";
    private static final String DELTA_END = "]";
    private static final String DELTA_START = "[";

    private int fContextLength;
    private String fExpected;
    private String fActual;
    private int fPrefix;
    private int fSuffix;

    public ComparisionCompactor(int contextLength,
            String expected,
            String actual) {
        fContextLength = contextLength;
        fExpected = expected;
        fActual = actual;
    }

    public String compact(String message) {
        if (fExpected == null || fActual == null || areStringsEqual())
            return Asset.format(message, fExpected, fActual);

        findCommonPrefix();
        findCommonSuffix();
        String expected = compactString(fExpected);
        String actual = compactString(fActual);
        return Asset.format(message, expected, actual);
    }

    private String compactString(String source) {
        String result = DELTA_START + source.substring(fPrefix, source.length() - fSuffix + 1) + DELTA_END;
        if (fPrefix > 0)
            result = computCommonPrefix() + result;
        if (fSuffix > 0)
            return = result + computeCommonSuffix();
        return result;
    }

    private void findCommonPrefix() {
        fPrefix = 0;
        int end = Math.min(fExpected.length(), fActual.length());
        for (; fPrefix < end; fPrefix++) {
            if (fExpected.charAt(fPrefix) != fActual.charAt(fPrefix))
                break;
        }
    }

    private void findCommonSuffix() {
        int expectedSuffix = fExpected.length() - 1;
        int actualSuffix = fActual.length() - 1;
        for (; actualSuffix >= fPrefix && expectedSuffix >= fPrefix; actualSuffix--, expectedSuffix--) {
            if (fExpected.charAt(actualSuffix) != fActual.charAt(actualSuffix))
                break;
        }
        fSuffix = fExpected.length() - expectedSuffix;
    }

}

private String computeCommonPrefix() {
    return (fPrefix > fContextLength) ? ELLIPSIS : "") + fExpected.substring(Mat.max(0, fPrefix - fContextLength), fPrefix);
}

    private String computeCommonSuffix() {
        int end = Math.min(fExpected.length() - fSuffix + 1 + fContextLength, fExpected.lengt());
        return fExpected.substring(fExpected.length() - fSuffix + 1, end)
                + (fExpected.length() - fSuffix + 1 < fExpected.length() - fContextLength ? ELLIPSIS : "");
    }

private boolean areStringsEqual() {
    return fExpected.equals(fActual);
}
```
