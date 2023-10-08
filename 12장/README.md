# 12장 : 창발성
## 📌 창발적 설계로 깔끔한 코드를 구현하자

착실하게 따르기만 하면 우수한 설계가 나오는 간단한 규칙 네 가지

- 모든 테스트를 실행한다.
- 중복을 없앤다.
- 프로그래머 의도를 표현한다.
- 클래스와 메서드 수를 최소로 줄인다.

위 목록은 중요도 순이다.

## 📌 단순한 설계 규칙 1 : 모든 테스트를 실행하라

테스트 케이스를 작성하면 저절로 설계 품질이 높아진다.

- 테스트를 철저히 거쳐 모든 테스트 케이스를 항상 통과하는 시스템은 ‘테스트가 가능한 시스템’이다.
    - 테스트가 불가능한 시스템은 검증도 불가능하다.
- 테스트가 가능한 시스템을 만드려고 애쓰면 설계 품질 ⬆️
    - 크기가 작고 목적 하나만 수행하는 클래스가 나옴.
    - 결함도가 높으면 테스트 케이스를 작성하기 힘듦.

## 📌 단순한 설계 규칙 2~4 : 리팩터링

테스트 케이스를 모두 작성했다면 이제 코드와 클래스를 정리해도 괜찮다.

- 코드 몇 줄을 추가할 때마다 잠시 멈추고 설계를 조감한다.
    - 설계 품질을 낮추는가? 그럼 깔끔히 정리한 후 테스트 케이스를 돌려 기존 기능을 깨뜨리지 않았다는 사실을 확인한다.

> 코드를 정리하면서 시스템이 깨질까 걱정할 필요가 없다. 테스트 케이스가 있으니까!
> 

리펙터링 단계에서는 소프트웨어 설계 품질을 높이는 기법이라면 무엇이든 적용해도 괜찮다.

## 📌 중복을 없애라

중복은 추가 작업, 추가 위험, 불필요한 복잡도를 뜻한다.

- 다음과 같은 메서드가 있다고 하자.

```java
int size() {}
	boolean isEmpty() {}
```

- 각 메서드를 따로 구현하는 방법도 있다. 하지만 isEmpty 메서드에서 size 메서드를 사용하면 코드를 중복해 구현할 필요가 없어진다.

```java
boolean isEmpty() {
	return 0 == size();
}
```
- 다음 코드를 보자.

```java
public void scaleToOneDimension(
 float desiredDimension, float imageDimensiion) {
	if(Math.abs(desiredDimension - imageDimension) < errorThreshold)
		return;
	float scalingFactor = desiredDimension / imageDimension;
	scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
	
	RenderedOp newImage = ImageUtilities.getScaledImage(
	  image, scalingFactor, scalingFactor);
		image.dispose();
		System.gc();
		image = newImage;
	}
	public synchronized void rotate(int degrees) {
		RenderedOp newImage = ImageUtilities.getRotatedImage(
		  image, degrees);
		image.dispose();
		System.gc();
		image = new Image();
	}
```
- 일부 코드가 동일하다. 중복을 제거하자.

```java
public void scaleToOneDimension(
 float desiredDimension, float imageDimensiion) {
	if(Math.abs(desiredDimension - imageDimension) < errorThreshold)
		return;
	float scalingFactor = desiredDimension / imageDimension;
	scalingFactor = (float)(Math.floor(scalingFactor * 100) * 0.01f);
	replaceImage(ImageUtilites.getScaledImage(
				image, scalingFacor, scalingFactor));
}

public synchronized void rotate(int degrees) {
	replaceImage(ImageUtilites.getScaledImage(image, degrees));
}

private void replaceImage(RenderedOp newImage) {
	image.dispose();
	System.gc();
	image = new Image();
}
```

- 공통적인 코드를 새 메서드로 뽑고 보니 클래스가 SRP를 위반한다.
- 다른 클래스로 옮기면 새 메서드의 가시성이 높아지겠군! 다른 팀원이 재사용할 기회 포착도 가능
- 이런 ‘`소규모 재사용`’은 시스템 복잡도를 줄여줌.
- `TEMPLATE METHOD` 패턴은 고차원 중복을 제거할 목적으로 자주 사용하는 기법임.

```java
public class VacationPolicy {
	public void accureUSDivisionVacation() {
		// 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
		// ...
		// 휴가 일수가 미국 최소 법정 일수를 만족하는지 확인하는 코드
		// ...
		// 휴가 일수를 급여 대장에 적용하는 코드
		// ...
	}

	public void accureEUDivisionVacation() {
		// 지금까지 근무한 시간을 바탕으로 휴가 일수를 계산하는 코드
		// ...
		// 휴가 일수가 미국 최소 법정 일수를 만족하는지 확인하는 코드
		// ...
		// 휴가 일수를 급여 대장에 적용하는 코드
		// ...
	}
}
```

- 두 코드는 거의 동일. 직원 유형에 따라 살짝 변함
- TEMPLATE METHOD 패턴 적용

```java
abstract public class VacationPolicy {
	public void accureVacation() {
		calculateBaseVacationHours();
		alterForLegalMinimum();
		applyToPayroll();
	}

	public void calculateBaseVacationHours() { /* ... */ };
	abstract protected void alterForLegalMinimums();
	private void applyToPayrool() { /* ... */ };
}

public class UsVacationPolicy extends VacationPolicy {
	@Override protected void alterForLegalMinimums() {
		// 미국 최소 법정 일수를 사용한다.
	}
}

public class EUVacationPolicy extends VacationPolicy {
	@Override protected void alterForLegalMinimums() {
		// 유럽연합 최소 법정 일수를 사용한다.
	}
}
```
## 📌 표현하라
- 소프트웨어 프로젝트 비용 중 대다수는 장기적인 유지보수에 들어감.
- 소프트웨어 프로젝트 비용 중 대다수는 장기적인 유지보수에 들어감.
- 결함과 유지보수 비용을 줄이기 위해서 **코드는 개발자의 의도를 분명하게 표현해야 함.**
1. 좋은 이름을 선택한다.
2. 함수와 클래스 크기를 가능한 줄인다.
3. 표준 명칭을 사용한다.
    - 예를 들어, 디자인 패턴은 의사소통과 표현력 강화가 주요 목적
        - 클래스에 COMMAND나 VISITOR 같은 표준 패턴을 사용해 구현된다면 클래스 이름에 패턴 이름 넣어주기
4. 단위 테스트 케이스 꼼꼼히 작성
    - 테스트 케이스 == ‘예제로 보여주는 문서’
5. 노오력하기
    - 코드만 돌린 후 다음 문제로 직행하지 말고 나중에 읽을 사람을 고려해 쉽게 만드려는 충분한 고민이 필요
    - 자신의 작품을 조금 더 자랑하자. 주의는 대단한 재능이다.

## 📌 클래스와 메서드 수를 최소로 줄여라

- 클래스와 메서드 크기를 줄이자고 조그만 클래스와 메서드를 수없이 만들지는 말아라.
- 함수와 클래스 수를 가능한 줄이라.
- 무의미하고 독단적인 정책
    
    ex) 클래스마다 무조건 인터페이스 생성하라
    
    ex) 자료 클래스와 동작 클래스는 무조건 분리하라
    
- 가능한 독단적인 견해는 멀리하고 실용적인 방식을 택하라.
- 목표 : 함수와 클래스 작게 유지 & 시스템 크기 작게 유지
