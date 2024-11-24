# 아이템 11 : equals를 재정의하려거든 hashCode도 재정의하라

# hashCode

: 객체의 주소값을 변환하여 생성한 객체의 고유한 정수값

## hashCode 재정의가 필요한 이유

```java
public static void main(String[] args) {
    Map<Point, String> map = new HashMap<>();

    Point p1 = new Point(1, 2);
    Point p2 = new Point(1, 2);
    System.out.println(p1.equals(p2)); // true

    map.put(p1, "test");
    System.out.println(map.get(p2)); // null
}
```

map의 key로 사용된 **p1,p2는 논리적 동치성이 보장**되었다.

하지만 map에서 **p2를 key로 value를 얻고자 할 때 value인 test가 출력되지 않는다.**

→ **hash기반 Collection에서는 equals를 재정의할 때 hashCode도 같이 재정의** 해야한다.

## hashCode를 재정의하는 과정

Point의 `hashCode` 메서드를 재정의한다.

```java
 static class Point {
        public final int x;
        public final int y;

          public Point(int x, int y) {
              this.x = x;
              this.y = y;
          }

          @Override
          public boolean equals(Object o) {
              if (o == this) return true;
              if (!(o instanceof Point)) return false;
              Point p = (Point) o;

              return p.x == this.x && p.y == this.y;
          }

          @Override
          public int hashCode() {
		          // hashCode() 재정의
              int result = Integer.hashCode(this.x);
              result += 31 * result + Integer.hashCode(this.y);

              return result;
          }
    }
```

1. `equals`에 사용된 핵심필드 중 첫번째 필드의 hash 값을 int 타입 변수 result에 초기화 한다.
2. 나머지 핵심필드에 대해 hash 값을 계산하고 계산된 결과를 result에 누적한다.
    
    → 누적시 `31`을 곱하는데 이것은 해시충돌을 피해서 해시성능을 높이기 위함이다.
    
3. 누적된 결과를 반환한다.

https://velog.io/@dhk22/Java-Effective-Java-Equals-And-HashCode-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EC%95%8C%EA%B3%A0-%EC%93%B0%EC%9E%90