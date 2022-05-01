# 4장 - 아이템 24 멤버 클래스는 되도록 static으로 만들라

## 중첩클래스
다른 클래스 안에 정의된 클래스를 말한다.  
- 정적 멤버 클래스
- 비정적 멤버 클래스
- 익명 클래스
- 지역 클래스


### 정적 멤버 클래스
- 다른 클래스 안에 선언
- 일반 클래스와 동일
```java
 public class Process {  
   public String returnData(String data) {
     String data = new Calculate("KOREA")
     .calc();
     return data;
   }
   
   private static class Calculate {
     private String data;

     Calculate(String data) {
       this.data = data;
     }

     private Calculate calc() {
       data = data.toLowerCase();
       return this;
     }

   }
 }
```

### 비정적 멤버 클래스
- 어댑터를 정의할 때 자주 사용
```java
 public class MySet<E> extends AbstractSet<E> {
  
  @Override public Iterator<E> iterator() {
    return new MyIterator();
  }
  // 어댑터
  private class MyIterator implements Iterator<E> {
    
  }
}
```

### 익명 클래스
- 선언 지점에서만 인스턴스 사용
- 람다 나오고
- 제약이 많음
- 예: Comparator


### 지역 클래스
- 가장 드물게 사용.
- 짧게 작성해야 함


## 정리
자연스럽게 사용하던 것들이 정확히 내가 뭘 하는지 뭔 의미를 가지고 의미하는지 다시 한번 생각해보을 수 있었다.