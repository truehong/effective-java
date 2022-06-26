# 8장 - 아이템 54 null이 아닌, 빈 컬렉션이나 배열을 반환하라

### 배열, 컬렉션 값이 없을때
- null로 반환
- 빈 배열이나 컬렉션으로 반환


### null로 반환
```java
return list.isEmpty() ? null : new ArrayList<>(item);
```


### 빈 배열이나 컬렉션으로 반환
- 빈 컬렉션을 매번 새로 할당
```java
return list.isEmpty() ? Collections.emptyList() : new ArrayList<>(item);
```
- 빈 배열을 만들어놓고 할당
```java
private static final Test[] EMPTY_LIST = new Test[0];

return item.toArray(EMPTY_LIST);
```
배열이나 컬렉션을 반환할때 반환 되는 경우가 잦다면 만들어놓고 할당 하는 쪽을 사용하는것이 메모리적으로 더 효과적이다.


## 정리
배열이나 컬렉션을 반환한다면 null 대신 빈 배열이나 빈 컬렉션을 반환하자.  
이전 프로젝트때 매번 null체크를 하고 리팩토링 과정을 거치면서 새로할당하는 방법으로 한 적이 있다.  
그 경험과 이번 아이템에서 배운 내용을 바탕으로 프로젝트 초반 좋은 설계를 할 수 있을 것같다.