
스트림 API 의 추상 개념 중 핵심은 두 가지이다. 

- `스트림` : 데이터 원소의 유한 혹은 무한 시퀀스(sequence)
- `스트림 파이프라인` : 원소들로 수행하는 연산단계를 표현하는 개념

  `소스 스트림` 에서 시작해 `종단 연산`으로 끝나며 그 사이에 하나 이상의 `중간 연산`이 있을 수 있다. 각 `중간 연산`은 스트림을 어떠한 방식으로 변환한다. `종단 연산`은 마지막 중간 연산이 내놓은 스트림에 최후의 연산을 가한다. 원소를 정렬해 컬렉션에 담거나, 특정 원소 하나를 선택하거나 .

# 특징

- 스트림은 지연 평가 된다.

`지연 평가(lazy evaluation)`

평가는 종단 연산이 호출될 때 이뤄지며, 종단 연산에 쓰이지 않는 데이터 원소는 계산에 쓰이지 안흔ㄴ다. 이러한 지연 평가가 무한 스트림을 다룰 수 있게 해주는 열쇠다. 종단 연산이 없는 스트림 파이프라인은 아무일도 하지 않는 명령어인 no-op과 같으니, 종단 연산을 빼먹는 일이 절대 없도록 하자.

- 스트림은 어디서든 올 수 있다. 예를 들면
    - 컬렉션 (Collection)
    - 배열 (Array)
    - 파일 (File)
    - 정규표현식 (Regex Pattern Matcher)
    - 난수 생성기 (Random Generator)
    - 다른 스트림 (Other Stream)

- 스트림의 종류,
    - **Stream** : 객체 참조타입에 대한 Stream
    - **IntStream** : int 타입에 대한 Stream
    - **LongStream** : long 타입에 대한 Stream
    - **DoubleStream** : double 타입에 대한 Stream
    
- 스트림으로 하면 좋은 것들 :
    - 원소들의 시퀀스를 일관되게 변환한다.
    - 원소들의 시퀀스를 필터링 한다.
    - 원소들의 시퀀스를 하나의 연산을 사용해 결합한다. (더하기, 연결하기, 최솟값 구하기)
    - 원소들의 시퀀스를 컬렉션에 모은다. (아마도 공통ㅈ된 속성을 기준으로 묶어 가며)
    - 원소들의 시퀀스에서 특정 조건을 만족하는 원소를 찾는다.
    
- 스트림으로 하기 어려운 것들 :
    - 한 데이터가 파이프라인의 여러단계(stage) 를 통과할 때 이 데이터의 각 단게에서의 값들에 동시에 접근하기 어려운 경우.
    - char 값들을 처리할 때는 스트림을 삼가는 편이 낫다.

```java
private static List<Card> newDeck() {
	List<Card> result = new ArrayList<>();
	for (Suit suit : Suit.values())
		for (Rank rank : Rank.values())
			result.add(new Card(suit, rank)); 
	return result;
} 
```

```java
private static List<Card> new Deck() {
	return Stream.or(Suie.values())
		.flatMap(suit -> 
			Stream.of(Rank.values())
				.map(rank -> new Card(suit, rank)))
				.collect(toList());
}

```
