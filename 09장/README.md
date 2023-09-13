# 9장 : 단위 테스트
## 📌 들어가며
지난 10년 동안 우리 분야는 눈부신 성장을 이뤘다.

원래 TDD 아무도 몰랐음. 그래도 여전히 갈 길은 멀다. 제대로 된 테스트 케이스를 작성해야 한다는 좀 더 미묘한 (그리고 더 중요한) 사실을 놓쳐버렸다.

## 📌 TDD 법칙 세 가지

1. 실패하는 단위 테스트를 작성할 때까지 실제 코드를 작성하지 않는다.
2. 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
3. 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.

- 위 세 가지 규칙을 따르면
    - 개발과 테스트 코드와 실제 코드가 함께 나올 뿐더러
    - 테스트 코드가 실제 코드보다 불과 몇 초전에 나온다.
    - 실제 코드를 사실상 전부 테스트하는 테스트 케이스가 나온다.
- 하지만 방대한 테스트 코드는 관리 문제 유발 가능

## 📌 깨끗한 테스트 코드 유지하기

- 지저분한 테스트 코드를 작성하는 건 테스트를 안 하는 것보다 못할 수도 있음.
    - 실제 코드가 변화하면 테스트 코드도 변해야 하는데 테스트 코드가 지저분할수록 변경하기 어려움.
    - 테스트 코드가 복잡할수록 실제 코드를 짜는 시간보다 테스트 케이스를 추가하는 시간이 더 걸림.
        - 실제 코드를 변경해 기존 테스트 실패 → 테스트 코드는 계속 늘어남.
    - 새 버전을 출시할 때마다 테스트 코드 유지 / 보수 비용이 늘어남.
        
        → 결국 테스트 코드가 개발자 사이에서 가장 큰 불만으로 자리 잡음.
        
        → 테스트 슈트 폐기 
        
        → 수정한 코드가 제대로 도는지 확인 못함
        
        → 망가진 코드
        
- 테스트 코드를 깨끗하게 짰다면 테스트 코드에 쏟아부은 노력은 허사로 돌아가지 않음.

> 테스트 코드는 실제 코드 못지 않게 중요하다.
### 테스트는 유연성, 유지보수성, 재사용성을 제공한다

- 코드에 유연성, 유지보수성, 재사용성을 제공하는 버팀목이 바로 단위 테스트이다.
    - 테스트케이스가 있으면 변경이 두렵지 않으니까!
    - 테스트 케이스가 없다면 모든 변경이 잠정적 버그🐝라 개발자는 변경을 주저함.
- 테스트 슈트는 설계와 아키텍처를 최대한 깨끗하게 보존하는 열쇠🔑

## 📌 깨끗한 테스트 코드
> 깨끗한 테스트 코드를 만들려면? 세 가지가 필요하다. 가독성, 가독성, 가독성.
- 어쩌면 가독성은 실제 코드보다 테스트 코드에 더더욱 중요
- 테스트 코드는 최소의 표현으로 많은 것을 나타내야 함.
- 테스트 코드의 표현력이 떨어지는 예시
    
    ```java
    // 목록 9-1 SerializedPageResponderTest.java
    public void testGetPageHieratchyAsXml() throws Exception {
      crawler.addPage(root, PathParser.parse("PageOne"));
      crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
      crawler.addPage(root, PathParser.parse("PageTwo"));
    
      request.setResource("root");
      request.addInput("type", "pages");
      Responder responder = new SerializedPageResponder();
      SimpleResponse response =
        (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
      String xml = response.getContent();
    
      assertEquals("text/xml", response.getContentType());
      assertSubString("<name>PageOne</name>", xml);
      assertSubString("<name>PageTwo</name>", xml);
      assertSubString("<name>ChildOne</name>", xml);
    }
    
    public void testGetPageHieratchyAsXmlDoesntContainSymbolicLinks() throws Exception {
      WikiPage pageOne = crawler.addPage(root, PathParser.parse("PageOne"));
      crawler.addPage(root, PathParser.parse("PageOne.ChildOne"));
      crawler.addPage(root, PathParser.parse("PageTwo"));
    
      PageData data = pageOne.getData();
      WikiPageProperties properties = data.getProperties();
      WikiPageProperty symLinks = properties.set(SymbolicPage.PROPERTY_NAME);
      symLinks.set("SymPage", "PageTwo");
      pageOne.commit(data);
    
      request.setResource("root");
      request.addInput("type", "pages");
      Responder responder = new SerializedPageResponder();
      SimpleResponse response =
        (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
      String xml = response.getContent();
    
      assertEquals("text/xml", response.getContentType());
      assertSubString("<name>PageOne</name>", xml);
      assertSubString("<name>PageTwo</name>", xml);
      assertSubString("<name>ChildOne</name>", xml);
      assertNotSubString("SymPage", xml);
    }
    
    public void testGetDataAsHtml() throws Exception {
      crawler.addPage(root, PathParser.parse("TestPageOne"), "test page");
    
      request.setResource("TestPageOne"); request.addInput("type", "data");
      Responder responder = new SerializedPageResponder();
      SimpleResponse response =
        (SimpleResponse) responder.makeResponse(new FitNesseContext(root), request);
      String xml = response.getContent();
    
      assertEquals("text/xml", response.getContentType());
      assertSubString("test page", xml);
      assertSubString("<Test", xml);
    }
    ```
    
    - 읽는 사람을 고려하지 않음.
