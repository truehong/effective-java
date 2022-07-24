### 핵심 정리

첫번째, 두 박싱된 기본타입을 == 연산자로 비교한다면 식별성 비교가 이뤄진다.

두번째, 같은 연산에서 기본타입과 박싱된 기본타입을 혼용하면 언박싱이 이뤄지며, 언박싱 과정에서 `NullPointerException`을 던질 수 있다.

세번째, 기본타입을 박싱하는 작업은 필요없는 객체를 생성하는 부작용을 나을 수 있다.

 기본 타입은 값만 가지고 있으나, 박싱된 기본 타입은 값에 더해 식별성(**identity**) 이란 속성을 갖는다. 

아래 비교자, *compare 메서드는 첫 번째 원소가 두 번째 원소보다 작으면 음수, 같으면 0, 크면 양수를 반환한다)

```java
Comparator<Integer> naturalOrder = (i, j) -> (i < j) ? -1 : (i ==j ? 0 : 1)
naturalOrder.compare(new Integer(42), new Integer(42)); // error 1을 출력 
```

아래는 문제를 수정한 비교자 

```java
Comparator<Integer> naturalOrder = (iBoxed, jBoxed) -> {
	in i = iBoxed, j = jBoxed; 
	return i< j ? -1 : (i == j ? 0 : 1)
}
```

기본타입과 박싱된 기본타입을 혼용한 연산에서는 박싱된 기본 타입의 박싱이 자동으로 풀린다. 

그리고 null 참조를 언박싱 하면 `NullPointException` 이 발생한다. 

지역변수 sum을 박싱된 기본타입으로 선언하여 느려진다.

```java
public static void main(String[] args) {
	Long sum = 0L; 
	for (long i = 0; i <= Integer.MAX_VALUE; i++){
		sum += i
	} 
	System.out.println(sum);
}
```

### 언제 사용하는가

첫번째, 컬렉션의 원소, 키, 값으로 쓴다. 컬렉션은 기본타입을 담을 수 없다. 

더 일반화해 말하면, 매개 변수화 타입이나 매개변수로 기본 타입을 지원하지 않기 때문이다. 

예컨대 변수를 ThreadLocal<int> 타입으로 선언하는건 불가능 하다. 

마지막으로, 리플렉션을 통해 메서드를 호출할때도 박싱된 기본 타입을 사용해야한다.
