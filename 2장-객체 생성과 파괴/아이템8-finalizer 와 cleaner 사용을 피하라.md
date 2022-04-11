# 2장 - 아이템 8 finalizer 와 cleaner 사용을 피하라

## 객체 소멸자
객체가 가비지 컬렉터(gc)에 의해 제거될 때 호출되는 함수

### finalizer
- 예측할 수 없고, 상황에 따라 위험할 수 있어서 일반적으로 불필요
- 오작동, 낮은 성능, 이식성 문제의 원인이 되기도 함


### cleaner
- finalizer 보다는 덜 위험하지만 여전히 예측할 수 없고, 느리고, 일반적으로 불필요함.

### finalizer, cleaner 부작용
- Garbage Collection(GC) 알고리즘에 따라 finalizer와 cleaner가 실행이 되기까지 정확히 측정이 되지않으며
제때 실행조차 될수 없다. 
- 또한 구현에 따라 천차만별.
- 상태를 영구적으로 수정하는 작업은 피해야 한다.


### AutoCloseable
- finalizer와 cleaner를 대신하기 위해 사용. 클라이언트에서 인스턴스를 다 쓰고 나면 close 메서드를 사용.
- AutoCloseable에서 Cleaner를 사용하는 경우 try-with-resources 사용

### 책예제 소스
```java
 public class Room implements AutoCloseable {  
      
	      private static final Cleaner cleaner = Cleaner.create();  
	      
	      private static class State implements Runnable {  
	      int numJunkpiles;  
	      
	      State(int numJunkpiles) {  
		      this.numJunkpiles = numJunkpiles;  
	      }  
	      
	      @Override  
	      public void run() {  
		      System.out.println("방 청소");  
		      numJunkpiles = 0;  
	      }  
	     }  
	     private final State state;  
	     private final Cleaner.Cleanable cleanable;  
	      
	     public Room(int numJunkpiles) {  
	      state = new State(numJunkpiles);  
	      cleanable = cleaner.register(this, state);  
	      }  
	      
	      @Override  
	      public void close() throws Exception {  
	      cleanable.clean();  
	      }  
    }

    public class main {  
      public static void main(String[] args) throws Exception {  
	      try (Room myRoom = new Room(7)) {
		      System.out.println("안녕~");
		  }
		  new Room(99);
		  System.out.println("아무렴~");
	  }
```


## 정리
finalizer를 사실 거의 보기만 하고 과거 선임자들의 소스에서 아그렇구나하며 함수만 사용했던 기억이 있다. 
가급적 사용을 자제하되, 어느 부분에서 사용을 할수밖에 없는지 알아둘 필요가 있다고 느꼈다.