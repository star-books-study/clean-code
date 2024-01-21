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

- Args 클래스 구현부
- 흉내낼 가치가 있는 코드,,,

> 🔑 malshal : 모으다, 집결시키다



```sql
책에 있는 모든 코드를 가져오기는 너무 길어서 핵심적인 코드 부분만 작성하고 나머지 구현 코드는 간단한 설명으로 대체했습니다.
```

```java
// 목록 14-2.Args.java

public class Args {
	private Map<Character, ArgumentMarshaler> marshalers;
	private Set<Character> argsFound;
	private ListIterator<String> currentArgument;

	public Args(String schema, String[] args) throws ArgsException {
		malshalers = new HashMap<Character, ArgumentMarchaler>();
		argsFound = new HashSet<Character>();
	
		parseSchema(schema);
		parseArgumentStrings(Arrays.asList(args));
	}
	
	public parseSchema(String schema) throws ArgsException {
		for(String element : schema.split(","))
			if(element.length() > 0)
				parseSchemaElement(element.trim());
	}
		
	public parseSchemaElement(String element) throws ArgsException {
		element의 첫 번째 char을 elementId로 설정
		이후 char을 elementTail로 설정
		validateSchemaElementId(elementId);
		if (elementTail.length() == 0) 
			malchalers.put(elementId, new BooleanArguementMarchaler());
		else if (elementTail.equals("*"))
			malchalers.put(elementId, new BooleanArguementMarchaler());
		else if ("#"일 때)
			malchalers.put(elementId, new IntegerArguementMarchaler());
		else if ("##"일 때)
			malchalers.put(elementId, new DoubleArguementMarchaler());
		else if ("[*]"일 때)
			malchalers.put(elementId, new StringArrayArguementMarchaler());
		else
			throw new ArgsException(INVALID_ARGUMENT_FORMAT, elementId, elementTail);
	}

	private void validateSchemaElementId(char elementId) throws ArgsException {
		if (!Character.isLetter(elementId))
			throw new ArgsException(INVALID_ARGUMENT_NAME, elementId, null);
	}

	private void parseArgumentStrings(List<String> argsList) throws ArgsException {
		for(argsList를 순회하며 currentArgument에 현재 값을 저장) {
			String argsString = currentArgument.next();
			if (argsString.startsWith("-")) {
				parseArgumentCharacters(argsString.substring(1));
			} else {
				currentArgument.previous();
				break;
			}
		}
	}
	
	private void parseArgumentCharacters(String argChars) throws ArgsException {
		for(argsChars의 길이만큼 i를 증가시키며)
			parseArgumentCharacter(argChars.charAt(i));
	}

	private void parseArgumentCharacter(char argsChar) throws ArgsException {
		ArgumentMarchaler m = marshalers.get(argChar);
		if (m == null) {
			throws new ArgsException(UNEXPECTED_ARGUMENT, argsChar, null);
		} else {
			argsFound.add(argChar);
			try {
				m.set(currentArgument);
			} catch (ArgsException e) {
				e.setErrorArgumentId(argChar);
				throw e;
			}
		}
	}

	public boolean has(char arg) {
		return argsFound.contains(arg);
	}

	public int nextArguemtn() {
		return currentArguement.nextIndex();
	}

	public int getBoolean(char arg) {
		return BooleanArgumentMarshalers.getValue(malshalers.get(arg))
	}

	public String getString(char arg) {
		// 생략
	}

	public String getInt(char arg) {
		// 생략
	}

	public String getDouble(char arg) {
		// 생략
	}

	public String getStringArray(char arg) {
		// 생략
	}
```

- 여기저기 뒤적일 필요 없이 위에서 아래로 코드가 읽힌다는 사실에 주목
- 코드를 주의 깊게 읽으면 ArgumentMarshaler 인터페이스가 무엇이며 파생 클래스가 무슨 기능을 하는지 이해할 수 있음.

```java
// 목록 14-3 ArgumentMarchaler.java
public interface ArgumentMarshaler {
	void set(Iterator<String> currentArgument) throws ArgsException;
}
```

```java
// 목록 14-4 BooleanArgumentMarshaler
public class BooleanArgumentMarchaler implements ArgumentMarshaler {
	private boolean booleanValue = false;

	public void set(Iterator<String> currentArgument) throws ArgsException {
		booleanValue = true;
	}
	
	public static boolean getValue(ArgumentMalshaler am) {
		if (am != null && am instanceof BooleanArgumentMarchaler)
			return ((BooleanArgumentMarchaler) am).booleanValue;
		else
			return false;
	}
}
```

