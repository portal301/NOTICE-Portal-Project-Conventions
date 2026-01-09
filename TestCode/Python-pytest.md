# Python Test Code (pytest)

## 설치

```bash
pip install pytest pytest-cov
```

---

## 실행

```bash
# 전체 테스트
pytest

# 특정 파일
pytest tests/test_user.py

# 특정 함수
pytest tests/test_user.py::test_login

# 상세 출력
pytest -v

# 실패 시 즉시 중단
pytest -x

# 커버리지 포함
pytest --cov=src tests/
```

---

## 프로젝트 구조

```
project/
├── src/
│   └── account.py
├── tests/
│   ├── __init__.py
│   ├── conftest.py      # 공통 fixture
│   ├── test_account.py
│   └── test_user.py
├── pytest.ini           # pytest 설정
└── requirements.txt
```

---

## pytest.ini 설정

```ini
[pytest]
testpaths = tests
python_files = test_*.py
python_functions = test_*
addopts = -v --tb=short
```

---

## 기본 테스트 작성

### Given-When-Then 패턴

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

### 예외 테스트

```python
import pytest

def test_withdraw_insufficient_funds():
    """잔고 부족 시 ValueError 발생"""
    account = Account("Bob", balance=50)

    with pytest.raises(ValueError, match="Insufficient funds"):
        account.withdraw(100)
```

---

## Fixture

테스트에 필요한 객체를 재사용 가능하게 정의

### 기본 Fixture

```python
import pytest

@pytest.fixture
def account():
    """테스트용 계좌 생성"""
    return Account("TestUser", balance=1000)

def test_withdraw(account):
    account.withdraw(500)
    assert account.balance == 500
```

### conftest.py (공통 Fixture)

`tests/conftest.py`에 정의하면 모든 테스트에서 사용 가능

```python
# tests/conftest.py
import pytest

@pytest.fixture
def db_connection():
    """테스트용 DB 연결"""
    conn = create_test_db()
    yield conn
    conn.close()

@pytest.fixture
def sample_user(db_connection):
    """테스트용 사용자"""
    return User.create(db_connection, name="Test")
```

### Fixture Scope

```python
@pytest.fixture(scope="function")  # 기본값, 매 테스트마다 생성
@pytest.fixture(scope="class")     # 클래스당 1회
@pytest.fixture(scope="module")    # 파일당 1회
@pytest.fixture(scope="session")   # 전체 테스트당 1회
```

---

## Parametrize

여러 입력값으로 같은 테스트 반복

```python
import pytest

@pytest.mark.parametrize("amount,expected", [
    (100, 100),
    (200, 200),
    (0, 0),
])
def test_deposit_amounts(amount, expected):
    account = Account("Alice")
    account.deposit(amount)
    assert account.balance == expected
```

### 여러 파라미터 조합

```python
@pytest.mark.parametrize("x", [1, 2])
@pytest.mark.parametrize("y", [10, 20])
def test_multiply(x, y):
    # (1,10), (1,20), (2,10), (2,20) 4가지 조합 테스트
    assert x * y > 0
```

---

## Mark (테스트 분류)

```python
import pytest

@pytest.mark.slow
def test_heavy_computation():
    """시간이 오래 걸리는 테스트"""
    pass

@pytest.mark.skip(reason="아직 구현 안됨")
def test_future_feature():
    pass

@pytest.mark.skipif(sys.platform == "win32", reason="Linux only")
def test_linux_only():
    pass
```

### 특정 Mark만 실행

```bash
pytest -m slow           # slow 마크만
pytest -m "not slow"     # slow 제외
```

---

## Mock

외부 의존성 대체

```python
from unittest.mock import Mock, patch

def test_api_call():
    """외부 API 호출 모킹"""
    with patch("requests.get") as mock_get:
        mock_get.return_value.status_code = 200
        mock_get.return_value.json.return_value = {"id": 1}

        result = fetch_user(1)

        assert result["id"] == 1
        mock_get.assert_called_once()
```

### Fixture로 Mock 관리

```python
@pytest.fixture
def mock_db():
    with patch("app.database.connect") as mock:
        mock.return_value = FakeDB()
        yield mock
```

---

## 커버리지

### 실행

```bash
# 기본
pytest --cov=src tests/

# HTML 리포트
pytest --cov=src --cov-report=html tests/

# 최소 커버리지 강제
pytest --cov=src --cov-fail-under=80 tests/
```

### .coveragerc 설정

```ini
[run]
source = src
omit =
    */tests/*
    */__init__.py

[report]
exclude_lines =
    pragma: no cover
    if __name__ == .__main__.:
```

---

## 자주 사용하는 Assert

```python
# 값 비교
assert result == expected
assert result != unexpected
assert result > 0

# 컬렉션
assert item in collection
assert len(items) == 3
assert all(x > 0 for x in items)

# 타입
assert isinstance(obj, MyClass)

# None
assert result is None
assert result is not None

# 근사값 (float)
assert result == pytest.approx(3.14, rel=1e-3)
```

---

## 디버깅 팁

```bash
# 실패한 테스트만 재실행
pytest --lf

# print 출력 보기
pytest -s

# 디버거 진입 (실패 시)
pytest --pdb

# 첫 실패 후 디버거
pytest -x --pdb
```
