# 3장 - Comparable을 구현할지 고려하라

### 동치
수학과 논리학에서 동치(同値)란 두 문장이 논리적으로 같다는 것을 의미

### comparable 사용하는 이유
- 단순 동치성 비교에 더해 `순서 비교`까지 가능
- 제네릭하다.

### compareTo 메서드 규약
이 객체 - A , 주어진 객체 - B
A 가 B 보다 작으면 -1 , 같으면 0, 크면 1을 리턴
- 반사성  
A.compoareTo(B) == 0 이면 sgn(A.compareTo(C)) == sgn(B.compareTo(C)) 만족해야 한다.  
(※sgn : 부호함수. 음수 = -1, 같으면 0 , 양수 = 1)
- 대칭성  
A.compareTo(B) == B.compareTo(A) * -1 만족해야 한다.
- 추이성  
A.compareTo(B) > 0 이고, B.compareTo(C) > 0 이면 A.compareTo(C) 만족해야 한다.


### comparable 구현
- 관계연산자 `<` 와 `>` 는 거추장스럽고 오류를 유발하니 대신 compare을 사용하자.
```java
public int compareTo(int kor, int eng) {
	return (kor < eng) ? -1 : ((kor == eng) ? 0 : 1); //관계연산자
}
public int compareTo(int kor, int eng) {
	return Integer.compare(kor, eng); // compare
}
```

- 메서드 이용
```java
/// 경우 : 비교 필드가 여러개일때
// 1. 여러번 비교
// 더 느리고 코드량이 많아진다
public int compareTo(Exam ex) {
	int result = Short.compare(kor, ex.kor);
	if (result == 0) {
		result = Short.compare(eng, ex.eng);
		if (result == 0) {
			result = Short.compare(math, ex.math);
		}
	}
	return result;
}
// 2. 비교자 생성 메서드를 이용
// 더 간결하다. 보기편하다.
private static final Comparator<Exam> COMPARATOR
			= comparingInt((Exam ex) -> ex.kor)
				.thenComparingInt(ex -> ex.eng)
				.thenComparingInt(ex -> ex.math);

public int comareTo(Exam ex) {
	return COMPARATOR.compare(this, ex);
}
```


## 정리
Array를 많이 사용하게 되면서 Comparable 사용은 필수라고 생각한다.  
쓰임에 맞게 잘 사용할 수 있도록 해보자.