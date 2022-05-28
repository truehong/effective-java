# 6장 - 아이템 37 ordinal 인덱싱 대신 EnumMap을 사용하라

### ordinal
- 열거 타입 상수에서 해당 상수가 몇 번째 위치인지 반환하는 메서드
```java
class Plant {
  enum LifeCycle { ANNUAL, PERENNIAL, BIENNIAL }

  final String name;
  final LifeCycle lifeCycle;

  Plant(String name, LifeCycle lifeCycle) {
    this.name = name;
    this.lifeCycle = lifeCycle;
  }

  @Override public String toString() {
    return name;
  }
}
```
문제점
- 상수 선언 순서에 따라 영향을 받는다.
- 초기화 할때 정수 값 잘못 입력시 ArrayIndexOutOfBoundException 발생한다.
- 정수는 열거 타입과 달리 타입이 안전하지 않다.


### EnumMap
```java
Map<Plant.LifeCycle, Set<Plant>> plantsByLifeCycle = new EnumMap<>(Plant.LifeCycle.class);
for(Plant.LifeCycle lc : Plant.LifeCycle.values())
  plantsByLifeCycle.put(lc, new HashSet<>());
for(Plant p : garden)
  plantsByLifeCycle.get(p.lifeCycle).add(p);
```



## 정리
배열의 인덱스를 얻기 위해 ordinal보다 EnumMap을 사용하자.  