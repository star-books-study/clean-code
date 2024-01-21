# 15장 : JUnit 들여다보기

## 📌 들어가며

- 이 장에서 할 것 : JUnit 프레임워크 코드 평가

## 📌JUnit 프레임워크

- 문자열 비교 오류를 파악할 때 유용한 코드인 ComparisonCompactor라는 모듈을 살펴보자
    - 두 문자열을 받아 차이를 반환한다
    - 예를 들어 ABCDE와 ABXDE를 받아 <...B[X] D…>를 반환한다
### ComparisonCompactor 모듈에 대한 테스트 코드

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

### 개선 전 ComparisionCompactor 코드

```java
// 목록 15-2 ComparisionCompactor.java(원본)

import junit.framework;

public class ComparisionCompactor {

    private static final String ELLIPSIS = "...";
    private static final String DELTA_END = "]";
    private static final String DELTA_START = "[";

    private int **fContextLength;**
    private String **fExpected;**
    private String **fActual;**
    private int **fPrefix;**
    private int **fSuffix;**

    public ComparisionCompactor(int contextLength,
            String expected,
            String actual) {
        fContextLength = contextLength;
        fExpected = expected;
        fActual = actual;
    }

    public String compact(String message) {
        **if (fExpected == null || fActual == null || areStringsEqual())
            return Asset.format(message, fExpected, fActual);**

        **findCommonPrefix();
        findCommonSuffix();**
        **String expected = compactString(fExpected);
        String actual = compactString(fActual);**
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

개선할 점은 다음과 같다.

1. 멤버변수 앞에 붙인 접두어 `f` → 오늘날 사용하지 않으므로 제거
2. `compact` 함수 시작부에 캡슐화되지 않은 조건문 → `shoudNotCompact()` 메서드로 뽑아내기
3. 멤버변수와 지역변수명이 겹친다(`expected`와 `actual`) → 지역변수명을 `compactExpected`, `compactActual`로 변경
4. `shoudNotCompact()`의 if문의 부정문은 긍정문보다 이해하기 약간 더 어렵다 → 첫 문장 if를 긍정으로 만들어 조건문을 반전한다. (`canBeCompacted()`)
5. `canBeCompacted()`의 함수명이 이상하다. 리턴값이 false면 압축하지 않으므로 오류 점검이라는 부가 단계가 숨겨진다. 게다가 함수는 단순히 압축된 문자열이 아니라 형식이 갖춰진 문자열을 반환한다. → `formatCompactedComparision(String message)`
6. 예상 묹다열과 실제문자열을 압축하는 부분을 빼내어 compactExpectedAndActual이라는 메서드로 만든다. 하지만 형식을 맞추는 작업은 `formatCompactedComparision(String message)`에게 전적으로 맡긴다. → 이 과정에서 compactActual과 compactActual을 멤버변수로 승격
7. `findCommonPrefix()`와 `findCommonSuffix()`를 → `findCommonPrefixAndSuffix()`로 바꾸고 여기서 findCommonPrefix를 호출
8. `suffixIndex`는 실제로 접미어 길이를 나타냄 → index와 length 중 적합한 변수명은 length → `suffixLength`
9. 필요없는 if문 삭제

### 개선 후 ComparisionCompactor 코드

```java
// 목록 15-5. ComparisonCompactor.java (최종 버전)

package junit.framework;
public class ComparisonCompactor {
  private static final String ELLIPSIS = "...";
  private static final String DELTA_END = "]";
  private static final String DELTA_START = "[";
  private int contextLength;
  private String expected;
  private String actual;
  private int prefixLength;
  private int suffixLength;
  public ComparisonCompactor(
    int contextLength, String expected, String actual
  ) {
    this.contextLength = contextLength;
    this.expected = expected;
    this.actual = actual;
  }
  public String formatCompactedComparison(String message) {
    String compactExpected = expected;
    String compactActual = actual;
    if (shouldBeCompacted()) {
      findCommonPrefixAndSuffix();
      compactExpected = compact(expected);
      compactActual = compact(actual);
    }
    return Assert.format(message, compactExpected, compactActual);
  }
  private boolean shouldBeCompacted() {
    return !shouldNotBeCompacted();
  }
  private boolean shouldNotBeCompacted() {
    return expected == null ||
            actual == null ||
            expected.equals(actual);
  }
  private void findCommonPrefixAndSuffix() {
    findCommonPrefix();
    suffixLength = 0;
    for (; !suffixOverlapsPrefix(); suffixLength++) {
      if (charFromEnd(expected, suffixLength) !=
          charFromEnd(actual, suffixLength)
      )
        break;
    }
  }
  private char charFromEnd(String s, int i) {
    return s.charAt(s.length() - i - 1);
  }
  private boolean suffixOverlapsPrefix() {
    return actual.length() - suffixLength <= prefixLength ||
      expected.length() - suffixLength <= prefixLength;
  }
  private void findCommonPrefix() {
    prefixLength = 0;
    int end = Math.min(expected.length(), actual.length());
    for (; prefixLength < end; prefixLength++)
      if (expected.charAt(prefixLength) != actual.charAt(prefixLength))
        break;
  }
  private String compact(String s) {
    return new StringBuilder()
      .append(startingEllipsis())
      .append(startingContext())
      .append(DELTA_START)
      .append(delta(s))
      .append(DELTA_END)
      .append(endingContext())
      .append(endingEllipsis())
      .toString();
  }
  private String startingEllipsis() {
    return prefixLength > contextLength ? ELLIPSIS : "";
  }
  private String startingContext() {
    int contextStart = Math.max(0, prefixLength - contextLength);
    int contextEnd = prefixLength;
    return expected.substring(contextStart, contextEnd);
  }
  private String delta(String s) {
    int deltaStart = prefixLength;
    int deltaEnd = s.length() - suffixLength;
    return s.substring(deltaStart, deltaEnd);
  }
  private String endingContext() {
    int contextStart = expected.length() - suffixLength;
    int contextEnd =
      Math.min(contextStart + contextLength, expected.length());
    return expected.substring(contextStart, contextEnd);
  }
  private String endingEllipsis() {
    return (suffixLength > contextLength ? ELLIPSIS : "");
  }
}
```

## 📌 결론

- 코드를 처음보다 조금 더 깨끗하게 만드는 책임은 우리 모두에게 있다. (보이스카우트 규칙)
