# 아이템 59 : 라이브러리를 익히고 사용하라

# 표준 라이브러리

1. 코드를 작성한 전문가의 지식과 앞서 사용한 다른 프로그래머들의 경험을 활용할 수 있다.
2. 핵심적인 일과 크게 관련 없는 문제를 해결하느라 시간을 허비하지 않아도 된다.
3. 노력하지 않아도 성능이 지속해서 개선된다.
4. 기능이 점점 많아진다.
5. 다른 개발자들이 읽고 유지보수, 재활용 하기 좋은 코드가 된다.

## 예시 : Random

### 직접 구현

```java
static Random rnd = new Random();

static int random(int n) {
	return Math.abs(rnd.nextInt()) % n;
}

public static void main(String[] args){
	int n = 2 * (Integer.MAX_VALUE / 3);
  int low = 0;
  
  for (int i = 0; i < 1000000; i++){
  	if (random(n) < n/2)
      	low++;
  }
  System.out.println(low);
}
```

위의 코드에서 `Math.abs(random.nextInt()) % n` 는 아래와 같은 문제점을 가지고 있다.

- n이 그리 크지 않은 2의 제곱수라면 같은 수열이 반복된다.
- n이 2의 제곱수가 아니라면 몇몇 숫자가 더 자주 반환된다.
- 더불어 지정한 범위의 바깥수가 종종 튀어나올 수도 있다.
    
    → `rnd.nextInt()`가 MIN_VALUE를 반환하는 경우
    

### **Random.nextInt(n) 사용**

```java
static int random(int n) {
	return rnd.nextInt();
}
```

`Random.nextInt(n)`를 사용해 위의 문제를 해결할 수 있다.

→ 자바 7 이후부터는 `Random`이 아닌 `ThreadLocalRandom` 을 사용하는 것이 좋다.

→ 포크 조인 풀이나, 병렬 스트림에서는 `SplittableRandom` 을 사용하는 것이 좋다.

## 예시 : **InputStream**

지정한 URL의 내용을 가져오는 코드로 InputStream의 `transfer()` 메서드를 사용하면 쉽게 구현 가능하다.

```java
public void urlTest() throws IOException {
    try (InputStream in = new URL("https://dbpia.co.kr").openStream()) {
        in.transferTo(System.out);
    }
}
```

## 핵심 정리

- 일반적으로 라이브러리의 코드는 직접 작성한 것보다 품질이 좋고, 점차 개선될 가능성이 크기 때문에 직접 구현하지 말고 라이브러리를 사용하자.
- 라이브러리 중 `java.lang`, `java.util`. `java.io` 하위 패키지들에는 익숙해지는 것이 좋다.
- 자바 표준 라이브러리에서 원하는 기능을 찾지 못했다면, 고품질의 서드파티(ex) google의 구아바) 라이브러리까지 찾아보고 마지막 대안으로 직접 구현하도록 하자.