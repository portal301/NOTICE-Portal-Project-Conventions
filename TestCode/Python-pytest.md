# Python Test Code (pytest + uv)

## 철학

> **테스트는 코드의 스펙이다**

- 테스트 코드는 "이 코드가 어떻게 동작해야 하는지"를 문서화한다
- 테스트가 없으면 리팩토링할 수 없다
- CI에서 돌아가지 않는 테스트는 테스트가 아니다

---

## uv (패키지 매니저)

### uv란?

Rust로 작성된 초고속 Python 패키지 매니저. pip, venv, pyenv를 대체합니다.

### 설치

```bash
# Windows (PowerShell)
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"

# Mac/Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 프로젝트 초기화

```bash
# 새 프로젝트 생성
uv init my-project
cd my-project

# Python 버전 지정
uv python pin 3.11

# 가상환경 생성 + 의존성 설치
uv sync
```

### 의존성 관리

```bash
# 프로덕션 의존성 추가
uv add fastapi pydantic

# 개발 의존성 추가 (테스트, 린터 등)
uv add --group dev pytest pytest-cov pytest-asyncio ruff mypy

# 의존성 제거
uv remove package-name

# lock 파일 갱신
uv lock

# 설치
uv sync
```

### 실행

```bash
# 가상환경 내 Python 실행
uv run python main.py

# 가상환경 내 pytest 실행
uv run pytest

# 가상환경 활성화 (선택)
# Windows
.venv\Scripts\activate
# Mac/Linux
source .venv/bin/activate
```

---

## pyproject.toml 설정

```toml
[project]
name = "my-project"
version = "0.1.0"
requires-python = ">=3.11,<3.12"
dependencies = [
    "fastapi>=0.100.0",
    "pydantic>=2.0.0",
]

[dependency-groups]
dev = [
    "pytest>=8.0.0",
    "pytest-asyncio>=0.23.0",
    "pytest-cov>=4.0.0",
    "ruff>=0.1.0",
    "mypy>=1.0.0",
]

[tool.coverage.run]
source = ["src"]
branch = true
omit = ["*/tests/*", "*/__pycache__/*"]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "if TYPE_CHECKING:",
    "raise NotImplementedError",
]
```

---

## pytest.ini 설정

```ini
[pytest]
asyncio_mode = auto
asyncio_default_fixture_loop_scope = function
log_cli = true
log_cli_level = INFO
pythonpath = .
```

| 설정 | 설명 |
|------|------|
| `asyncio_mode = auto` | async 함수 자동 인식 |
| `log_cli = true` | 테스트 중 로그 출력 |
| `pythonpath = .` | 프로젝트 루트에서 import 가능 |

---

## 테스트 실행

```bash
# 전체 테스트
uv run pytest

# 특정 폴더
uv run pytest tests/ci/

# 특정 파일
uv run pytest tests/ci/api/test_session.py

# 특정 함수
uv run pytest tests/ci/api/test_session.py::test_create_session

# 상세 출력
uv run pytest -v

# 커버리지 포함
uv run pytest --cov=src --cov-report=html
```

---

## 프로젝트 구조

```
project/
├── src/                    # 소스 코드
│   ├── api/
│   ├── framework/
│   └── utils/
├── tests/
│   ├── conftest.py         # 공통 fixture
│   ├── ci/                  # CI 자동화 테스트
│   │   ├── api/
│   │   ├── integration/
│   │   └── models/
│   └── manual/              # 수동 실행 테스트 (하드웨어 등)
├── pyproject.toml
├── pytest.ini
└── uv.lock
```

### 테스트 분류 철학

| 폴더 | 용도 | CI 실행 |
|------|------|---------|
| `tests/ci/` | 자동화 테스트 (외부 의존성 없음) | O |
| `tests/manual/` | 하드웨어, 외부 시스템 연동 테스트 | X |
| `tests/integration/` | 통합 테스트 | 선택적 |

---

## conftest.py 활용

### 공통 fixture 정의

```python
# tests/conftest.py
import pytest
from pathlib import Path

