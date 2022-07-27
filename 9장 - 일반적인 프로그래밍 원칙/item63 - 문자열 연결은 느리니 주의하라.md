### 핵심 정리

 (+) 대신 StringBuilder의 append 메서드를 사용하라. 

문자열 연결 연산자로 문자열 n 개를 잇는 시간은 n2 에 비례한다. 

문자열은 불변이라서 두 분자열을 연결할 경우 내용을 모두 복사해야 하므로 성능 저하는 피할 수 없는 결과다.

```java
public String statement() {
	String result = "";
	for (int i = 0; i< numItems(); i++) 
		result += lineForItem(i) // 문자열 연결
	return result; 
}
```

StringBuilder 를 사용하면 문자열 연결 성능이 크게 개선된다. 

```java
public String statement2() {
	StringBuilder b = new StringBuilder(numItems() * LINE_WIDTH); 
	for (int i =0; i < numItems(); i++)
		b.appennd(lineForItem(i);
		return b.toString();
}
```

statement2 가 6.5 배나 빠르다. statement 메서드의 수행 시간은 품목 수의 제곱이 비례해 늘어나고 statement2 는 선형으로 늘어나므로, 품목 수가 늘어날 수록 성능 격차도 점점 벌어질 것이다.
