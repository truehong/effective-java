```java 
 public double x; 
 public double y;
```

 이런 클래스는 데이터 필드에 직접 접근할 수 있으니 캡슐화의 이점을 제공하지 못한다.
 API 를 수정하지 않고는 내부 표현을 바꿀수 없고, 불변식을 보장할 수 없으며, 외부에서 필드에 접근할 때 부수 작업을 수행할 수도 없다.  
 철처한 객체 지향 프로그래머는 이런 클래스를 상당히 싫어해서 필드들을 모두 private 으로 바꾸고 public (getter) 를 추가한다. 
 
```java 
 private double x; 
 private double y; 
 
 public Point(double x, double y) {
    this.x = x; 
    this.y = y; 
 }
 
 public double getX() {return x;} 
 public double getY() {return y;} 
 
 public void setX(double x) { this.x = x; } 
 public boid setY(double y) { this.y = y; }
```

  ### 핵심 정리  
  public 클래스는 절대 가변 필드를 직접 노출해서는 안된다. 불변 필드라면 노출해도 덜 위 험하지만 완전히 안심할 수는 없다. 
  하지만 `package-private 클래스`나 `private 중첩 클래스`에서는 종종(불변이든 가변이든)필드는 노출하는 편이 나을 때도 있다. 
    

```java 
 public final class Time {
   private static final int HOURS_PER_DAY = 24; 
   private static final int MINUTES_PER_HOUR = 60;
   
   public final int hour; 
   public final in minute;
   
   public Time(int hour, int minute){
   
   
   }
 }
```
