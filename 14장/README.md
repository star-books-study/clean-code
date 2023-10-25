# 14장 : 점진적인 개선
## 📌 들어가며

- 프로그램을 짜다보면 종종 명령행 인수의 구문을 분석할 필요가 생김.
- 편리한 유틸리티가 없다면 main 함수로 넘어오는 문자열 배열을 직접 분석하게 됨.
- 딱 맞는 유틸리티가 없으면 직접 짜겠다 결심 → `Args`라 하자.
- Args의 사용법 : Args 생성자에 인수 문자열과 형식 문자열을 넘겨 Args 인스턴스를 생성한 후 거기에다 인수 값을 질의한다.

```java
// 목록 14-1 간단한 Args 사용법

public static void main(String[] args) {
	try {
		Args arg = new Args("l,p#,d*", args);
		boolean logging = arg.getBoolean('l');
		int port = arg.getInt('p');
		String directory = arg.getString('d');
		executeApplication(logging, port, directory);
	} catch {
		System.out.printf("Argument error: %s\n", e.errorMessage());
	}
}
```

- 첫 번째 매개변수 : 형식 또는 스키마 지정
- 두 번째 매재변수 : main으로 넘어온 명령행 인수 배열 그 자체

## 📌 Args 구현

- Args 클래스를 개선하는 예시가 나와 있음.

### 어떻게 짰느냐고?

- 처음부터 그렇게 짠 건 아님.

## 📌 Args : 1차 초안

- 맨 처음 짰던 Args 클래스
### 그래서 멈췄다
- 추가할 인수 유형이 적어도 두 개는 더 있었는데 더 나빠질 것 같아서 멈춤.

### 점진적으로 개선하다
