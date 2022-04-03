# 10장. euqals는 일반 규약을 지켜 재정의하라

---

자바의 모든 클래스는 Object 클래스의 자식 클래스이다. Object 클래스에 포함된 final이 아닌 메서드 (Object는 메서드로만 이루어져 있다.)는 재정의를 염두하고 설계된 것이다. 이때 일반 규약에 맞게 재정의 하지 않으면 HashMap, HashSet 등 타 메서드가 규약을 준수한다고 가정하는 클래스들이 오동작 하게 된다.

euqals의 재정의가 필요한 경우의 예시, equals의 재정의 시 지켜야할 규약

---

## **euqals의 재정의가 필요한 경우**

- equals의 재정의는 앞서 말한데로 타 클래스의 오동작을 초래할 수 있으므로 주의해야한다.

그럼에도 equals의 재정의가 필요한 경우는 값이 같은지 비교하는 것을 넘어 논리적 동치성을 확인하기 위해서이다.

→ 객체식별성(객체가 물리적으로 같은가) vs 논리적동치성

```java
//객체식별성을 판단하는 '=='의 예시
   String str1 = "asd";
	 String str2 = new String("asd");
	 

	 System.out.println(str1);
	 System.out.println(str2);
	 boolean result = (str1 == str2);  //-> false 리턴

//논리적동치성을 판단하는 'equals' (String 클레스에서 재정의 되어있음을 확인)
  Object obj1 = "asd";
	Object obj2 = new String("asd");
	
	obj1 = "asd";
	obj2 = "asd";
	
	 System.out.println(obj1);
	 System.out.println(obj2);
	 boolean result = (obj1 == obj2);  //-> true 리턴
	 
	 System.out.println(result);
```

객체식별성과 논리적동치성은 각각 객체의 동일 여부와 값의 동일 여부를 판단한다는 의미이다. 

위의 코드를 확인해보면 ‘==’은 객체식별성을 판단하기 때문에 다른 별도로 생성된 객체가 다름에 false를 리턴하고 있음을 확인할 수 있다. 그리고 equals()메소드 사용시 직접 사용되지 않고 하위 클래스 String에서 재정의 되어서 논리적동치성을 판별하고 있음을 확인할 수 있다.

이를 통해 값을 표현하는 클래스인 (String, Integer)과 같이 논리적동치성을 확인해야 하는 경우 equals의 재정의가 필요함을 알 수 있다. equals의 재정의를 통해 논리적 동치성을 확인하도록 만들면 인스턴스 값의 비교가 가능하고 Map의 Key와 Set의 원소로 사용하여 활용할 수 있다.

(Enum과 같은 경우는 논리적으로 같은 인스턴스가 만들어 지지 않음으로 논리적 동치성과 객체 식별성이 같은 의미를 가진다. - 재정의 필요가 없음)

## equals의 재정의 시 지켜야할 규약

- **반사성** : x.equals(x)는 true

equals의 재정의 시 같은 객체에 대한 비교 즉 자기 자신은 자신과 같아야 한다.

- **대칭성** : x.equals(y)가 true이면 y.equals(x)도 true

만약 CaseInsensitiveString 객체와 기존의 String 객체의 논리적동치성을 비교하는 재정의를 구현한다고 했을 경우의 예시를 보자

```java
CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "Polish"

cis.equals(s); // true 반환
s.equals(cis); // false 반환
```

위 코드를 보면 cis를 통해 호출한 CaseInsensitiveString 객체의 equals는 String 객체 s를 알고 있어 cis.equals(s)가 true를 반환하지만 s를 통해 호출한 String 객체의 s.equals(cis)의 경우  equals를 새롭게 오버라이딩 한 CaseInsensitiveString 클래스를 모르고 있으므로 논리적동치성을 비교하고자 하는 의도와 달리 false를 반환하게 된다.

- **추이성** : x.equals(y)는 true이고  y.equals(z)는 true이면 x.equals(z)는 true

```java
// 1. 재정의 단계

public class Point{
	private final int x;
	private final int y;

pbulic Point(int x, int y){
	this.x = x;
	this.x = y;
}

//equals 재정의
@Override public boolean equals(Object o){
if(!(o instanceof Point))
	return false;
Point p = (Point)o;
return p.x ==x && p.y == y;
}
...
}
```

```java
// 2. 재정의 한 이후 상속을 통해 새로운 필드가 추가된 경우
Public clss ColorPoint extends Point{
	private final Color color;

	public ColorPoint(int x, int y, Color color){
	super(x,y);
	this.color = color;
}
...
}
```

논리적동치성을 비교하는 경우 비교하는 3가지 객체가 모두 같아야 한다는 의미이다. 당연한게 아닌가 싶지만 예시 코드와 같이 equals의 재정의 이후 상위클래스 상속을 통해 하위클래스를 생성하고 필드를 추가하는 경우 새롭게 추가한 필드를 무시하고 비교를 진행하게 되는 경우가 발생한다. → 대칭성, 추이성 위배 발생 

- **일관성** : x.equals(y)를 반복해서 호출해도 항상 true또는 false를 반환

이것도 위배될 일이 있는 당연한게 아닌가 싶지만 equals의 판단에 신뢰할 수 없는 자원이 끼어드는 경우 일관성 조건의 만족이 어려워진다. 실무에서도 이러한 문제가 발생할 수 있다.  

예를 들어 java.net.URL에 정의된 equals() 같은 경우(java.net.URL에서 재정의 후 URLStreamHandler 객체를 통해 Object객체 이용) 기존 URL과의 비교에 매핑된 호스트의 IP 주소를 이용하기 때문에 네트워크를 통해야하는데 이 경우 동일한 결과를 보장할 수 없다는 문제가 생긴다.

- **null-아님** : x.equals(null)는 false

모든 객체가 null과 같지 않아야 한다는 null아님 조건은 앞선 예시 코드 중 instanceof 로 묵시적 null 검사를 사용하는 이유이기도 하다. 논리적동치성 검사를 위해서 건네받은 Object 객체를 형변환하여 새롭게 정의된 객체와 비교해야 하는데 이때 잘못된 타입과 비교된다면 ClassCastException이 발생하게 된다.