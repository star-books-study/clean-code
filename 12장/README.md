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
