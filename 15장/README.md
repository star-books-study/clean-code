# 15장 : JUnit 들여다보기

## 📌 들어가며

- 이 장에서 할 것 : JUnit 프레임워크 코드 평가

## 📌JUnit 프레임워크

- 문자열 비교 오류를 파악할 때 유용한 코드인 ComparisonCompactor라는 모듈을 살펴보자
    - 두 문자열을 받아 차이를 반환한다
    - 예를 들어 ABCDE와 ABXDE를 받아 <...B[X] D…>를 반환한다
- 테스트 ㅗ드를 살펴보자

```java
// 목록 15-1 ComparisionCompactorTest.java
package junit.test.framework;
```
