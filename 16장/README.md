# 16장 : SerialDate 리팩터링

## 📌 들어가며

- JCommon 라이브러리 → [org.free.dat](http://org.free.date) 패키지 → SerialDate 클래스
- SerialDate 코드는 분명히 ‘우수한’ 코드
- SerialDate는 날짜를 표현하는 자바 클래스임. 특정 날짜만 표현하는 클래스(예 : 2023년 11월 15일)

## 📌 첫째, 돌려보자

- SerialDateTests 클래스는 단위 테스트 케이스 몇 개를 포함하는 클래스임.
- 단, 모든 경우를 점검하지는 않음(코드 커버리지 정도가 50% 정도에 불과) → 저자가 단위 테스트 케이스 구현
- 따라서 더 높은 코드 커버리지를 위해 단위 테스트 케이스를 구현하고, 해당 단위 테스트에서 실패한 경우를 성공시킬 수 있도록 원본 클래스를 리팩터링
## 📌 둘째, 고쳐보자

1. 라이선스 정보, 저작권, 작성자, 변경 이력 중 라이선스 정보와 저작권은 보존하고 변경 이력은 삭제한다.
2. import문 줄이기
3. 주석이 네 가지 언어를 사용해서 보기 어려움 → 차라리 주석 전부를 <pre>로 감싸기
4. 클래스 이름인 SerialDate는 그다지 서술적인 이름이 아니고, 구현을 암시하는데 실상은 추상 클래스다 → DayDate로 변경
5. MonthConstants는 enum으로 정의해야 마땅하다.
6. DayDate 클래스에서 파생한 SpreadsheetDate의 구현과 관련된 부분은 SpreadsheetDate로 옮긴다.
7. 부모 클래스는 자식 클래스를 몰라야 바람직 하므로 ABSTRACT FACTORY 패턴을 적용해 DayDateFactory는 DayDate 인스턴스를 생성하며 (최대 날짜와 최소 날짜 등) 구현 관련 질문에도 답하게 구현했다.
8. createInstance 메서드를 조금 더 서술적인 이름인 makeDate라는 이름으로 변경
9. …
10. 필요없는 주석 삭제 및 위와 비슷한 과정 반복

---

**지금까지 한 작업을 정리하면 다음과 같다.**

1. 처음에 나오는 주석은 너무 오래 되었다. 그래서 간단하게 고치고 개선했다.
2. enum을 모두 독자적인 소스 파일로 옮겼다.
3. 정적 변수(dateFormatSymbols)와 정적 메서드(getMonthNames, isLeapYear, lastDayOfMonth)를 DateUtil이라는 새 클래스로 옮겼다.
4. 일부 추상 메서드를 DayDate 클래스로 끌어올렸다.
5. Month.make를 Month.fromInt로 변경했다. 다른 enum도 똑같이 변경했다. 또한 모든 euum에 toInt() 접근자를 생성하고 index 필드를 private로 설정했다.
6. plusYears와 plusMonths에 흥미로운 중복이 있었는데, correctLastDayOfMonth라는 새 메서드를 생성해 중복을 없앴다.
7. 팔방미인으로 사용하던 숫자 1을 없애고 Month.JANUARY.toInt() 혹은 Day.SUNDAY.toInt()로 적절히 변경했다.
8. SpeedsheetDate 코드를 살펴보고 알고리즘을 조금 손봤다.

## 📌 결론

- 다시 한 번 우리는 보이스카우트 규칙을 따랐다.
- 테스트 커버리지 증가했고, 버그도 고쳤으며, 코드 크기도 줄었고, 코드가 명확해짐.
