# Test Code Convention

## 핵심 원칙

| 원칙 | 설명 |
|------|------|
| Given-When-Then | 상황 → 동작 → 결과 구조로 작성 |
| 단일 테스트 | 하나의 테스트는 하나의 동작만 검증 |
| 명확한 네이밍 | `test_기능_조건_예상결과` 형식 |

---

## 테스트 작성 시점

| 시점 | 방법 | 권장 상황 |
|------|------|----------|
| 개발 전 (TDD) | 테스트 → 실패 → 구현 → 리팩토링 | 요구사항이 명확할 때 |
| 개발 중 | 기능 구현과 테스트 병행 | 일반적인 개발 |
| 버그 수정 시 | 버그 재현 테스트 먼저 작성 | 버그 재발 방지 |

---

## 환경 설정 및 실행

### Python (pytest)

```bash
# 설치
pip install pytest pytest-cov

# 실행
pytest tests/
pytest tests/test_account.py -v          # 특정 파일
pytest --cov=src tests/                   # 커버리지 포함
```

### TypeScript (Jest)

```bash
# 설치
npm install --save-dev jest @types/jest ts-jest

# 설정 (jest.config.js)
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'node',
};

# 실행
npx jest
npx jest --coverage
```

### C# (xUnit)

```bash
# 설치
dotnet add package xunit
dotnet add package xunit.runner.visualstudio

# 실행
dotnet test
dotnet test --collect:"XPlat Code Coverage"
```

### Unity (Unity Test Framework)

1. Package Manager → Unity Test Framework 설치
2. Window → General → Test Runner
3. EditMode / PlayMode 테스트 작성

---

## Given-When-Then 패턴

```
Given: 사전 조건 (테스트 준비)
When:  테스트할 동작
Then:  기대 결과 검증
```

### Python 예시

```python
def test_account_deposit():
    """100원 입금 시 잔고가 100원이 되어야 한다"""
    # Given
    account = Account("Alice")

    # When
    account.deposit(100)

    # Then
    assert account.balance == 100
```

### TypeScript 예시

```typescript
describe('Account', () => {
  it('should increase balance when deposit', () => {
    // Given
    const account = new Account('Alice');

    // When
    account.deposit(100);

    // Then
    expect(account.balance).toBe(100);
  });
});
```

### C# 예시

```csharp
public class AccountTests
{
    [Fact]
    public void Deposit_ShouldIncreaseBalance()
    {
        // Given
        var account = new Account("Alice");

        // When
        account.Deposit(100);

        // Then
        Assert.Equal(100, account.Balance);
    }
}
```

---

## 테스트 네이밍

### 함수명 패턴

```
test_{기능}_{조건}_{예상결과}
```

**예시:**
- `test_login_with_valid_credentials_returns_token`
- `test_withdraw_exceeds_balance_throws_error`
- `test_create_user_with_duplicate_email_fails`

### 한글 docstring 활용

```python
def test_account_overdraft():
    """잔고보다 큰 금액 출금 시 ValueError 발생"""
    ...
```

---

## 테스트 구조

```
tests/
├── unit/           # 단위 테스트
├── integration/    # 통합 테스트
└── e2e/            # End-to-End 테스트
```

| 유형 | 범위 | 속도 | 예시 |
|------|------|------|------|
| Unit | 함수/클래스 단위 | 빠름 | 계산 로직 검증 |
| Integration | 모듈 간 연동 | 보통 | DB 연동 테스트 |
| E2E | 전체 시스템 | 느림 | 사용자 시나리오 |
