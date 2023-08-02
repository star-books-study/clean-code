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

- 때때로 주석은 구현을 이해하게 도와주는 선을 넘어 결정에 깔린 의도까지 설명
- 흥미로운 예제
    - 두 객체를 비교할 때 저자는 다른 어떤 객체보다 자기 객체에 높은 순위를 주기로 결정했다.
    
    ```java
    public int compareTo(Object o)
    {
    	if(o instanceof WikipagePath)
    	{
    		WikiPagePath p = (WikiPagePath) o;
    		String compressedName = StringUtil.join(names, "");
    		String 
    ```