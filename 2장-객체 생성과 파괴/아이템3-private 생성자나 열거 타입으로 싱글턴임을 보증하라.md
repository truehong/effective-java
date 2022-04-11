# 2장 - 아이템 3 private 생성자나 열거 타입으로 싱글턴임을 보증하라

## 싱글턴 만드는 방식

### `public static final 필드 방식`의 싱글턴
```java
public class Bank {
  public static final Bank INSTANCE = new Bank();
	private Bank() { ... }
}
```
장점 1. 생성자를 private으로 함으로써 인스턴스가 하나임이 보장이된다.  
=> public, protected 생성자가 없기 때문  
예외 1. 리플렉션 API(아이템 65)인 AccessibleObject.setAccessible 을 이용하여 private 생성자 호출이 가능  
=> 해결책 : 생성자를 수정하여 두번째 객체가 생성되려 할때 예외를 던지게함  
```java
public class Bank {
    public static final Bank INSTANCE = new Bank();
	private Bank() { 
        if (INSTANCE != null) throw new RuntimeException("exist instance");
     }
}
```

### `정적 팩터리 방식`의 싱글턴
```JAVA
public class Bank {
	private static final Bank INSTANCE = new Bank();
	private Bank() { ... }
	public static Bank getInstance() { return INSTANCE; }
}
```
장점 1. API를 바꾸지 않고도 싱글턴이 아니게 변경할 수 있음  
=> 예 : 팩터리 메서드가 호출하는 스레드별로 다른 인스턴스를 넘겨주게 할 수 있음  
장점 2. 원한다면 정적 팩터리를 제네릭 싱글턴 팩터리로 만들 수 있음 (아이템 30)  
장점 3. 정적 팩터리의 메서드 참조를 공급자(supplier)로 사용할 수 있음 (아이템 43, 44)  
=> 예 : Bank::getInstance를 Supplier<Bank>로 사용하는 식

### `열거 타입 방식`의 싱글턴
```JAVA
public enum Bank {
	INSTANCE;
}
```
장점 1. public 필드 방식과 비슷하지만 더 간결함  
장점 2. 추가 노력 없이 직렬화할 수 있음  
장점 3. 아주 복잡한 직렬화 상황이나 리프렉션 공격에서도 제 2의 인스턴스가 생기는 일을 완벽하게 막아줌  

예외 1. 만드려는 싱글턴이 Enum 외의 클래스를 상속해야 한다면 이 방법은 사용할 수 없음  
=> 열거 타입이 다른 인터페이스를 구현하도록 선언할 수는 있음  


### `public static final 필드 방식`과 `정적 팩터리 방식` 의 직렬화 (12장)
모든 인스턴스 필드를 일시적(transient)이라고 선언하고 readResolve 메서드를 제공해야 함 (아이템 89)  
=> 이렇게 하지 않으면 직렬화된 인스턴스를 역직렬화할 때마다 새로운 인스턴스가 만들어짐  

해결 : readResolve 메서드를 추가  
```JAVA
private Object readResolve() {
	// 진짜 객체를 반환하고, 가짜 객체는 가비지 컬렉터에 맡김
	return INSTANCE;
}
```

## 정리
singleton, 자바 직렬화, enum 에 대해서 평소 당연하듯 사용하였던 부분들에 대해서 이유 있게 사용하고
왜 사용하는 지에 중점을 두어서 공부를 하였으며 singleton을 프로젝트 성향에 맞춰 다양하게 적용을
해볼 생각이 들었다.