- 깨끗하고 이해가 쉬운 코드
    
    ```java
    // 목록 9-2 SerializedPageResponderTest.java
    public void testGetPageHierarchyAsXml() throws Exception {
      makePages("PageOne", "PageOne.ChildOne", "PageTwo");
    
      submitRequest("root", "type:pages");
    
      assertResponseIsXML();
      assertResponseContains(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
    }
    
    public void testSymbolicLinksAreNotInXmlPageHierarchy() throws Exception {
      WikiPage page = makePage("PageOne");
      makePages("PageOne.ChildOne", "PageTwo");
    
      addLinkTo(page, "PageTwo", "SymPage");
    
      submitRequest("root", "type:pages");
    
      assertResponseIsXML();
      assertResponseContains(
        "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>");
      assertResponseDoesNotContain("SymPage");
    }
    
    public void testGetDataAsXml() throws Exception {
      makePageWithContent("TestPageOne", "test page");
    
      submitRequest("TestPageOne", "type:data");
    
      assertResponseIsXML();
      assertResponseContains("test page", "<Test");
    }
    ```
    
    - BUILD-OPERATE-CHECK 패턴이 위와 같은 테스트 구조에 적합
    - 명확히 세 부분으로 나누어짐.
        1. 첫 번째 부분은 테스트 자료를 만듦.
        2. 두 번째 부분은 테스트 자료를 조작하며, 세번째 부분은 조작한 결과가 올바른지 확인
        3. 세 번째 부분은 조작한 결과가 올바른지 확인
    - 테스트 코드는 본론에 돌입해 진짜 필요한 자료유형과 함수만 사용

### 도메인에 특화된 테스트 언어

- 목록 9-2는 도메인에 특화된 언어(DSL)로 테스트 코드를 구현하는 기법을 보여줌.
    - 테스트를 구현하는 당사자와 나중에 읽어볼 독자를 도와주는 테스트 언어
    - 처음부터 설계된 건 아니고 계속 리팩터링하다가 진화된 API임.
- 숙련된 개발자라면 자기 코드를 좀 더 간결하고 표현력이 풍부한 코드로 리팩터링 해야 함.

### 이중 표준

- 테스트 API코드에 적용하는 표준은 실제 코드에 적용하는 표준과 확실히 다르다.
- 단순하고, 간결하고, 표현력이 풍부해야 하지만, 실제 코드만큼 효율적일 필요는 없다.
    - 실제 환경이 아니라 테스트 환경에서 돌아가는 코드이기 때문인데, 실제 환경과 테스트 환경은 요구사항이 판이하게 다르다.
