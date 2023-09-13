# 4장 : 주석
> 나쁜 코드에 주석을 달지 마라. 새로 짜라.
> 
- 경솔하고 근거 없는 주석은 코드를 이해하기 어렵게 만듦.
- 주석은 ‘순수하게 선하지’ 못하다.
- 우리는 코드로 의도를 표현하지 못해, 실패를 만회하기 위해 주석을 사용한다.
    
    > 주석은 언제나 실패를 의미한다.
    > 
- 주석이 필요한 상황에 처하면 상황을 역전해 코드로 의도를 표현할 방법은 없을지 생각해라.
- 주석은 오래될수록 그릇될 가능성도 커짐
    - 코드는 변화하기 때문에 코드 조각 일부가 여기저기로 옮겨짐.
        - 나뉘고 갈라지고 합쳐지면 → 괴물😈이 됨.
- 예를 들어 이런 경우
    
    ```java
    MockRequest request;
    private final String HTTP_DATE_REGEXP =
    	"[SMTWF][a-z]{2}\\,\\s[0-9]{2}\\s[JFMASOND][a-z]{2}\\s"+
    	"[0-9]{4}\\s[0-9]{2}\\:[0-9]{2}\\sGMT";
    private Response response;
    private FitNessContext context;
    private FileResponsder responder;
    private Locale saveLocale;
    // Example : "Tue, 02 Apr 2003 22:18:49 GMT"
    ```
    
    - `HTTP_DATE_REGEXP` 상수와 주석 사이에 다른 인스턴스 변수가 추가된 상황

> 부정확한 주석은 아예 없는 주석보다 훨씬 더 나쁘다.
> 

> 진실은 한 곳에만 존재한다. 바로 코드다.
> 

## 📌 주석은 나쁜 코드를 보완하지 못한다

- 표현력이 풍부하고 깔끔하며 주석이 거의 없는 코드가, 복잡하고 어수선하며 주석이 많이 달린 코드보다 훨씬 좋다.

> 자신이 저지른 난장판을 주석으로 설명하려 애쓰는 대신에 그 난장판을 깨끗이 치우는 데 시간을 보내라!
> 

## 📌 코드로 의도를 표현하라!

- 코드만으로 의도를 설명하기 어려운 경우도 존재함.
- 그렇다고 코드가 훌륭한 수단이 아니라는 의미 Xx
- 나쁜 예시
    
    ```java
    // 직원에게 복지 혜택을 받을 자격이 있는지 검사한다.
    if((employee.flags & HOURLY_FLAG) &&
    	(employee.age > 65))
    ```
    
- 좋은 예시
    
    ```java
    if(employee.isEligibleForFullBenefits())
    ```
    
    - 몇 초만 생각하면 코드로 대다수 의도를 표현 가능

## 📌 좋은 주석

- 어떤 주석은 필요하거나 유익함.
- 명심) 정말로 좋은 주석은, 주석을 달지 않을 방법을 찾아낸 주석임.

### 법적인 주석
## 📌 좋은 주석

- 어떤 주석은 필요하거나 유익함.
- 명심) 정말로 좋은 주석은, 주석을 달지 않을 방법을 찾아낸 주석임.

### 법적인 주석

- 예시 : FitNess에서 모든 소스파일 첫머리에 추가한 표준 주석 헤더
    - (요즘 IDE는 주석 헤더 자동으로 축소해 코드만 표시함.)
    
    ```java
    // Copyright (C) 2003,2004,2005 by Object Mentor, Inc. All right reserved.
    // GNU General License 버전 2 이상을 따르는 조건으로 배포한다.
    ```
    
- 꼭 첫 머리에 들어가는 주석이 계약 조건이나 법적인 정보일 필요 X

### 정보를 제공하는 주석

- 때로는 **기본적인 정보**를 주석으로 제공하면 편리하다.
- 예시1  : 추상 메서드가 반환할 값을 설명
    
    ```java
    // 테스트 중인 Responder 인스턴스를 반환한다.
    protected abstract Responder responderInstance();
    ```
    
    - 더 개선한 예시 : 가능하면 함수 이름에 정보를 담는 편이 더 좋음.
        
        ```java
        protected abstract Responder responderBeingTested();
        ```
        
