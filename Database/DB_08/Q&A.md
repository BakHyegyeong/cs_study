## Q1. 트랜잭션이란 무엇인가요? 그리고 특징 성질은 무엇인가요?
트랜잭션은 DB 시스템에서 수행하는 작업의 단위로, 하나 이상의 쿼리가 모여 하나의 작업을 수행하는 것입니다. 트랜잭션의 특징은 ACID로, 원자성(Atomicity), 일관성(Consistency), 고립성(Isolation), 지속성(Durability)을 보장해야 합니다.

## Q2. 트랜잭션 원자성, 일관성에 대해서 설명해주세요.
- **원자성**: 모두 수행되거나 모두 수행되지 않는 것을 보장한다. 롤백이 일어났을 때 어떻게 해결해야 하는지에 대한 방안이 있어야 한다.
- **일관성**: 트랜잭션 완료 후 데이터베이스가 항상 일관된 상태를 유지해야 한다.

## Q3. 원자성을 보장하기 위한 방안
- **커밋**: 쿼리가 성공적으로 처리되었다고 확정하는 명령어
    - 커밋 후에 데이터베이스에 영구적으로 저장된다.
- **롤백**: 트랜잭션이 일어나기 전으로 돌리는 일(취소)

## Q4. 고립성(Isolation)과 지속성(Durability)이란 무엇인가요?
- 고립성은 여러 트랜잭션이 동시에 실행될 때 서로 영향을 미치지 않고 독립적으로 실행되어야 하는 성질입니다. 다른 트랜잭션의 연산에 간섭할 수 없습니다. 
- 지속성은 트랜잭션 작업이 성공적으로 완료되면 결과가 영구적으로 반영되어야 하는 성질입니다.

## Q5. 고립성이 낮아지면 어떤 문제가 발생할 수 있나요?
- 서로 간섭이 발생하여, 더티 리드, 팬텀 리드, 반복 불가능 읽기가 생길 수 있다.
    - **더티 리드**: 커밋되지 않은 행에 변경 데이터를 보는 것
    - **팬텀 리드**: 한 트랜잭션 내에서 동일한 쿼리문을 사용했음에도 불구하고 다른 결과를 얻는 것
    - **반복 불가능 읽기**: 같은 트랜잭션에서 동일한 데이터에 대해 두 번 읽었을 때 다른 결과를 얻는 현상

## Q6. 격리 수준이 가장 높은 단계
- **SERIALIZABLE**: 트랜잭션을 순차적으로 진행시키는 것
    - 여러 트랜잭션이 같은 행에 접근할 수 없다.
    - 교착 상태가 일어날 확률이 높고 가장 성능이 떨어진다.

## Q7. 지속성에서 시스템 장애 발생 후 원래 상태로 회복하는 방법
- **체크섬**: 중복 검사, 오류 정정을 통해 송신된 자료의 무결성을 보호
- **저널링**: 트랜잭션에 대한 로그를 남기는 것
- **롤백**

## Q8. Commit과 Rollback의 차이점은 무엇인가요?
- **Commit**: 트랜잭션의 작업 결과를 데이터베이스에 영구적으로 반영하는 연산으로, 성공적으로 완료된 경우 수행됩니다.
- **Rollback**: 트랜잭션의 작업을 취소하고 수행 전 상태로 복구하는 연산으로, 작업 중 오류가 발생한 경우 수행됩니다.

## Q9. COMMIT 이후의 데이터 상태
- 데이터에 대한 변경 사항이 데이터베이스에 영구히 반영된다.
    - 이전 데이터는 사라져 복구할 수 없다.
- 모든 사용자는 결과를 볼 수 있다.
- 관련된 행에 대한 잠금이 풀리고, 다른 사용자들이 행을 조작할 수 있게 된다.

## Q10. Rollback이 자주 발생하면 시스템에 어떤 영향을 줄 수 있나요?
Rollback이 자주 발생하면 데이터베이스의 성능이 저하될 수 있다. 불필요한 트랜잭션 실행으로 리소스 낭비가 증가하고, 시스템 부하가 늘어나 처리 속도가 느려질 위험이 있다.