```java
// 목록 14-5 StringArgumentMarshaler
public class StringArgumentMarshaler implements ArgumentMarshaler {
	private String stringValue = false;

	public void set(Iterator<String> currentArgument) throws ArgsException {
		try {
			stringValue = currentArgument.next();
		} catch (NoSuchElementException e) {
			throw new ArgsException(MISSING_STRING);
		}
	}
	
	public static boolean getValue(ArgumentMalshaler am) {
		if (am != null && am instanceof StringArgumentMarshaler)
			return ((StringArgumentMarshaler) am).stringValue;
		else
			return "";
	}
}
```

- 나머지 ArgumentMarshaler의 파생 클래스들은 생략
- 오류 코드 상수를 정의하는 코드 정의 부분은 어떻게 생겼을까?

```java
// 목록 14-7 ArgsException.java

public class ArgsException extends Exception {
    private char errorArgumentId = '\0';
    private String errorParameter = null;
    private ErrorCode errorCode = OK;
		
		ArgsException 기본 생성자, String형 message를 인자로 받는 생성자 작성
		
    
    public ArgsException(ErrorCode errorCode) {
        this.errorCode = errorCode;
    }

    public ArgsException(ErrorCode errorCode, String errorParameter) {
        this.errorCode = errorCode;
        this.errorParameter = errorParameter;
    }

    public ArgsException(ErrorCode errorCode, char errorArgumentId, String errorParameter) {
        this.errorCode = errorCode;
        this.errorArgumentId = errorArgumentId;
        this.errorParameter = errorParameter;
    }

    public char getErrorArgumentId() {
        return errorArgumentId;
    }

		public void setErrorArgumentId(char errorArgumentId) {
        this.errorArgumentId = errorArgumentId;
    }

    public String getErrorParameter() {
        return errorParameter;
    }

		public void setErrorParameter(String errorParameter) {
        this.errorParameter = errorParameter;
    }

    public ErrorCode getErrorCode() {
        return errorCode;
    }
    
    public void setErrorCode(ErrorCode errorCode) {
        this.errorCode = errorCode;
    }

		public String errorMessage() {
			switch (errorCode) {
				case OK:
					return "TILT : Should not get here.";
				case UNEXPECTED_ARGUMENT:
					return String.format("Argument -%c unexpected.", errorArgumentId);

				... 생략 ....

			}

    public enum ErrorCode {
        OK, MISSING_STRING, MISSING_INTEGER, INVALID_INTEGER, UNEXPECTED_ARGUMENT, .. 생략 ...
    }
  
}

```

- 새로운 인수 유형을 추가하는 방법이 명백하다.
- ArgumentMarshaler에서 새 클래스를 파생해 getXXX 함수를 추가한 후 parseSchemaElement 함수에 새 case문만 추가하면 끝이다.
- 필요하다면 ArgsException.ErrorCode를 만들고 새 오류 메시지를 추가한다.

### 어떻게 짰느냐고?

- 프로그래밍은 과학보다 공예에 가깝다.
    - 깨끗한 코드를 짜려면 지저분한 코드를 짠 뒤에 정리해야 한다는 의미다.
- TDD 기법을 이용
    - TDD는 언제 어느 때라도 시스템이 돌아가야 한다는 원칙을 따른다.
    - 변경을 가한 후에도 시스템이 변경 전과 똑같이 돌아가야 한다는 말이다.
- 단위 테스트 슈트와 인수 테스트를 만들어놓고 시스템에 자잘한 변경을 가하기 시작했다.

## 📌 결론

- 나쁜 코드보다 더 오랫동안 심각하게 개발 프로젝트에 악영향을 미치는 요인도 없다.
- 나쁜 코드를 깨끗한 코드로 개선하는 것보다 처음부터 코드를 깨끗하게 유지하는 게 비용이 훨씬 적다.
    
    > 더욱이 5분 전에 엉망으로 만든 코드는 지금 당장 정리하기 아주 쉽다.
    > 
- 코드는 언제나 최대한 깔끔하고 단순하게 정리하자. 절대로 썩어가게 방치하면 안된다.