- 예시2 : 코드에서 사용한 정규표현식이 시각과 날짜를 뜻한다고 설명
    
    ```java
    // kk:mm:ss EEE, MMM dd, yyyy 형식이다.
    Pattern timeMatcher = Pattern.complie(
    	"\\d*:\\d* \\w*, \\w*, \\d*, \\d*");
    ```
    
    - 이왕이면 시각과 날짜를 변환하는 클래스를 만들어 코드를 옮겨주면 더 좋고 깔끔하겠다.

### 의도를 설명하는 주석

**때때로 주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명한다.**

- 흥미로운 예제
    
    두 객체를 비교할 때 저자는 다른 어떤 객체보다 **자기 객체에 높은 순위를 주기로 결정**했다.
    
    - 원래 예시
        
        ```java
        public int compareTo(Object o)
        {
        	if(o instanceof WikipagePath)
        	{
        		WikiPagePath p = (WikiPagePath) o;
        		String compressedName = StringUtil.join(names, "");
        		String compressedArgumentName = StringUtil.join(p.names, "");
        		return compressedName.compareTo(compressedArgumentName);
        	}
        	return 1; // 오른쪽 유형이므로 정렬 순위가 더 높다.
        }
        ```
        
    - 더 나은 예시
        
        ```java
        public void testConcurrendAddWidgets() throws Exception {
        	WidgetBuilder widgetBuilder = 
        		new WidgetBuilder(new Class[]{BoldWidget.class});
        	String text = "'''bold text'''";
        	parentWidget parent =
        		new BoldWidget(new MockWidgetRoot(), "'''bold text'''");
        	AtomicBoolean failFlag = new AtomicBoolean();
        	failFlag.set(false);
        	
        	// 스레드를 대량 생선하는 방법으로 어떻게든 경쟁 조건을 만들려 시도한다.
        	for(int i = 0; i < 25000; i++) {
        		WidgetBuilderThread widgetBuilderThread =
        			new WidgetBuilderThread(widgetBuilder, text, parent, failFlag);
        		Thread thread = new Thread(widgetBuilderThread);
        		thread.start();
        	}
        	assertEquals(false, failFlag.get());
        }
        ```
        
        (이해가 안감…. 둘이 의도가 좀 다른 것 같은데…………)
        

### 의미를 명료하게 밝히는 주석

**때때로 모호한 인수나 반환값은 그 의미를 읽기 좋게 표현하면 이해하기 쉬워진다.**

인수나 반환값이 표준 라이브러리나 변경하지 못하는 코드에 속한다면 으미를 명료하게 밝히는 주석이 유용

- 예시
    
    ```java
    public void testCompareTo() throws Exception
    {
    	WikipagePath a = PathParser.parse("PageA");
    	WikipagePath ab = PathParser.parse("PageA.PageB");
    	WikipagePath b = PathParser.parse("PageB");
    	WikipagePath aa = PathParser.parse("PageA.PageA");
    	WikipagePath bb = PathParser.parse("PageB.PageB");
    	WikipagePath ba = PathParser.parse("PageB.PageA");
    
    	assertTrue(a.compareTo(a) == 0); // a == a
    	assertTrue(a.compareTo(b) != 0); // a != b
    	assertTrue(ab.compareTo(ab) == 0); // ab == ab
    	assertTrue(a.compareTo(b) == -1); // a < b
    	assertTrue(aa.compareTo(ab) == 0); // aa == ab
    	...
    }
    ```
    
- But 의미를 명료히 밝히는 주석이 위험한 이유
    - 그릇된 주석을 달아놓을 위험 상당히 높음.
  
    - 주석이 올바른지 검증 어려움.
- 위와 같은 주석을 달 때에는 더 나은 방법이 없는지 고민하고 각별히 주의

### 결과를 경고하는 주석

다른 프로그래머에게 결과를 경고할 목적으로 주석 사용하기도 함.

