# 13장. clone() 재정의는 주의해서 진행하라

## Cloneable 인터페이스

Cloneable 인터페이스는 복제해도 되는 클래스임을 명시하는 Mixin 인터페이스이다.

> 믹스인(Mixin)
다른 클래스의 부모클래스가 되지 않으면서 다른 클래스에서 사용할 수 있는 메서드를 포함하는 클래스이다. 믹스인은 때때로 "상속"이 아니라 "포함"으로 설명된다.
> 

```java
public interface Cloneable {
}
```

- clone() 메서드란?

```java
java.lang.Object

protected native Object clone() throws CloneNotSupportedException;
```

- Cloneable 인터페이스는 빈 인터페이스이다
따라서 Cloneable 을 구현했다고 clone() 을 호출할 수 있는 것이 아니다.
- clone() 메서드는 따로 오버라이딩 해줘야 한다.
- clone() 은 접근제한자가 protected 라서 같은 패키지에서만 접근가능하다

## Cloneable를 왜 사용하는가?

- clone() 은 모든 클래스에서 오버라이딩 할 수 있다.
- 하지만 Cloneable 인터페이스를 구현하지 않은 클래스의 clone() 은 예외를 던진다.
- super.clone() 을 사용하지 않을 것이라면, Cloneable 사용하지 않아도 된다.
- Cloneable 을 구현한 클래스만 clone() 을 사용할 수 있다.
- 즉, Cloneable 은 “상위 클래스 (Object) 에 정의된 clone() 의 동작방식을 변경한다”는 의미다.

## 문제점

일반적인 clone 메서드 규약은 제약이 심하지 않다. 

`Object` 클래스의 명세를 살펴보면 다음과 같다.

```java
x.clone() != x 는 참이여야 한다.
x.clone().getClass() == x.getClass() 는 참이여야 한다.
x.clone().equals(x) 는 참이여야 한다.
```

- 일반적인 clone 메서드 규약은 기초자료형을 필드로 담고 있을 경우 쉽게 규약을 지킬 수 있다.
- 아래 X라는 객체는 int와 String을 필드로 갖는다. 그리고 Cloneable 인터페이스를 구현하였다.

```java
class X implements Cloneable {
    int field1;
    String field2;

    public X(int field1, String field2) {
        this.field1 = field1; this.field2 = field2;
    }

    @Override protected X clone() {
        try {
            return (X) super.clone();
        } catch (CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
}
```

```java
public static void main(String[] args) throws CloneNotSupportedException {
  X x = new X(1, "X");

  boolean isCloneSame = x != x.clone();
  System.out.println(isCloneSame); // true

  boolean isTypeEqual = x.clone().getClass() == x.getClass();
  System.out.println(isTypeEqual); // true, not an absolute requirement

  boolean equals = x.clone().equals(x); // true, not an absolute requirement
  System.out.println(equals);

  X clonedX = x.clone();
  boolean isFields1Equals = clonedX.field1 == x.field1;
  boolean isFields2Equals = clonedX.field2 == (x.field2);
  System.out.println("is field 1 Equal? " + isFields1Equals);
  System.out.println("is field 2 Equal? " + isFields2Equals);
}
```

첫 번째 boolean 값은 true가 나온다.

두 번째 boolean 값에도 true가 나온다.

세 번째는 false가 나온다. 

왜 일까? equals를 오버라이드하지 않았기 때문이다.
마지막 2개의 boolean은 X의 필드가 같은가를 확인한다. 
field1은 기초 자료형인 int이므로 같다. 
String 역시 같은 문자열이므로 JVM이 내부적으로 같은 객체를 사용하였다.

## 참조형 타입을 필드로 갖는 객체의 Cloneable 구현

참조형 타입을 필드로 갖는다면, 단순히 super.clone() 을 구현해서는 안된다.

```java
public class Stack_BadImplementation implements Cloneable {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack_BadImplementation() {
        this.elements = new Object[DEFAULT_INITIAL_CAPACITY];
    }

    public void push(Object e) {
        ensureCapacity();
        elements[size++] = e;
    }

    public Object pop() {
        if (size == 0)
            throw new EmptyStackException();
        Object result = elements[--size];
        elements[size] = null; // Eliminate obsolete reference
        return result;
    }

    private void ensureCapacity() {
        if (elements.length == size)
            elements = Arrays.copyOf(elements, 2 * size + 1);
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return super.clone();
    }
}
```

이 클래스를 cloneable하게 만들고 싶다면, clone 메서드가 단순히 super.clone()을 호출하면 될 것 같다. 하지만 size필드의 경우 정확히 복제 하지만, element 필드는 복제 후에도 여전히 원본 Stack 인스턴스의 필드를 참조한다. 
이런 레퍼런스 타입을 복제하려면 clone 메서드는 원본 객체에 내부도 카피하도록 한다.

## clone() 재귀 호출이 부족하다면 Deep copy 를 하라

만약 배열 안에 가변 참조 객체가 있다면 재귀적인 clone() 호출으로는 부족하므로 Deep copy 를 쓰도록 한다.

```java
    public Entry deepCopy() {
        return new Entry(key, value, next == null ? null : next.deepCopy());
    }
    @Override
    public CustomHashTable clone() {
        try {
            CustomHashTable result = (CustomHashTable) super.clone();
            result.buckets = new Entry[buckets.length];

            for (int i = 0; i < buckets.length; i++) {
                if (buckets[i] != null)
                    result.buckets[i] = buckets[i].deepCopy();
            }

            return result;
        } catch (final CloneNotSupportedException e) {
            throw new AssertionError();
        }
    }
```

이 방식은 원소 수만큼 연결리스트를 만든다. 따라서 원소 수만큼의 스택 프레임을 소비하고, 스택 오버플로를 일으킬 수 있다. 그럴 때 반복자를 이용하여 구현할 수 있다.

```java
public Entry deepCopy() {
        Entry result = new Entry(key, value, next);
        for (Entry p = result; p.next != null; p = p.next) {
            p.next = new Entry(p.next.key, p.next.value, p.next.next);
        }
        return result;
    }
```

## 정적 팩토리 메서드로 구현하자.

- 지금까지 살펴본 clone 메서드를 구현하면서 정확히 동작하길 원하기란 어렵다.
(게다가, clone 메서드는 thread safe하지 않다.)
- 가장 쉽고 효과적인 방법은 복제용 생성자나 복제용 정적 팩토리 메서드를 만드는 것이다.
- 배열만이 clone() 을 재대로 사용하는 유일한 예이다. 
복사하는 대상이 배열이 아니면 복사 생성자, 복사 팩터리가 좋다.

### 복사 생성자

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public Stack(Stack s) {
        this.elements = s.elements.clone();
        this.size = s.size;
    }
}
```

기본 생성자가 필요하면 추가하기

### 복사 팩터리

```java
public class Stack {
    private Object[] elements;
    private int size = 0;
    private static final int DEFAULT_INITIAL_CAPACITY = 16;

    public static Stack newInstance(Stack s) {
        return new Stack(s.elements, s.size);
    }
}
```