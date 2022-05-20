 ### 핵심 정리 
 
  ```
   열거 타입은 확실히 정수 [상수보다 뛰어나다]. 더 읽기 쉽고 안전하고 강력하다. 
  ```
  ```
  대다수 열거 타입이 명시적 생성자나 메서드 없이 쓰이지만, [각 상수를 특정 데이터와 연결]짓거나 [상수마다 다르게 동작하게 할 때]는 필요하다. 
  ```
  ```
  드물게는 하나의 메서드가 [상수별로 다르게 동작해야 할때]도 있다. 이런 열거타입에서는 switch문 대신 [상수별 메서드 구현] 을 사용하자.
  ```
  ```
  [열거 타입 상수 일부가 같은 동작을 공유]한다면 [전략 열거 타입 패턴] 을 사용하자.
  ```

### 기존 - 정수 열거 패턴 (상당히 취약하다!)
```java
  public static final int APPLE_FUJI = 0;
  public static final int APPLE_PIPPIN = 1;
  public static final int APPLE_GRANNY_SMITH = 2;

  public static final int ORANGE_NAVEL = 0;
  public static final int ORANGE_TEMPLE = 1;
  public static final int ORANGE_BLOOD = 2;

```
 - 평범한 상수를 나열한 형태
 - 타입 안정성 보장 불가 `APPLE_FUJI - ORANGE_TEMPLE`
 - 이름`APPLE_FUJI`이 아닌 값`0` 이 클라이언트에 그대로 보여진다.

### 현재 - 기본적인 Enum 
```java 
 public enum Apple { FUJI, PINPPIN, GRANNY_SMITH }
```
- 컴파일 타입 안전성 제공 `Planet 외에 다른 enum은 생성 불가`
- 임의의 메서드나 필드를 추가하거나 인터페이스를 구현할 수 있다.

### 클래스 타입의 Enum 
 - 상수 하나당 자신의 인스턴스를 하나씩 만들어 **public static final field** 로 공개한다. 
 - 밖에서 접근할 수 있는 생성자를 제공하지 않으므로 사실상 **final** -> **인스턴스를 직접 생성하거나 확장 불가**
 - 열거 타입 선언으로 만들어진 인스턴스들은 딱 하나씩만 존재 *(싱글턴 보장 - 아이템 3)*

```java 
 public enum Planet {
  MERCURY(3.302e+23, 2.439e6),
  VENUS(4.869+24, 6.052e6); 
  ...
  private final double mass; 
  private final double radius; 
  private final double surfaceGravaty;
  ...
 }

```


### Switch 문 대신 상수별 메서드로 구현하기

**AS-IS**
```java
public enum Operation {
  PLUS, MINUS, TIMES, DIVIDE;
  
  public double apply(double x, double y) {
    switch(this){
      case PLUS: return x + y;
      case PLUS: return x - y;
      case PLUS: return x * y;
      case PLUS: return x / y;
    }
    throw new AssertionError("알 수 없는 연산: " + this);
  }

}


```
**TO-BE(1) - 상수별 메서드 구현**
```java 
public enum Operation { 
  PLUS {public double apply(double x, double y){return x + y}},
  MINUS {public double apply(double x, double y){return x - y}},  
  TIMES {public double apply(double x, double y){return x * y}},
  DIVIDE {public double apply(double x, double y){return x / y}},
  
  public abstract double apply(double x , double y); 
}
```

### 열거 타입 상수 각각을 특정 데이터와 연결

- 열거 타입 상수 각각을 특정 데이터와 연결 지으려면 생성자에서 데이터를 받아 특정 인스턴스 필드에 저장하면 된다.  
  
**TO-BE(2) 심볼 추가, toString 재정의**

```java  
public enum Operation { 
  PLUS("+") {public double apply(double x, double y){return x + y}},
  MINUS("-") {public double apply(double x, double y){return x - y}},  
  TIMES("*") {public double apply(double x, double y){return x * y}},
  DIVIDE("/") {public double apply(double x, double y){return x / y}},
  
  private final String symbol; 
  
  Operation(String symbol) {this.symboe = symbol; }
  public abstract double apply(double x , double y); 
}
```

```java
 public static void main(String[] args) {
  double x = Double.parseDouble(args[0]);
  double y = Double.parseDouble(args[1]);
  
  for(Operation op : Operation.values())
    System.out.printlen("%f %s %f = %f%n, x, op, y, op.apply(x,y));
 }

```

### 전략 열거 타입 
```java
public enum PayrollDay {
  MONDAY(WEEKDAY),
  TUESDAY(WEEKDAY),
  WEDNESDAY(WEEKDAY),
  THURSDAY(WEEKDAY),
  FRIDAY(WEEKDAY),
  SATURDAY(WEEKEND),
  SUNDAY(WEEKEND),
  ;

  private final PayType payType;

  PayrollDay(PayType payType) {
    this.payType = payType;
  }

  int pay(int minutesWorked, int payRate) {
    return payType.pay(minutesWorked, payRate);
  }

  enum PayType {
    WEEKDAY {
      @Override
      int overtimePay(int minsWorked, int payRate) {
        return minsWorked <= MINS_PER_SHIFT ? 0 :
              (minsWorked - MINS_PER_SHIFT) * payRate / 2;
      }
    },
    WEEKEND {
      @Override
      int overtimePay(int minsWorked, int payRate) {
          return minsWorked * payRate / 2;
      }
    };

    abstract int overtimePay(int mins, int payRate);
    private static final int MINS_PER_SHIFT = 8 * 60;

    int pay(int minsWorked, int payRate) {
      int basePay = minsWorked * payRate;
      return basePay + overtimePay(minsWorked, payRate);
    }
  }
}

```