- 예시
    
    특정 테스트 케이스를 꺼야하는 이유를 설명하는 주석
    
    ```java
    // 여유 시간이 충분하지 않다면 실행하지 마십시오.
    public void _testWithReallyBigFile()
    {
    	writeLinesToFile(10000000);
    	
    	response.setBody(testFile);
    	response.readyToSend(this);
    	String responseString = output.toString();
    	assertSubString("Content-Length: 1000000000", responseString);
    	assertTrue(byTesSent > 1000000000);
    }
    ```
    
    - 물론 요즘에는 `@Ignore` 속성을 이용해 테스트 케이스를 꺼버림.
  
    - 구체적인 설명은 `@Ignore` 속성에 문자열로 넣어줌.
        - `@Ignore("실행이 너무 오래 걸린다.")`
  
    - 더 적절한 코드
    
    ```java
    public static SimpleDateFormat makeStandardHttpDateFormat()
    {
    	// SimpleDateFormat은 스레드에 안전하지 못하다.
    	// 따라서 각 인스턴스를 독립적으로 생성해야 한다.
    	SimpleDateformat df = new SimpleDateFormat("EEE, dd MMM yyyy HH:mm:ss z");
    	df.setTimeXone(TimeZone.getTimeZone("GMT"));
    	return df;
    }
    ```
    
    - 정적 초기화 함수를 사용하려던 열성적인 프로그래머가 주석 때문에 실수를 면할 수 있는 사례

### TODO 주석

‘앞으로 할 일’을 //TODO 주석으로 남겨두면 편할 때도 있음.

```java
// TODO-MdM 현재 필요하지 않다.
// 체크아웃 모델을 도입하면 함수가 필요 없다.
protected VersionInfo makeVersion() throws Exception
{
	return null;
}
```

- **TODO 주석이 유용할 때는?**
    - 더 이상 필요 없는 기능을 삭제하라는 알림
    - 누군가에게 문제를 봐달라는 요청


    - 더 좋은 이름을 떠올려달라는 부탁
    - 앞으로 발생할 이벤트에 맞춰 코드를 고치라는 주의
- 단, 나쁜 코드를 나마겨놓는 핑계가 되어서는 안됨.


- 주기적으로 TODO 주석을 점검해 없애도 괜찮은 주석은 없애자.

### 중요성을 강조하는 주석

**자칫 대수롭지 않다고 여겨질 뭔가의 중요성 강조**

```java
String listItemContent = match.group(3).trim();
// 여기서 trim()은 정말 중요하다. trim 함수는 문자열에서 시작 공백을 제거한다
// 문자열에 시작 공백이 있으면 다른 문자열로 인식되기 때문이다.
new ListItemWidget(this, listItemContent, this.level + 1);
return buildList(text.substring(match.end());
```

### 공개 API에서 Javadocs

- 설명이 잘된 공개 API는 참으로 유용
    - Javadocs가 좋은 예
  
- 공개 API를 구현한다면 반드시 훌륭한 Javadocs를 작성한다.
    - 하지만 여느 주석과 마찬가지로 Javadocs 역시 잘못된 정보 전달 가능성 O

## 📌 나쁜 주석

대다수 주석이 이 범주에 속함.

### 주절거리는 주석

주절주절주절주절

- 특별한 이유 없이 의무감으로 마지못해 주석을 단다면 시간낭비!
    
    > 주석을 달기로 결정했다면 충분한 시간을 들여 최고의 주석을 달도록 노력한다.
    > 
- 주절주절주석 예시
    ```java
    public void loadProperties()
    {
        try
        {
            String propertiesPath = propertiesLocation + "/" + PROPERTIES_FILE;
            FileInputStream propertiesStream = new FileInputStream(propertiesPath);
            loadedProperties.load(propertiesStream);
        }
        catch(IOExeption e)
        {
            // 속성 파일이 없다면 기본값을 모두 메모리로 읽어 들였다는 의미다.
        }
    }
    ```

뭔 뜻일까?

- `IOException`이 발생하면 속성 파일이 없다 → 기본값을 메모리로 읽어들인 상태

- 근데 누가 기본값을 메모리로 읽어들인건지 알 수 없음.
    - `loadProperties.load`를 호출하기 전?
    - `loadProperties.load`가 예외를 잡아 기본값을 읽어들인 후 예외를 던져주나?
  
    - `loadProperties.load`가 파일을 읽어들이기 전에 모든 기본값부터 읽어들이나?
    - 그냥 catch 블록 비워놓기 뭐해서 몇 마디 끄적인 거?