# 테스트 상수
TEST_SESSION_NAME = "test_session"

@pytest.fixture(scope="function", autouse=True)
def setup_test_environment():
    """각 테스트 전 환경 설정, 후 정리"""
    # Setup
    create_test_dirs()
    yield
    # Teardown
    cleanup_test_dirs()

@pytest.fixture(autouse=True)
def reset_singletons():
    """싱글톤 객체 리셋 (테스트 격리)"""
    MySingleton._reset_for_testing()
    yield
    MySingleton._reset_for_testing()
```

### Mock 객체 정의

```python
class MockRobot:
    """테스트용 Mock Robot"""
    def __init__(self, name: str = "mock_robot"):
        self.name = name
        self._state = "ready"

    def set_ready(self, ready: bool = True):
        """테스트용: 상태 직접 설정"""
        self._state = "ready" if ready else "error"

@pytest.fixture
def mock_robot():
    return MockRobot()
```

### 비동기 유틸리티

```python
async def wait_for_status(obj, expected_status, timeout: float = 1.0) -> bool:
    """
    상태가 expected_status가 될 때까지 대기
    고정 sleep 대신 명시적 상태 확인으로 flaky test 방지
    """
    import asyncio
    start = asyncio.get_event_loop().time()
    while asyncio.get_event_loop().time() - start < timeout:
        if obj.status == expected_status:
            return True
        await asyncio.sleep(0.01)
    return False
```

---

## Given-When-Then 패턴

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

---

## 비동기 테스트

```python
import pytest

@pytest.mark.asyncio
async def test_async_api_call():
    """비동기 API 호출 테스트"""
    # Given
    client = AsyncClient()

    # When
    result = await client.fetch("/users/1")

    # Then
    assert result["id"] == 1
```

> `asyncio_mode = auto` 설정 시 `@pytest.mark.asyncio` 생략 가능

---

## Fixture

### 기본 Fixture

```python
@pytest.fixture
def account():
    """테스트용 계좌 생성"""
    return Account("TestUser", balance=1000)

def test_withdraw(account):
    account.withdraw(500)
    assert account.balance == 500
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

```python
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

---

## Mock

### 기본 개념

| 용어 | 설명 |
|------|------|
| Mock | 호출 기록을 추적하는 가짜 객체 |
| MagicMock | Mock + 매직 메서드 자동 지원 |
| patch | 특정 객체를 임시로 Mock으로 교체 |
| AsyncMock | 비동기 함수용 Mock |

### 기본 Mock 사용

```python
from unittest.mock import Mock, MagicMock

def test_basic_mock():
    # Mock 객체 생성
    mock_service = Mock()

    # 반환값 설정
    mock_service.get_user.return_value = {"id": 1, "name": "Alice"}

    # 호출
    result = mock_service.get_user(1)

    # 검증
    assert result["name"] == "Alice"
    mock_service.get_user.assert_called_once_with(1)
```

### patch 데코레이터

```python
from unittest.mock import patch

# 방법 1: 데코레이터
@patch("module.external_api.fetch")
def test_with_decorator(mock_fetch):
    mock_fetch.return_value = {"data": "ok"}
    result = my_function()
    assert result == {"data": "ok"}

# 방법 2: context manager
def test_with_context():
    with patch("module.external_api.fetch") as mock_fetch:
        mock_fetch.return_value = {"data": "ok"}
        result = my_function()
        assert result == {"data": "ok"}
```

### patch 경로 규칙

```python
# myapp/service.py
from external_lib import api_client

def fetch_data():
    return api_client.get("/data")

# tests/test_service.py
# patch 경로는 "사용하는 곳" 기준
@patch("myapp.service.api_client")  # O - 사용하는 모듈 기준
@patch("external_lib.api_client")   # X - 정의된 모듈 기준 (동작 안 함)
def test_fetch(mock_client):
    mock_client.get.return_value = {"ok": True}
    result = fetch_data()
    assert result == {"ok": True}
```

### 비동기 Mock

```python
from unittest.mock import AsyncMock, patch

