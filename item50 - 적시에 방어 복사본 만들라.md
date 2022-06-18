# Item50 적시에 방어적 복사본을 만들라

## 배운내용
- 방어적 복사본을 만들어야 하는 상황
- 방어적 복사본을 만들때는 clone메서드로 복사하지말자
- 가급적 불변 객체를 조합해 객체를 구성하자!! 결국 불변객체를 조합해서 사용하면 방어적 복사본을 우리가 신경쓸 필요가 없다.

## 불변성을 깨뜨리는 경우

- jvm이 메모리를 관리해주는 언어이다. 즉, 특별하게 메모리를 관리할 필요가 없다.
- 하지만, 다른 클래스로부터의 메모리침범을 막을 수 있는건 아니다. 이를 예방하기 위해서는 불변식을 깨뜨리는 것을 막도록 방어적 프로그래밍을 해야한다.

아래 객체를 보면 객체의 허락 없이 외부에서 내부의 내용을 수정하지 못하는것처럼 보인다!!

```java
import java.util.Date;

public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        if (start.compareTo(end) > 0)
            throw new IllegalArgumentException(
                    start + " after " + end);
        this.start = start;
        this.end = end;
    }

    public Date getStart() {
        return start;
    }

    public Date getEnd() {
        return end;
    }
}
```

하지만 아래와 코드를 사용하면 객체의 값을 아주 쉽게 변경할 수 있다. 이 문제는 Java8에서는 불변인 Instant나 LocalDateTime또는 ZonedDateTime을 사용해서 해결할 수 있다.(즉, Date는 불변객체가 아니므로 사용하지 않는것이 좋다)

```java
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);
end.setYear(78);

```



## 방어적 복사

- 불변성을 해치는 코드로부터 보호하기 위해서는 방어적 복사가 필요하다. 
- 아래 코드 처럼 생성자 매개변수를 각각 방어적으로 복사하면 Period에서는 원본이 아닌 복사본을 사용하게 된다.
- 단, 유효성 검사로직은 복사본으로 해야하지 원본으로 유효성 검사를하면 의미 없다!!
- 또한, clone메서드를 통해서 복사하지 않도록 해야한다. 
	- clone메서드로 객체를 복사하면 원본의 변경이 복사본의 변경에도 영향을 준다.
	- clone메서드는 객체를 복사할때 얕은복사를 진행한다.(즉 메모리 참조를 복사하기때문에 같은 값을 바라보고 있음)

```java
import java.util.Date;

public final class Period {
    private final Date start;
    private final Date end;

    public Period(Date start, Date end) {
        this.start = new Date(start.getTime());
        this.end = new Date(end.getTime());

        if (start.compareTo(end) > 0)
            throw new IllegalArgumentException(
                    start + " after " + end);
    }

    public Date getStart() {
        return new Date(start.getTime());
    }

    public Date getEnd() {
        return new Date(end.getTime());
    }
}

```


```java
Date start = new Date();
Date end = new Date();
Period p = new Period(start, end);
end.setYear(78);// 여기서 end의 값을 변경했더라도 Period는 복사본이므로 문제가 없다.

```
