## Q1. 소프트웨어 생명 주기 모델이란 무엇인가요?
- 소프트웨어 개발을 위한 일련의 과정을 단계적으로 나눈 모델로써, 폭포수 모델, 프로토타입 모델, 애자일이 있다.
- 이 중 애자일은 요구사항이 빈번하게 변하는 프로젝트에 적합하며, 프로토타입 모델은 초기 요구사항을 명확히 하지 못할 때 사용한다.

## Q2. SDLC(Software Development Life Cycle)가 어떤 단계로 이루어져 있는지
- 계획 → 요구 분석 → 설계 → 구현 → 테스트 → 배포 → 유지보수
- 크게 정의 단계, 개발 단계, 유지보수 단계로 존재한다.

## Q3. 폭포수 모형의 단점과 이를 보완한 모델
- 개발 중 발생하는 새로운 요구나 경험을 반영하기 어려워 처음부터 사용자가 모든 요구사항을 명확하게 제시해야 한다.
- 오류 없이 다음 단계로 진행해야 하는데 현실적으로 힘들다.
- 업무에 운용할 때 검출되지 않은 오류가 발생할 수 있다.
- 프로토타입 모형(또는 나선형 모델)에서 폭포수 모델에서 단계별 역행이 안 되는 부분을 설계 단계 이전에 보완할 수 있다.

## Q4. SDLC 모델 중 요구 분석 → 위험 분석 → 개발 → 사용자 평가 과정을 반복하며 점진적으로 개발하는 모델은?
- 나선형 모델

## Q5. 나선형 모델이란? 장단점은?
- 나선형(Spiral) 모델은 소프트웨어 개발을 여러 차례 반복하여 점진적으로 완성하는 모델이다.
- 장점: 프로젝트가 커질수록 유연하게 변경사항을 반영할 수 있으며, 위험 요소를 조기에 식별하여 실패 확률을 줄인다.
- 단점: 사용자의 피드백에 의존하기 때문에 오류를 발견하지 못하면 큰 위험이 발생하며, 개발 비용과 기간이 증가할 수 있다.

## Q6. SDLC 모델과 개발 방법론의 차이점
- SDLC 모델: 소프트웨어 개발 프로세스를 일반화해서 프로젝트에 적용할 수 있도록 만든 것
  → 소프트웨어 개발을 위한 일련의 절차와 활동
- 개발 방법론: 모델의 프로세스를 수행하기 위한 구체적인 방법
  → 프로세스를 수행하기 위한 접근법과 원칙, 철학

## Q7. TDD(Test-Driven Development)란?
- 테스트 주도 개발로써, 테스트를 먼저 작성하고 테스트를 통과한 코드를 작성하는 개발 방법론이다.
- TDD의 3가지 단계는 실패하는 테스트 코드를 먼저 작성, 테스트 코드를 성공시키기 위한 실제 코드를 작성, 중복 코드 제거, 일반화 등의 리팩토링을 수행하는 것이다.

## Q8. TDD의 장점과 주의점은?
- TDD는 테스트 코드를 먼저 작성한 후 통과하는 최소한의 기능을 구현하는 개발 방법론이다.
- 장점: 테스트 문서를 대체할 수 있고, 객체지향적 설계를 유도하고, 디버깅 시간이 단축되어 유지보수가 용이하다.
- 주의점: 테스트가 실패하는지(Red)를 먼저 확인한 후 구현해야 하며, 테스트 간의 독립성을 유지해야 한다.