- 답을 알아내려면 다른 코드를 뒤져보는 수 밖에 없는 코드 → 독자와 제대로 소통하지 못하는 주석임.

### 같은 이야기를 중복하는 주석

- 주석이 코드보다 더 많은 정보를 제공하지 못하는 예시
    - 헤더에 달린 주석이 같은 코드 내용을 그대로 중복한다.
        
        ```java
        // this.closed가 true일 때 반환되는 유틸리티 메서드다.
        // 타임아웃에 도달하면 예외를 던진다.
        public synchronized void waitForClose(final long timeoutMillis)
        throws Exception
        {
        	if(!closed)
        	{
        		wait(timeoutMillis);
        		if(!closed)
        			throw new Exception("MockResponseSender could not be closed");
        	}
        }
        ```
        
- 쓸모없고 중복된 Javadocs가 많은 예시(Tomcat에서 가져온 코드)
    
    ```java
    public abstract class ContainerBase
    	implements Container, Lifecycle, Pipeline,
    	MBeanRegistration, Serializable {
    	
    	/** 
    	* 이 컨포넌트의 프로세서 지연값
    	*/
    	protected int backgroundProcessorDelay = -1;
    	
    	/** 
    	* 이 컨포넌트를 지원하기 위한 생명주기 이벤트
    	*/
    	protected LifecycleSupport lifecycle =
    		new LifecycleSupport(this);
    
    	/** 
    	* 이 컨포넌트를 위한 컨테이너 이벤트 Listener
    	*/
    	protected ArrayList listeners = new ArrayList();
    
    	// 뒤에 이런 식으로 변수 선언 겁나 많이 하고 주석은 다 달음.
    ```
    
    - 코드만 지저분하고 정신 없게 만듦.

### 오해할 여지가 있는 주석

