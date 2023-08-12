# 5장 : 형식 맞추기
> 프로그래머라면 형식을 깔끔하게 맞춰 코드를 짜야 한다. 코드 형식을 맞추기 위한 간단한 규칙을 정하고 그 규칙을 착실히 따라야 한다.
> 

## 📌 형식을 맞추는 목적

### 코드 형식의 중요성

- 코드 형식은 중요하다!

    - 너무나도 중요하므로 융통성 없이 맹목적으로 따르면 안 된다.


    - 코드 형식은 의사소통의 일환이다.

### 왜 중요한가?

- 오늘 구현한 기능이 다음 버전에서 바뀔 확률은 아주 높다.


    - 오늘 구현한 코드의 가독성은 앞으로 바뀔 코드의 품질에 지대한 영향을 미친다.


- 오랜 시간이 지나 원래 코드의 흔적이 사라지더라도


    - 맨 처음 잡아놓은 구현 스타일과 가독성 수준은 유지보수 용이성과 확장성에 영향

## 📌 적절한 행 길이를 유지하라

### 소스코드 길이는 얼마나 길어야 적당할까?

(자바에서 파일 크기는 클래스 크기와 밀접)

- 대다수 자바 소스 파일은 크기가 어느 정도일까?
    
    ![](https://blog.kakaocdn.net/dn/bUT1v1/btq2tmuFutm/eVKfKKIBnGoPMwhTRgKsK0/img.png)
    
    - 상자를 관통하는 선은 각 프로젝트에서 최대 파일 길이와 최소 길이 파일


    - 상자는 대략 파일의 1/3을 차지한다. 상자 중간이 평균이다.
    - 분석
        - 평균 파일 크기는 65줄


        - 전체 파일 중 대략 1/3이 40-100줄
        - 가장 긴 파일은 약 400줄, 가장 짧은 파일은 6줄
- 결론
    - 500줄을 넘지 않고 대부분 200줄 정도인 파일로도 커다란 시스템을 구축할 수 있다.


    - 일반적으로 큰 파일보다 작은 파일이 이해하기 쉬우므로 이걸 바람직한 규칙으로 삼으면 좋겠다.

### 신문 기사처럼 작성하라 🗞️

- 신문의 특징
    - 독자는 최상단 표제를 보고서 기사를 읽을지 말지 결정


    - 첫 문단은 전체 기사 내용을 요약
    - 쭉 읽으며 내려가면 세세한 사실이 조금씩 드러남.
    - 날짜, 이름, 발언, 주장, 기타 세부사항이 나온다.
- 소스파일에 적용
    - 이름은 간단하면서 설명이 가능하게


        - 이름을 보고도 올바른 모듈을 살펴보고 있는지 아닌지를 판단할 정도로 신경 써서 짓는다.
    - 소스코드 첫 부분은 고차원 개념과 알고리즘 설명
    - 아래로 내려갈수록 의도를 세세하게 묘사
    - **마지막에는 가장 저차원 함수와 세부 내역이 나옴.**

> 한 면을 채우는 기사는 거의 없다.

### 개념은 빈 행으로 분리해라

- 생각 사이에는 빈 행을 분리해야 마땅하다.
- 개념을 빈 행으로 구분한 좋은 예
    
    ```java
    package fitnesse.wikitext.widgets;
    
    import java.util.regex.*;
    
    public class BoldWidget extends ParentWidget {
    		public static final String REGEXP = "'''.+?'''";
    		private static final Pattern pattern = Pattern.compile("'''(.+?)'''",
    			Pattern.MULTILINE + Pattern.DOTALL
    	);
    	
    	public BoldWidget(ParentWidget parent, String text) throws Exception {
    		super(parent);
    			Matcher match = pattern.matcher(text);
    			match.find();
    			addChildWidgets(match.group(1));
    	}
    	
    	public String render() throws Exception {
    		StringBuffer html = new StringBuffer("<b>");
    		html.append(childHtml()).append("<b>");
    		return html.toString();
    	}
    }
    ```
    
    - 패키지 선언부, import 문, 각 함수 사이에 빈 행이 들어감.
- 빈 행은 새로운 개념을 시작한다는 시각적 단서

### 세로 밀집도

- 줄바꿈이 개념을 분리한다면 세로 밀집도는 연관성을 의미함.
- 서로 밀집한 코드의 행은 세로로 가까이 놓여야 함.
- 나쁜 예
    
    ```java
    public class ReporterConfig {
    	/**
    	* 리포터 리스너의 클래스 이름
    	*/
    	private String m_className;
    	
    	/**
    	* 리포터 리스너의 속성
    	*/
    	private List<Property> m_properties = new ArrayList<Property>();
    	public void addProperty(Property property) {
    		m_properties.add(property);
    	}
    }
    ```
    
    - 의미 없는 주석으로 두 인스턴스 변수를 떨어뜨려 놓음.
  
- 개선한 예
    
    ```java
    public class ReporterConfig {
    	private String m_className;
    	private List<Property> m_properties = new ArrayList<Property>();
    
    	public void addProperty(Property property) {
    		m_properties.add(property);
    	}
    }
    ```
    
    - 코드가 **한 눈에** 들어옴.


    - 변수 두 개에 메서드 하나인 클래스라는 사실이 드러남.

### 수직 거리

> 함수 연관 관계와 동작 방식을 이해하려고 이 함수에서 저 함수로 오가며 소스 파일을 위아래로 뒤지는 등 뺑뺑이를 돌았으나 결국은 미로 같은 코드 때문에 혼란만 가증된 경험이 있는가?
> 
- 서로 밀접한 개념은 세로로 가까이 둬야 한다.
- 하지만 타당한 근거가 없다면 서로 밀접한 개념은 한 파일에 속해야 마땅하다.


    - **이게 바로 protected 변수를 피해야 하는 이유**


- 같은 파일에 속할 정도로 밀접한 두 개념은 세로 거리로 연관성 표현
    - 연관성 : 한 개념을 이해하는 데 다른 개념이 중요한 정도


    - 연관성이 깊은 두 개념이 멀리 떨어져 있으면 코드를 읽는 사람이 소스 파일과 클래스를 여기저기 뒤지게 됨.

**변수선언**

- 변수는 사용하는 위치에 최대한 가까이 선언한다. (?? 자바에서만인가?
- 우리가 만든 함수는 짧음 → 지역 변수는 각 함수 맨 처음에 선언
- 예시1
    
    ```java
    private static void readPreferences() {
    	InputStream is= null;
    	try {
    		is= new FileInputStream(getPreferencesFile());
    		setPreferences(new Properties(getPreferences()));
    		getPreferences().load(is);
    	} catch (IOException e) {
    		try {
    			if(is != null)
    			is.close();
    		} catch(IOException i1) {
    		}
    	}
    }
    ```
    
- 예시2
    - 목표를 제어하는 변수는 흔히 루프문 내부에 선언
    
    ```java
    public int countTestCases() {
    	int count= 0;
    	for(Test each : tests)
    		count += each.countTestCases();
    	return count;
    }
    ```
    
- 예시3
    - 블록 상단이나 루프 직전에 변수 선언

**인스턴스 변수**

- 클래스 맨 처음에 선언
- 변수간 세로로 거리를 두지 않는다.
- 인스턴스 변수 선언 위치는 논쟁 분분
    - C++은 인스턴스 변수를 클래스 마지막에 선언
    - 자바는 보통 클래스 맨 처음에 인스턴스 변수 선언
- 인스턴스 변수 선언이 꽁꽁 숨겨진 예시(나쁜 예)
    
    ```java
    public class TestSuite implements Test {
    	static public Test createTest(Class<? extends TestCase> theClass,
    																String nmae) {
    		...
    	}
    	
    	public static Constructor<? extends TestCase>
    	getTestConstructor(Class<? extends TestCase> theClass)
    	throws NoSuchMethodException {
    		...
    	}
    	
    	public static Test warning(final String message) {
    		...
    	}
    	
    	private static String excpetionToString(Throwable t) {
    		...
    	}
    	
    	private String fName;
    	private Vector<Text> fTests = new Vector<Text>(10);
    	
    	public TestSuite() {
    	}
    	
    	public TestSuite(final Class<? extends TestCase> theClass) {
    		...
    	}
    	
    	public TestSuite(Class<? extends TestCase> theClass, String name) {
    		...
    	}
    	... ...  
    }
    ```
    

**종속 함수**

- 한 함수가 다른 함수를 호출한다면 → 두 함수 세로로 가까이 배치
- 가능하다면 호출하는 함수를 호출되는 함수보다 먼저 배치
    
    ⇒ 프로그램이 자연스럽게 읽힘.
    
- 규칙을 일관적으로 적용하면 예측 가능

```java
public class WikipageResponder implements SecureResponder {
	protected WikiPage page;
	protected PageData pageData;
	protected String PateTitle;
	protected Request request;
	protected PageCrawler crawler;

	public Response makeResponse(FitNesseContext content, Request request)
		throws Exception {
		String pageName = getPageNameOrDefault(request, "FrontPage");
		loadPage(pageName, context);
		if (page == null)
			return notFoundResponse(context, request);
		else
			return makePageResponse(context);

	private String getPageNameOrDefault(Request request, String defaultPageName)
	{
		String pageName = request.getResource();
		if (StringUtil.isBlank(pageName))
			pageName = defaultPageName;
		
		return pageName;
	}

	protected void loadPage(String resource, FitNesseContext context)
		throws Exception {
		WikiPagePath path = PathParser.parse(resource);
		crawler = context.root.getPageCrawler();
		// 생략
	
	private Response notFoundResponse(FitNesseContext context, Request request)
		throws Exception {
		return new NotFoundResponder().makeResponse(context, request);
	}

	private SimpleResponse makePageResponse(FitNesseContext context)
		throws Exception {
		pageTitle = PathParser.render(crawler.getFullPath(page));
		String html = makeHtml(context);

		SimpleResponse response = new SimpleResponse();
		response.setMaxAge(0);
		response.setContent(html);
		return response;
	}
```

**개념의 유사성**

- 개념적인 친화도가 높을수록 코드를 가까이 배치한다.
- 친화도가 높은 요인
    - 한 함수가 다른 홈수를 호출해 생기는 직접적인 종속성
    - 변수와 그 변수를 사용하는 함수


    - **비슷한 동작을 수행하는 일군의 함수**
    
    ```java
    public class Assert {
    	static public void assertTrue(String message, boolean condition) {
    		if (!condition)
    			fail(message);
    	}
    
    	static public void assertTrue(boolean condition) {
    		assertTrue(null, condition);
    	}
    
    	static public void assertFalse(String message, boolean condition) {
    		assertTrue(message, !condition);
    	}
    
    	static public void assertFalse(boolean condition) {
    		assertFalse(null, condition);
    	}
    
    ...
    ```
    
    - 위 세 함수들은 개념적인 친화도 높음


        - 명명법이 똑같고 기본 기능이 유사하고 간단


    - 종속적인 관계가 없더라도 가까이 배치할 함수들

### **세로 순서**

- 일반적으로 `호출되는 함수`를 `호출하는 함수`보다 나중에 배치
    
    ⇒ 소스코드 모듈이 고차원에서 저차원으로 자연스럽게 내려감. 
    
- like 신문기사 🗞️
    - 중요한 개념을 가장 먼저 표현
    - 가장 중요한 개념을 표현할 때에는 세세한 사항 최대한 배제

## 📌 **가로 형식 맞추기**

- 한 행은 가로로 얼마나 길어야 적당할까????? (넘나 궁금
- 일반적인 프로그램에서 행 길이
    
    https://oopy.lazyrockets.com/api/v2/notion/image?src=https://s3-us-west-2.amazonaws.com/secure.notion-static.com/76b1e629-5c30-43c4-af52-80eacdcf7242/Untitled.png&blockId=31a3e6d5-dcae-4fb7-bb64-08134ac91cd7
    
    - 규칙적임.
    - 45자 근처가 제일 규칙적
    - 20-60자 사이는 모든 값이 총 행 수의 1%
        - __20자-60자 사이인 행이 총 행수의 총 행수의 40%에 달함.
    - 80자 이후부터는 행 수 급격히 감소
        
        **⇒ 프로그래머는 명백하게 짧은 행을 선호한다.**
        
- 저자 개인적으로는 120자 정도로 행 길이 제한

### **가로 공백과 밀집도**

- 가로로는 공백을 사용해 밀접한 개념과 느슨한 개념을 표현함.
- 공백 활용 예시
    
    ```java
    private void measuerLine(String line) {
    	lineCount++;
    	int lineSize = line.length();
    	totalChars += lineSize;
    	lineWidthHistogram.addLine(lineSize, lineCount);
    	recordWidestLine(lineSize);
    }
    ```
    
    - 할당 연산자 사이의 공백 → 왼쪽 요소와 오른쪽 요소가 분명히 나뉨.
    - 함수 이름과 이어지는 괄호 사이에는 공백 X → 함수와 인수는 서로 밀접함.
    - 공백을 넣으면 한 개념이 아니라 별개로 보임.
        - 함수 호출 코드에서 괄호 안 인수는 공백으로 분리
- 공백 활용 예시2
    
    연산자 우선순위를 강조하기 위해서도 공백 사용
    
    ```java
    public class Quadratic {
    	public static double root1(double a, double b, double c) {
    		double determinant = derterminant(a, b, c);
    		return (-b + Math.sqrt(determinant)) / (2*a);
    	}
    	
    	public static double root2(int a, int b, int c) {
    		double determinant = derterminant(a, b, c);
    		return (-b + Math.sqrt(determinant)) / (2*a);
    	}
    	private static double determinant(double a, double b, double c) {
    		return b*b - 4*a*c;
    	}
    ```
    
    - 승수 사이에는 공백이 없음. (곱셈이 가장 우선순위가 높기 때문)
    - 항 사이에는 공백이 들어감. (덧셈과 뺄셈은 우선순위가 곱셈보다 낮기 때문)
    
    (오 곱셈에도 무조건 띄어쓰기 했는데 이게 더 낫구나)
    
    - 근데 IDE에서 공백 없애는 경우가 흔함.

### **가로 정렬**

- 가로정렬이 뭐냐면 대충 이런거임
    
    ```java
    public class FitNesseExpediter implements ResponseSender {
        private     Socket         socket;
        private     InputStream    input;
        private     OutputStream   output;
        private     Reques         request;      
        private     Response       response; 
        private     FitNesseContex context; 
        protected   long           requestParsingTimeLimit;
        private     long           requestProgress;
        private     long           requestParsingDeadline;
        private     boolean        hasError;
    
    	public FitNesseExpediter(Socket          s,
    							 FitNesseContext context) throws Exception
    	{
    		this.context =            context;
    		socket =                  s;
    		input =                   s.getInputStream();
    		output =                  s.getOutputStream();
    		requestParsingTimeLimit = 10000;
    	}
    ```
    
- 가로정렬의 문제점
    - 코드의 엉뚱한 부분 강조 → 진짜 의도가 가려짐.
        - ex) 변수 유형은 무시하고 변수 이름부터 읽게 됨.
        - ex) 할당 연산자는 보이지 않고 오른쪽 피연산자에 눈이 감.
    - IDE는 대다수가 위와 같은 정렬을 무시함.
- 이제는 선언문과 할당문을 별도로 정렬하지 않음.
    - 정렬하지 않으면 오히려 중대한 결함을 찾기 쉬움.
- 정렬이 필요할 정도로 목록이 길다면?
    - 문제는 목록 **길이**지 정렬 부족이 아님.

### **들여쓰기**

- 우리는 계층을 표현하기 위해 코드를 들여씀.
- 프로그래머는 이런 들여쓰기 체계에 크게 의존

**들여쓰기 무시하기**

- 때로는 간단한 if문, while문, 짧은 함수에서 들여쓰기 규칙을 무시하고픈 유혹이 생김. (맞아!!!!!
- 저자는 항상 원점으로 돌아가 들여쓰기 넣음.
- 나쁜 예
    
    다음과 같이 한 행에 범위를 뭉뚱그린 코드는 피함.
    
    ```java
    public class CommentWidget extends TextWidget {
        public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";
    
        public CommentWidget(ParentWidget parent, String text){super(parent, text);}
        public String render() throws Exception {return ""; } 
    }
    ```
    
- 좋은 예
    
    ```java
    public class CommentWidget extends TextWidget {
        public static final String REGEXP = "^#[^\r\n]*(?:(?:\r\n)|\n|\r)?";
    
        public CommentWidget(ParentWidget parent, String text) {
    			super(parent, text);
    		}
    
        public String render() throws Exception {
    			return "";
    		}
    }
    ```
    

### 가짜 범위

- 저자는 빈 while문이나 for문 구조 선호 Xx
- 피하지 못할 때는 빈 블록을 올바로 들여쓰고 괄호로 감쌈.
- while 문 끝에 세미콜론(;) 하나를 살짝 덧붙인 코드로 수없이 골탕먹은 저자,,
- 세미콜론은 새 행에다가 제대로 들여써서 넣어줘야 눈에 띔.
    
    ```java
    while (dis.read(buf, 0, readBufferSize) != -1)
    ;
    ```
    

## 📌 팀 규칙

> 개개인이 따로국밥처럼 맘대로 짜대는 코드는 피해야 함.


![](https://mp-seoul-image-production-s3.mangoplate.com/597979_1629857347523897.jpg?fit=around|738:738&crop=738:738;*,*&output-format=jpg&output-quality=80)

- 팀은 한 가지 규칙에 합의해야 함. 그리고 모든 팀원은 그 규칙을 따라야 함.
- 어디에 괄호를 넣을지, 들여쓰기는 몇 자로 할지, 클래스와 변수와 메서드 이름은 어떻게 지을지 등등 결정
    - 우리가 정한 규칙으로 IDE 코드 형식기를 설정한 후 지금까지 사용
- 스타일은 일관적이고 매끄러워야 함.
- 한 소스파일에서 봤던 형식이 다른 소스파일에도 쓰이리라는 신뢰감을 독자에게 줘야 함.

## 📌 밥 아저씨의 형식 규칙

![](https://mblogthumb-phinf.pstatic.net/20160526_126/emo-art_1464269073322MHPQj_JPEG/zLNFIBtisESk634049407784855842.jpg?type=w800)