- 좋은 예제 : 온도가 '급격하게 떨어지면' 경보, 온풍기, 송풍기가 모두 가동되는지 확인하는 코드
    
    ```java
    // 목록 9-3 EnvironmentControllerTest.java
    @Test
    public void turnOnLoTempAlarmAtThreashold() throws Exception {
      hw.setTemp(WAY_TOO_COLD); 
      controller.tic(); 
      assertTrue(hw.heaterState());   
      assertTrue(hw.blowerState()); 
      assertFalse(hw.coolerState()); 
      assertFalse(hw.hiTempAlarm());       
      assertTrue(hw.loTempAlarm());
    }
    ```
    
    ```java
    // 목록 9-4 EnvironmentControllerTest.java(리팩터링)
    @Test
    public void turnOnLoTempAlarmAtThreshold() throws Exception {
      wayTooCold();
      assertEquals("HBchL", hw.getState());
    ```
    
    - 대문자는 '켜짐'이고 소문자는 '꺼짐'을 뜻한다. 문자는 항상 '{heater, blower, cooler, hi-temp-alarm, lo-temp-alarm}' 순서다.
    - 비록 위 방식이 그릇된 정보를 피하라는 규칙의 위반에 가깝지만 여기서는 적절해 보인다.
    - 일단 의미만 안다면 눈길이 문자열을 따라 움직이며 결과를 재빨리 판단한다. 테스트 코드를 읽기가 사뭇 즐거워진다.
- 복잡한 예제
    
    ```java
    // 목록 9-5 EnvironmentControllerTest.java (더 복잡한 선택)
    @Test
    public void turnOnCoolerAndBlowerIfTooHot() throws Exception {
      tooHot();
      assertEquals("hBChl", hw.getState()); 
    }
      
    @Test
    public void turnOnHeaterAndBlowerIfTooCold() throws Exception {
      tooCold();
      assertEquals("HBchl", hw.getState()); 
    }
    
    @Test
    public void turnOnHiTempAlarmAtThreshold() throws Exception {
      wayTooHot();
      assertEquals("hBCHl", hw.getState()); 
    }
    
    @Test
    public void turnOnLoTempAlarmAtThreshold() throws Exception {
      wayTooCold();
      assertEquals("HBchL", hw.getState()); 
    }
    ```
    
    ```java
    // 목록 9-6 MockControlHardware.java
    public String getState() {
      String state = "";
      state += heater ? "H" : "h"; 
      state += blower ? "B" : "b"; 
      state += cooler ? "C" : "c"; 
      state += hiTempAlarm ? "H" : "h"; 
      state += loTempAlarm ? "L" : "l"; 
      return state;
    }
    ```
    
    - 효율을 높이려면 StringBuffer가 더 적합
    - 실제 환경에서는 절대로 안 되지만 테스트 환경에서는 전혀 문제없는 방식이 있다.
    - 코드의 깨끗함과는 철저히 무관하다.

## 📌 테스트 당 assert 하나

- JUnit으로 테스트 코드를 짤 때 함수마다 assert를 단 하나만 사용해야 한다고 주장하는 학파가 있다.
- assert가 하나라면 결론이 하나기 때문에 코드를 이해하기 빠르고 쉽다.
- 하지만 목록 9-2는 어떨까? "출력이 XML이다"라는 assert문과 "특정 문자열을 포함한다"는 assert문을 하나로 병합하는 방식이 불합리해 보인다.
- 목록 9-7에서 보듯이 테스트를 쪼개 각자가 assert를 수행하면 된다.

```java
// 목록 9-7 SerializedPageResponderTest.java (단일 Assert)
public void testGetPageHierarchyAsXml() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
  whenRequestIsIssued("root", "type:pages");
  
  thenResponseShouldBeXML(); 
}

public void testGetPageHierarchyHasRightTags() throws Exception { 
  givenPages("PageOne", "PageOne.ChildOne", "PageTwo");
  
  whenRequestIsIssued("root", "type:pages");
  
  thenResponseShouldContain(
    "<name>PageOne</name>", "<name>PageTwo</name>", "<name>ChildOne</name>"
  ); 
}
```

- given-when-then 이라는 관례를 사용했다는 사실에 주목한다 → 테스트 코드 읽기 쉬워짐.
- 하지만 테스트를 분리하면 중복되는 코드가 많아진다.
- TEMPLATE METHOD 패턴을 사용하면 중복을 제거할 수 있지만 배보다 배꼽이 더크다.
- 결국 이 경우엔 aseert 문 여러개를 사용하는편이 좋다.
- 단일 assert 문이라는 규칙이 훌륭한 지침이라 생각한다. 대체로 단일 assert를 지원하는 해당 분야 테스트 언어를 만들려 노력한다.
- 하지만 때로는 주저 없이 함수 하나에 여러 assert 문을 넣기도 한다. 단지 assert 문 개수는 최대한 줄여야 좋다는 생각이다.

## 📌 테스트 당 개념 하나

"테스트 함수마다 한 개념만 테스트하라”

