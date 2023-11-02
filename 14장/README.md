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
```java
// 목록 14-2 Args.java

public class Args {
	private Map<Character, ArgumentMarchaler> malshalers;
	private Set<Character> argsFound;
	private ListIterator<String> currentArgument;

	public Args(String schema, String[] args) throws ArgsException {
		marchalers = new HashMap<Character, ArgumentMarshaler>();
		argsFound = new HashASet<Character>();

		parseSchema(schema);
		parseArgumentStrings(Arrays.asList(args));
	}

	private void parseSchema(String schema) throws ArgsException {
		for (String element : schema.split(","))
			if (element.length() > 0)
				parseSchemaElement(element.trim());
	}
	
	private void parseSchemaElement(String element) throws ArgsException {
		char elementId = element.charAt(0);
		String elementTail = element.substring(1);
		validateSchemaElementId(elementId);
		if (elementTail.length() == 0)
			malshalers.put(elementId, new BooleanArgumentMarchaler());
		else if (elementTail.equals("*"))
			marshalers.put(elementId, new StringArgumentMarchaler());
		else if (elementTail.equals("#"))
			marshalers.put(elementId, new IntegerArgumentMarchaler());
		else if (elementTail.equals("[*]"))
			marshalers.put(elementId, new StringArrayArgumentMarchaler());
		else
			throw new ArgsException(INVALID_ARGUMENT_FORMAT, elementId, elementTail);
		} // 어머 ArgsException 어떻게 생겼을지 궁금하다
	
		private void validateSchemaElementId(char elementId) throws ArgsException {
			if (!Character.isLetter(elementId))
				throw new ArgsException(INVALID_ARGUMENT_NAME, elementId, null);
		}
	
		private void parseArgumentStrings(List<String> argsList) throws ArgsException {
			for (currentArgument = argsList.listIterator(); currentArgument.hasNext();)

```


### 어떻게 짰느냐고?

- 처음부터 그렇게 짠 건 아님.

## 📌 Args : 1차 초안

- 맨 처음 짰던 Args 클래스
### 그래서 멈췄다
- 추가할 인수 유형이 적어도 두 개는 더 있었는데 더 나빠질 것 같아서 멈춤.

### 점진적으로 개선하다

### String 인수

### 결론