@pytest.mark.asyncio
async def test_async_function():
    with patch("module.async_api", new=AsyncMock(return_value="ok")):
        result = await some_async_function()
        assert result == "ok"

# AsyncMock 직접 생성
async def test_async_mock_direct():
    mock_client = AsyncMock()
    mock_client.fetch.return_value = {"id": 1}

    result = await mock_client.fetch("/users/1")
    assert result == {"id": 1}
```

### side_effect 활용

```python
from unittest.mock import Mock

# 예외 발생
def test_exception():
    mock_api = Mock()
    mock_api.call.side_effect = ValueError("API Error")

    with pytest.raises(ValueError, match="API Error"):
        mock_api.call()

# 순차적 반환값
def test_sequence():
    mock_api = Mock()
    mock_api.call.side_effect = [1, 2, 3]

    assert mock_api.call() == 1
    assert mock_api.call() == 2
    assert mock_api.call() == 3

# 함수로 동적 처리
def test_dynamic():
    def dynamic_return(x):
        return x * 2

    mock_api = Mock()
    mock_api.calculate.side_effect = dynamic_return

    assert mock_api.calculate(5) == 10
```

### 호출 검증

```python
from unittest.mock import Mock, call

mock_api = Mock()
mock_api.send("hello")
mock_api.send("world")

# 호출 여부
mock_api.send.assert_called()              # 한 번 이상 호출됨
mock_api.send.assert_called_once()         # X - 2번 호출됨
mock_api.send.assert_called_with("world")  # 마지막 호출 인자

# 호출 횟수
assert mock_api.send.call_count == 2

# 모든 호출 검증
mock_api.send.assert_has_calls([
    call("hello"),
    call("world")
])
```

### Fixture로 Mock 관리

```python
# conftest.py
import pytest
from unittest.mock import patch, AsyncMock

@pytest.fixture
def mock_database():
    with patch("app.db.connection") as mock:
        mock.query.return_value = []
        yield mock

@pytest.fixture
def mock_external_api():
    with patch("app.api.client", new=AsyncMock()) as mock:
        mock.fetch.return_value = {"status": "ok"}
        yield mock

# test_service.py
def test_with_mocked_db(mock_database):
    mock_database.query.return_value = [{"id": 1}]
    result = get_users()
    assert len(result) == 1
```

### 클래스 Mock (spec 사용)

```python
from unittest.mock import Mock, create_autospec

class RealService:
    def get_data(self, id: int) -> dict:
        pass

    def save(self, data: dict) -> bool:
        pass

# spec으로 인터페이스 강제
def test_with_spec():
    mock_service = create_autospec(RealService)
    mock_service.get_data.return_value = {"id": 1}

    # 존재하지 않는 메서드 호출 시 에러
    # mock_service.undefined_method()  # AttributeError

    result = mock_service.get_data(1)
    assert result == {"id": 1}
```

### PropertyMock

```python
from unittest.mock import PropertyMock, patch

class User:
    @property
    def is_active(self):
        return self._check_status()

def test_property_mock():
    with patch.object(User, "is_active", new_callable=PropertyMock) as mock:
        mock.return_value = True
        user = User()
        assert user.is_active == True
```

---

## 커버리지

```bash
# 기본
uv run pytest --cov=src

# HTML 리포트
uv run pytest --cov=src --cov-report=html

# XML 리포트 (CI용)
uv run pytest --cov=src --cov-report=xml

# 최소 커버리지 강제
uv run pytest --cov=src --cov-fail-under=80
```

---

## 디버깅

```bash
# 실패한 테스트만 재실행
uv run pytest --lf

# print 출력 보기
uv run pytest -s

# 디버거 진입 (실패 시)
uv run pytest --pdb

# 첫 실패 후 중단
uv run pytest -x
```

---

## CI 설정 예시 (GitHub Actions)

```yaml
name: Test

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install uv
        uses: astral-sh/setup-uv@v4

      - name: Set up Python
        run: uv python install 3.11

      - name: Install dependencies
        run: uv sync --all-groups

      - name: Run tests
        run: uv run pytest tests/ci/ --cov=src --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v4
```