- 이것저것 잡다한 개념을 연속으로 테스트하는 긴 함수는 피한다.
- 목록 9-8은 독자적인 개념 세 개를 테스트하므로 독자적인 테스트 세 개로 쪼개야 마땅하다.
- 이를 한 함수로 몰아넣으면 독자가 각 절이 거기에 존재하는 이유와 각 절이 테스트하는 개념을 모두 이해해야 한다.

```java
// 목록 9-8 addMonth() 메서드를 테스트하는 장황한 코드
public void testAddMonths() {
  SerialDate d1 = SerialDate.createInstance(31, 5, 2004);

  SerialDate d2 = SerialDate.addMonths(1, d1); 
  assertEquals(30, d2.getDayOfMonth()); 
  assertEquals(6, d2.getMonth()); 
  assertEquals(2004, d2.getYYYY());
  
  SerialDate d3 = SerialDate.addMonths(2, d1); 
  assertEquals(31, d3.getDayOfMonth()); 
  assertEquals(7, d3.getMonth()); 
  assertEquals(2004, d3.getYYYY());
  
  SerialDate d4 = SerialDate.addMonths(1, SerialDate.addMonths(1, d1)); 
  assertEquals(30, d4.getDayOfMonth());
  assertEquals(7, d4.getMonth());
  assertEquals(2004, d4.getYYYY());
```

- 이 경우 assert 문이 여럿이라는 사실이 문제가 아니라, 한 테스트 함수에서 여러 개념을 테스트한다는 사실이 문제
- 그러므로 가장 좋은 규칙은 "**개념 당 assert 문 수를 최소로 줄여라**" 와 "**테스트 함수 하나는 개념 하나만 테스트하라**"임.

## 📌 F.I.R.S.T

- 깨끗한 테스트는 다음 다섯 가지 규칙을 따르는데, 각 규칙에서 첫 글자를 따오면 FIRST가 된다.
- 빠르게Fast:
    - 테스트는 빨라야 한다. 테스트는 빨리 돌아야 한다는 말이다.
    - 테스트가 느리면 자주 돌릴 엄두를 못 낸다.
    - 자주 돌리지 않으면 초반에 문제를 찾아내 고치지 못한다.
    - 코드를 마음껏 정리하지도 못한다. 결국 코드 품질이 망가지기 시작한다.
- 독립적으로Independent:
    - 각 테스트를 서로 의존하면 안 된다.
    - 한 테스트가 다음 테스트가 실행될 환경을 준비해서는 안 된다.
    - 각 테스트는 독립적으로 그리고 어떤 순서로 실행해도 괜찮아야 한다.
    - 테스트가 서로에게 의존하면 하나가 실패할 때 나머지도 잇달아 실패하므로 원인을 진단하기 어려워지며 후반 테스트가 찾아내야 할 결함이 숨겨진다.
- 반복가능하게Repeatable:
    - 테스트는 어떤 환경에서도 반복 가능해야 한다.
    - 실제 환경, QA 환경, 버스를 타고 집으로 가는 길에 사용하는 노트북 환경(네트워크가 연결되지 않은)에서도 실행할 수 있어야 한다.
    - 테스트가 돌아가지 않는 환경이 하나라도 있다면 테스트가 실패한 이유를 둘러댈 변명이 생긴다.
    - 게다가 환경이 지원되지 않기에 테스트를 수행하지 못하는 상황에 직면한다.
- 자가검증하는Self-Validating:
    - 테스트는 bool값으로 결과를 내야 한다. 성공 아니면 실패다.
    - 통과 여부를 알리고 로그 파일을 읽게 만들어서는 안 된다.
    - 통과 여부를 보려고 텍스트 파일 두 개를 수작업으로 비교하게 만들어서도 안 된다.
    - 테스트가 스스로 성공과 실패를 가늠하지 않는다면 판단은 주관적이 되며 지루한 수작업 평가가 필요하게 된다.
- 적시에Timely:
    - 테스트는 적시에 작성해야 한다. 단위 테스트는 테스트하려는 실제 코드를 구현하기 직전에 구현한다.
    - 실제 코드를 구현한 다음에 테스트 코드를 만들면 실제 코드가 테스트하기 어렵다는 사실을 발견할지도 모른다.
    - 어떤 실제 코드는 테스트하기 너무 어렵다고 판명날지 모른다.
    - 테스트가 불가능하도록 실제 코드를 설계할지도 모른다.