- 이 코드 다시 보자. 오해할 여지가 있음.
    
    ```java
    // this.closed가 true일 때 반환되는 유틸리티 메서드다.
    // 타임아웃에 도달하면 예외를 던진다.
    public synchronized void waitForClose(final long timeoutMillis)
    throws Exception
    {
    	if(!closed)
    	{
    		wait(timeoutMillis);
    		if(!closed)
    			throw new Exception("MockResponseSender could not be closed");
    	}
    }
    ```
    
    - this.closed가 true로 변하는 순간에 메서드는 반한되지 않는다.
  
    - this.closed가 true**여야** 메서드는 반환된다.
        - 아니면 무조건 타임아웃을 기다렸다 this.closed가 그래도 true가 **아니면** 예외를 던진다.
    - (이게 무슨 말장난…? 아 그러니까 this.closed가 true로 변하는 그 찰나가 아니라, timeoutMillis 만큼 다~~ 기다렸다가 true인 거 검사되면 그 때 반환을 하네
  
- 이렇게 코드 짜면 true로 변하는 순간! 함수가 반환되는 줄 알고 경솔하게 함수 호출할 수도 있음.

### 의무적으로 다는 주석

- 모든 함수에 Javadocs를 달거나 모든 변수에 주석을 달아야 한다는 규칙은 어리석기 그지 없음.
- 괴물 코드
    
    ```java
    /**
    	*
    	* @param title CD 제목
    	* @param author CD 저자
    	* @param tracks CD 트랙 숫자
    	* @param durationInMinutes CD 길이(단위 : 분)
    	*/
    public void addCD(String title, String author, 
    									int tracks, int durationInMinutes) {
    	CD cd = new CD();
    	cd.title = title;
    	cd.author = author;
    	cd.tracks = tracks;
    	cd.duration = durationInMinutes;
    	cdList.add(cd);
    }
    ```
    

### 이력을 이록하는 주석

- 모듈을 편집할 때마다 모듈 첫 머리에 주석 추가할 때도 있음.

- 그럼 모듈 첫 머리 주석은 지금까지 모듈에 가한 변경을 모두 기록하는 일종의 일지 혹은 로그가 된다.
- 예전에는 모든 모듈 첫머리에 변경 이력을 기록하고 관리하는 관계가 바람직했음.(소스코드 관리 시스테밍 없었으니까)
- 이제는 혼란만 가중할 뿐.

### 있으나 마나 한 주석

너무 당연한 사실을 언급하며 새로운 정보를 제공하지 못하는 주석

```java
/**
	* 기본 생성자
	*/
protected AnnualDateRule() {
}
```

```java
/** 월 중 일자 */
private int dayOfMonth;
```

```java
/**
	* 월 중 일자를 반환한다.
	*
	* @return 월 중 일자
	*/
public int getDayOfMonth() {
	return dayOfMonth;
}
```

- 위와 같은 주석은 지나친 참견이라 개발자가 주석을 무시하는 습관에 빠짐
    - 결국은 코드가 바뀌면서 주석은 거짓말로 변한다.
- 다음 예시를 보자.
    - 첫 번째 주석은 적절해 보임.
    - 두 번째 주석은 전혀 쓸모가 없음.
    
    ```java
    private void startSending()
    {
    	try
    	{
    		doSending();
    	}
    	catch(SocketException e)
    	{
    		// 정상. 누군가 요청을 멈췄다.
    	}
    	catch(Exception e)
    	{
    		try
    		{
    			response.add(ErrorResponser.makeExceptionString(e));
    		}
    		catch(Exception e1)
    		{
    			// 이게 뭐야!
    		}
    	}
    }
    ```
    
    - try/catch 블록을 짜다 너무 짜증났나 봄.
    - 있으나 마나 한 주석으로 분풀이하는 대신 프로그래머가 코드 구조를 개선했더라면 짜증낼 필요가 없었을 것…
- 다음과 같이 try/catch 블록을 독자적인 함수로 만드는 데 노력을 쏟았어야 했음.
    
    ```java
    private void startSending()
    {
    	try
    	{
    		doSending();
    	}
    	catch(SocketException e)
    	{
    		// 정상. 누군가 요청을 멈췄다.
    	}
    	catch(Exception e)
    	{
    		addExceptionAndCloseResponse(e);
    	}
    }
    
    private void addExceptionAndCloseResponse(Exception e)
    {
    	try
    	{
    		response.add(ErrorResponseder.makeExceptionString(e));
    		response.closeAll();
    	}
    	catch(Exception e1)
    	{
    	}
    }
    ```
    

### 무서운 잡음

- 때로는 JavaDocs도 잡음임.
- 단지 문서를 제공해야 한다는 잘못된 욕심으로 탄생한 잡음

```java
/** The name. */
private String name;

/** The version. */
private String version;

/** The licenceName. */
private String licenseName;

/** The version. */
private String info;
```

- 잘라서 붙여넣기한 오류도 보임.

### 함수나 변수로 표현할 수 있다면 주석을 달지 마라

- 나쁜 예
    
    ```java
    // 전역 목록 <smodule>에 속하는 모듈이 우리가 속한 하위 시스템에 의존하는가?
    if (smodule.getDependSubsystems().contains(subSysMod.getSubSystem())
    ```
    
- 좋은 예
    
    ```java
    ArrayList moduleDependees = smodule.getDependSubsystems();
    String ourSubSystem = subSysMod.getSubSystem();
    if (moduleDependees.contains(ourSubSystem))
    ```
    

### 위치를 표시하는 주석

```java
// Actions //////////////////////////
```

- 가독성만 낮추므로 제거해야 마땅
- 너무 자주 사용하지 않는다면 ㄱㅊ

### 닫는 괄호에 다는 주석

- 중첩이 심하고 장황한 함수라면 의미가 있을지도 모르겠지만 작고 캡슐화된 (우리가 선호하는) 함수에는 잡음!
- 닫는 괄호에 주석을 달아야겠다는 생각이 든다면 대신에 함수를 줄여라

```java
public class wc {
	public satic void main(String[] args) {
		BufferedReader in = new BufferedReader(new InputStreamReader(System.in));
		String line;
		int lineCount = 0;
		int charCount = 0;
		int wordCount = 0;
		try {
			while((line = in.readLine()) != null) {
				lineCount++;
				charCount += line.length();
				String words[] = line.split("\\W");
				wordCount += words.length;
			} // while
			System.out.println("wordCount = " + wordCount);
			System.out.println("lineCount = " + lineCount);
			System.out.println("charCount = " + charCount);
		} // try
		catch (IOException e) {
			System.out.println("Error: " + e.getMessage());
		} // catch
	} // main
}
```

### 공로를 돌리거나 저자를 표시하는 주석

```java
/* 릭이 추가함 */
```

- 누가 언제 무엇을 추가했는지 소스코드 관리 시스템은 다 알고 있음. 저런 거 추가 ㄴㄴ
- 이런 주석은 그냥 오랫동안 코드에 방치되어 점차 부정확하고 쓸모없는 정보로 변하기 쉬움.
- 저런 정보는 소스코드 관리 시스템에 저장하는 편이 좋음.

### 주석으로 처리한 코드

주석으로 처리한 코드만큼 밉살스러운 관행도 드물다.

```java
대충 주석으로 처리된 코드
```

- 좋지 않은 이유
    - 다른 사람들이 지우기를 주저함. → 쓸모없는 코드가 점차 쌓여감.
- 예전과 다르게 지금은 소스코드 관리 시스템을 사용하기 때문에 이젠 필요 없음.

### HTML 주석

> 소스코드에서 HTML 주석은 혐오 그 자체다.
> 

```java
/**
	* 적합성 테스트를 수행하기 위한 과업
	* 이 과업은 적합성 테스트를 수행해 결과를 출력한다.
	* <p/>
	* <pre>
	* 용법:
	* &lt;taskder name=&quot;execute-fitnesse-tests&quot;
	*     classname=&quot;fitnesse.ant.ExecuteFitnesseTestsTask&quot;

어쩌구...
```

### 전역 정보

- 주석을 달아야 한다면 근처에 있는 정보만 설명하자.
- 시스템 전반적인 정보를 기술하지 말자. 맥락 상 안맞다.
- 예시
    
    ```java
    /**
     * 적합성 테스트가 동작하는 포트: 기본값은 <b>8082</b>.
     * @param fitnessePort
     */
    public void setFitnessePort(int fitnessePort) {
      this.fitnewssePort = fitnessePort;
    }
    ```
    

### 너무 많은 정보

- 주석에다 흥미로운 역시나 관련 없는 정보를 장황하게 늘어놓지 말자. 꼭 필요한 정보만 주석으로 달자.

### 모호한 관계

- 주석과 주석이 설명하는 코드는 둘 사이 관계가 명백해야 한다.
- 예시
    
    ```java
    /**
     * 모든 픽셀을 담을 만큼 충분한 배열로 시작한다(여기에 필터 바이트를 더한다).
     * 그리고 헤더 정보를 위해 200바이트를 더한다.
     */
     this.pngBytes = new byte[((this.width + 1) * this.height * 3) + 200];
    ```
    
    - 필터 바이트란 무엇일까? +1과 관련 있을 까? * 3과 관련이 있을까? 아니면 둘 다? 한 픽셀이 한 바이트인가? 200을 추가한 이유는 뭘까?
- 주석을 다는 목적은 코드만으로 설명이 부족해서다. 주석 자체가 다시 설명을 요구하면 안됨.

### 함수 헤더

짧은 함수는 긴 설명이 필요 없다. 짧고 한 가지만 수행하여 이름을 잘 붙인 함수가 주석으로 헤더를 추가한 함수보다 훨씬 좋다.

### 비공개 코드에서 Javadocs

- 공개 API는 Javadocs가 유용하지만 공개하지 않을 코드라면 Javadoc는 **쓸모없다.**
- 시스템 내부에 속한 클래스와 함수에 Javadocs 생성할 필요 Xx

### 예제

- 리팩토링 전 GeneratePrimes.java
    
    (예전에는 상당수가 이런 코드를 보면서 주석을 잘 달았다고 생각했다고 함!)
    
    ```java
    /**
     * This class Generates prime numbers up to a user specified
     * maximum. The algorithm used is the Sieve of Eratosthenes.
     * <p>
     * Eratosthenes of Cyrene, b. c. 276 BC, Cyrene, Libya --
     * d. c. 194, Alexandria.  The first man to calculate the
     * circumference of the Earth. Also known for working on
     * calendars with leap years and ran the library at Alexandria.
     * <p>
     * The algorithm is quite simple. Given an array of integers
     * starting at 2. Cross out all multiples of 2. Find the next
     * uncrossed integer, and cross out all of its multiples.
     * Repeat untilyou have passed the square root of the maximum
     * value.
     *
     * @author Alphonse
     * @version 13 Feb 2002 atp
     */
     
    public class GeneratePrimes
    {
      /**
       * @param maxValue is the generation limit.
       */
      public static int[] generatePrimes(int maxValue)
      {
        if (maxValue >= 2) // the only valid case
        {
          // declarations
          int s = maxValue + 1; // size of array
          boolean[] f = new boolean[s];
          int i;
          // initialize array to true.
          for (i = 0; i < s; i++)
            f[i] = true;
     
          // get rid of known non-primes
          f[0] = f[1] = false;
     
          // sieve
          int j;
          for (i = 2; i < Math.sqrt(s) + 1; i++)
          {
            if (f[i]) // if i is uncrossed, cross its multiples.
            {
              for (j = 2 * i; j < s; j += i)
                f[j] = false; // multiple is not prime
            }
          }
     
          // how many primes are there?
          int count = 0;
          for (i = 0; i < s; i++)
          {
            if (f[i])
              count++; // bump count.
          }
     
          int[] primes = new int[count];
     
          // move the primes into the result
          for (i = 0, j = 0; i < s; i++)
          {
            if (f[i])             // if prime
              primes[j++] = i;
          }
     
          return primes; // return the primes
        }
        else // maxValue < 2
        return new int[0]; // return null array if bad input.
      }
     
      public static void main(String[] args) {
        for (int i: generatePrimes(50)) {
          System.out.print(i + " ");
        }
      }
    }
    ```
    
- 리팩토링 후 PrimesGenerator.java
    
    ```java
    /**
     * 이 클래스는 사용자가 지정한 최대 값까지의 소수를 구한다.
     * 알고리즘은 에라스토테네스의 체다.
     * 2에서 시작하는 정수 배열을 대상으로 작업한다.
     * 처음으로 남아 있는 정수를 찾아 배수를 모두 제거한다.
     * 배열에 더 이상 배수가 없을 때까지 반복한다.
    */
     
    public class PrimeGenerator {
      private static boolean[] crossedOut;
      private static int[] result;
     
      public static int[] generatePrimes(int maxValue) {
        if (maxValue < 2)
          return new int[0];
        else {
          uncrossIntegersUpTo(maxValue);
          crossOutMultiples();
          putUncrossedIntegersIntoResult();
          return result;
        }
      }
     
      private static void uncrossIntegersUpTo(int maxValue) {
        crossedOut = new boolean[maxValue + 1];
        for (int i = 2; i < crossedOut.length; i++)
          crossedOut[i] = false;
      }
     
      private static void crossOutMultiples() {
        int limit = determineIterationLimit();
        for (int i = 2; i <= limit; i++)
          if (notCrossed(i))
            crossOutMultiplesOf(i);
      }
     
      private static int determineIterationLimit() {
        // 배열에 있는 모든 배수는 배열 크기의 제곱근보다 작은 소수의 인수다.
    		// 따라서 이 제곱근보다 더 큰 숫자의 배수는 제거할 필요가 없다.
        double iterationLimit = Math.sqrt(crossedOut.length);
        return (int) iterationLimit;
      }
     
      private static void crossOutMultiplesOf(int i) {
        for (int multiple = 2 * i;
             multiple < crossedOut.length;
             multiple += i)
          crossedOut[multiple] = true;
      }
     
      private static boolean notCrossed(int i) {
        return crossedOut[i] == false;
      }
     
      private static void putUncrossedIntegersIntoResult() {
        result = new int[numberOfUncrossedIntegers()];
        for (int j = 0, i = 2; i < crossedOut.length; i++)
          if (notCrossed(i))
            result[j++] = i;
      }
     
      private static int numberOfUncrossedIntegers() {
        int count = 0;
        for (int i = 2; i < crossedOut.length; i++)
          if (notCrossed(i))
            count++;
     
        return count;
      }
     
      public static void main(String[] args) {
        for (int i : generatePrimes(50)) {
          System.out.print(i + " ");
        }
      }
    }
    ```
    
- **첫 번째 주석** : 중복으로 보일 순 있지만 알고리즘을 이해하기 쉬워지는 주석임. → 남겨둠.
- **두 번째 주석 :** 거의 확실히 필요. 루프 한계값으로 제곱근을 사용한 이유 설명