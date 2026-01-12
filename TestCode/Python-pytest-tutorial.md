# Python pytest 튜토리얼

> 복사해서 붙여넣고 실행하면 됩니다

---

## 1. 설치

### uv 설치 (패키지 매니저)

**Windows (PowerShell):**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**Mac/Linux:**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

---

## 2. 프로젝트 생성

```bash
uv init pytest-tutorial
cd pytest-tutorial
uv python pin 3.11
uv add --group dev pytest
```

---

## 3. 폴더 구조 만들기

**Windows:**
```powershell
mkdir src
mkdir tests
New-Item src/calculator.py
New-Item tests/test_calculator.py
New-Item pytest.ini
```

**Mac/Linux:**
```bash
mkdir src tests
touch src/calculator.py tests/test_calculator.py pytest.ini
```

결과:
```
pytest-tutorial/
├── src/
│   └── calculator.py
├── tests/
│   └── test_calculator.py
└── pytest.ini
```

---

## 4. pytest.ini 설정

`pytest.ini`:

```ini
[pytest]
pythonpath = .
```

---

## 5. 테스트할 코드 작성

`src/calculator.py`:

```python
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    if b == 0:
        raise ValueError("0으로 나눌 수 없습니다")
    return a / b

def get_grade(score):
    if score >= 90:
        return "A"
    elif score >= 80:
        return "B"
    elif score >= 70:
        return "C"
    elif score >= 60:
        return "D"
    else:
        return "F"

def is_adult(age):
    return age >= 18

def get_user_info():
    return {
        "name": "홍길동",
        "age": 25,
        "email": "hong@example.com",
        "roles": ["user", "admin"]
    }
```

---

## 6. Given-When-Then 패턴

`tests/test_given_when_then.py`:

```python
from src.calculator import add, subtract, get_grade, is_adult, get_user_info

# ============================================
# Given-When-Then 기본
# ============================================

def test_add_two_numbers():
    # Given: 두 숫자가 주어졌을 때
    a = 10
    b = 5

    # When: 더하기를 실행하면
    result = add(a, b)

    # Then: 합이 나온다
    assert result == 15


def test_subtract_two_numbers():
    # Given
    a = 10
    b = 3

    # When
    result = subtract(a, b)

    # Then
    assert result == 7


def test_grade_for_90_score():
    # Given: 90점 이상이면
    score = 95

    # When: 성적을 계산하면
    grade = get_grade(score)

    # Then: A등급이 나온다
    assert grade == "A"


def test_adult_check():
    # Given: 18세 이상이면
    age = 20

    # When: 성인 여부 확인
    result = is_adult(age)

    # Then: True
    assert result == True
```

---

## 7. 다양한 assert 연습

`tests/test_assert_types.py`:

```python
import pytest
from src.calculator import (
    add, divide, get_grade, is_adult, get_user_info
)

# ============================================
# 기본 비교
# ============================================

def test_equal():
    """같다"""
    assert add(1, 2) == 3

def test_not_equal():
    """다르다"""
    assert add(1, 2) != 10

def test_greater_than():
    """크다"""
    assert add(5, 5) > 9

def test_greater_than_or_equal():
    """크거나 같다"""
    assert add(5, 5) >= 10

def test_less_than():
    """작다"""
    assert add(1, 1) < 10

def test_less_than_or_equal():
    """작거나 같다"""
    assert add(1, 1) <= 2


# ============================================
# 불리언 체크
# ============================================

def test_is_true():
    """True인지"""
    assert is_adult(20) is True

def test_is_false():
    """False인지"""
    assert is_adult(10) is False

def test_truthy():
    """참인 값인지 (1, "text", [1,2] 등)"""
    result = "hello"
    assert result  # 빈 문자열이 아니면 통과

def test_falsy():
    """거짓인 값인지 (0, "", [], None 등)"""
    result = ""
    assert not result  # 빈 문자열이면 통과


# ============================================
# None 체크
# ============================================

def test_is_none():
    """None인지"""
    result = None
    assert result is None

def test_is_not_none():
    """None이 아닌지"""
    result = "hello"
    assert result is not None


# ============================================
# 문자열 체크
# ============================================

def test_string_contains():
    """문자열에 포함되어 있는지"""
    message = "Hello World"
    assert "World" in message

def test_string_not_contains():
    """문자열에 포함되지 않았는지"""
    message = "Hello World"
    assert "Python" not in message

def test_string_startswith():
    """문자열이 특정 문자로 시작하는지"""
    message = "Hello World"
    assert message.startswith("Hello")

def test_string_endswith():
    """문자열이 특정 문자로 끝나는지"""
    message = "Hello World"
    assert message.endswith("World")


# ============================================
# 리스트/딕셔너리 체크
# ============================================

def test_list_contains():
    """리스트에 포함되어 있는지"""
    user = get_user_info()
    assert "admin" in user["roles"]

def test_list_length():
    """리스트 길이"""
    user = get_user_info()
    assert len(user["roles"]) == 2

def test_dict_has_key():
    """딕셔너리에 키가 있는지"""
    user = get_user_info()
    assert "email" in user

def test_dict_value():
    """딕셔너리 값 확인"""
    user = get_user_info()
    assert user["name"] == "홍길동"


# ============================================
# 타입 체크
# ============================================

def test_is_instance():
    """특정 타입인지"""
    result = add(1, 2)
    assert isinstance(result, int)

def test_is_instance_string():
    """문자열 타입인지"""
    grade = get_grade(95)
    assert isinstance(grade, str)

def test_is_instance_list():
    """리스트 타입인지"""
    user = get_user_info()
    assert isinstance(user["roles"], list)


# ============================================
# 예외 체크
# ============================================

def test_raises_exception():
    """예외가 발생하는지"""
    with pytest.raises(ValueError):
        divide(10, 0)

def test_raises_with_message():
    """특정 메시지로 예외가 발생하는지"""
    with pytest.raises(ValueError, match="0으로 나눌 수 없습니다"):
        divide(10, 0)

def test_raises_any_exception():
    """어떤 예외든 발생하는지"""
    with pytest.raises(Exception):
        divide(10, 0)


# ============================================
# 근사값 체크 (float)
# ============================================

def test_float_almost_equal():
    """실수 근사값 비교"""
    result = divide(10, 3)
    assert result == pytest.approx(3.333, rel=0.01)  # 1% 오차 허용

def test_float_exact():
    """실수 정확히 비교"""
    result = divide(10, 2)
    assert result == 5.0
```

---

## 8. Parametrize (여러 케이스 한번에)

`tests/test_parametrize.py`:

```python
import pytest
from src.calculator import add, get_grade, is_adult

@pytest.mark.parametrize("a, b, expected", [
    (1, 2, 3),
    (0, 0, 0),
    (-1, 1, 0),
    (100, 200, 300),
    (-5, -5, -10),
])
def test_add_cases(a, b, expected):
    assert add(a, b) == expected


@pytest.mark.parametrize("score, expected_grade", [
    (100, "A"),
    (95, "A"),
    (90, "A"),
    (89, "B"),
    (80, "B"),
    (79, "C"),
    (70, "C"),
    (69, "D"),
    (60, "D"),
    (59, "F"),
    (0, "F"),
])
def test_grade_cases(score, expected_grade):
    assert get_grade(score) == expected_grade


@pytest.mark.parametrize("age, expected", [
    (18, True),
    (19, True),
    (17, False),
    (0, False),
    (100, True),
])
def test_adult_cases(age, expected):
    assert is_adult(age) == expected
```

---

## 9. Fixture

`tests/conftest.py`:

```python
import pytest

class Account:
    def __init__(self, owner: str):
        self.owner = owner
        self.balance = 0

    def deposit(self, amount: int):
        self.balance += amount

    def withdraw(self, amount: int):
        if amount > self.balance:
            raise ValueError("잔고 부족")
        self.balance -= amount


@pytest.fixture
def account():
    """빈 계좌"""
    return Account("홍길동")


@pytest.fixture
def funded_account():
    """1000원 있는 계좌"""
    acc = Account("홍길동")
    acc.deposit(1000)
    return acc


@pytest.fixture
def sample_users():
    """샘플 사용자 목록"""
    return [
        {"id": 1, "name": "홍길동", "age": 25},
        {"id": 2, "name": "김철수", "age": 17},
        {"id": 3, "name": "이영희", "age": 30},
    ]
```

`tests/test_fixture.py`:

```python
def test_empty_account(account):
    # Given: 빈 계좌
    # When & Then
    assert account.balance == 0


def test_deposit(account):
    # Given: 빈 계좌
    # When: 입금
    account.deposit(500)

    # Then
    assert account.balance == 500


def test_withdraw(funded_account):
    # Given: 1000원 있는 계좌
    # When: 300원 출금
    funded_account.withdraw(300)

    # Then
    assert funded_account.balance == 700


def test_filter_adults(sample_users):
    # Given: 샘플 사용자들
    # When: 성인만 필터
    adults = [u for u in sample_users if u["age"] >= 18]

    # Then
    assert len(adults) == 2
    assert adults[0]["name"] == "홍길동"
    assert adults[1]["name"] == "이영희"
```

---

## 10. Mock 기초

`src/user_service.py`:

```python
import requests

def fetch_user(user_id: int) -> dict:
    """외부 API에서 사용자 정보 가져오기"""
    response = requests.get(f"https://api.example.com/users/{user_id}")
    return response.json()

def get_user_name(user_id: int) -> str:
    """사용자 이름만 가져오기"""
    user = fetch_user(user_id)
    return user["name"]

def is_user_active(user_id: int) -> bool:
    """사용자가 활성 상태인지"""
    user = fetch_user(user_id)
    return user.get("active", False)
```

`tests/test_mock_basic.py`:

```python
from unittest.mock import patch, Mock
from src.user_service import get_user_name, is_user_active

# ============================================
# 기본 Mock
# ============================================

def test_get_user_name():
    """외부 API 호출을 Mock으로 대체"""

    # Given: fetch_user가 이 값을 반환하도록 설정
    with patch("src.user_service.fetch_user") as mock_fetch:
        mock_fetch.return_value = {"id": 1, "name": "홍길동", "active": True}

        # When
        name = get_user_name(1)

        # Then
        assert name == "홍길동"
        mock_fetch.assert_called_once_with(1)  # 1로 호출되었는지 확인


def test_is_user_active_true():
    with patch("src.user_service.fetch_user") as mock_fetch:
        mock_fetch.return_value = {"id": 1, "name": "홍길동", "active": True}

        result = is_user_active(1)

        assert result == True


def test_is_user_active_false():
    with patch("src.user_service.fetch_user") as mock_fetch:
        mock_fetch.return_value = {"id": 1, "name": "김철수", "active": False}

        result = is_user_active(1)

        assert result == False


# ============================================
# 데코레이터로 Mock
# ============================================

@patch("src.user_service.fetch_user")
def test_with_decorator(mock_fetch):
    mock_fetch.return_value = {"id": 1, "name": "데코레이터"}

    name = get_user_name(1)

    assert name == "데코레이터"


# ============================================
# 여러 함수 Mock
# ============================================

@patch("src.user_service.fetch_user")
def test_multiple_calls(mock_fetch):
    """여러 번 호출될 때"""
    # 호출할 때마다 다른 값 반환
    mock_fetch.side_effect = [
        {"id": 1, "name": "첫번째"},
        {"id": 2, "name": "두번째"},
    ]

    name1 = get_user_name(1)
    name2 = get_user_name(2)

    assert name1 == "첫번째"
    assert name2 == "두번째"
    assert mock_fetch.call_count == 2  # 2번 호출됨
```

---

## 11. Mock 심화

`src/order_service.py`:

```python
from datetime import datetime

class Database:
    def save(self, data: dict) -> int:
        """DB에 저장하고 ID 반환"""
        raise NotImplementedError("실제 DB 연결 필요")

    def find_by_id(self, id: int) -> dict:
        """ID로 조회"""
        raise NotImplementedError("실제 DB 연결 필요")

class EmailService:
    def send(self, to: str, subject: str, body: str) -> bool:
        """이메일 발송"""
        raise NotImplementedError("실제 이메일 서버 필요")

class PaymentGateway:
    def charge(self, amount: int, card_number: str) -> dict:
        """결제 처리"""
        raise NotImplementedError("실제 결제 서버 필요")


class OrderService:
    def __init__(self, db: Database, email: EmailService, payment: PaymentGateway):
        self.db = db
        self.email = email
        self.payment = payment

    def create_order(self, user_email: str, amount: int, card_number: str) -> dict:
        # 1. 결제
        payment_result = self.payment.charge(amount, card_number)
        if not payment_result["success"]:
            raise ValueError("결제 실패")

        # 2. DB 저장
        order_id = self.db.save({
            "email": user_email,
            "amount": amount,
            "payment_id": payment_result["payment_id"],
            "created_at": datetime.now().isoformat()
        })

        # 3. 이메일 발송
        self.email.send(
            to=user_email,
            subject="주문 완료",
            body=f"주문번호: {order_id}"
        )

        return {"order_id": order_id, "status": "completed"}
```

`tests/test_mock_advanced.py`:

```python
from unittest.mock import Mock, patch, MagicMock, call
import pytest
from src.order_service import OrderService, Database, EmailService, PaymentGateway

# ============================================
# 클래스 Mock
# ============================================

def test_create_order_success():
    """주문 생성 성공 케이스"""

    # Given: 모든 의존성을 Mock으로 생성
    mock_db = Mock(spec=Database)
    mock_email = Mock(spec=EmailService)
    mock_payment = Mock(spec=PaymentGateway)

    # Mock 반환값 설정
    mock_payment.charge.return_value = {"success": True, "payment_id": "PAY123"}
    mock_db.save.return_value = 42  # 주문 ID

    service = OrderService(mock_db, mock_email, mock_payment)

    # When
    result = service.create_order(
        user_email="test@example.com",
        amount=10000,
        card_number="1234-5678"
    )

    # Then
    assert result["order_id"] == 42
    assert result["status"] == "completed"

    # 각 Mock이 올바르게 호출되었는지 검증
    mock_payment.charge.assert_called_once_with(10000, "1234-5678")
    mock_db.save.assert_called_once()
    mock_email.send.assert_called_once_with(
        to="test@example.com",
        subject="주문 완료",
        body="주문번호: 42"
    )


def test_create_order_payment_failed():
    """결제 실패 케이스"""

    # Given
    mock_db = Mock(spec=Database)
    mock_email = Mock(spec=EmailService)
    mock_payment = Mock(spec=PaymentGateway)

    # 결제 실패 설정
    mock_payment.charge.return_value = {"success": False}

    service = OrderService(mock_db, mock_email, mock_payment)

    # When & Then
    with pytest.raises(ValueError, match="결제 실패"):
        service.create_order("test@example.com", 10000, "1234-5678")

    # 결제 실패 시 DB 저장, 이메일 발송 안 됨
    mock_db.save.assert_not_called()
    mock_email.send.assert_not_called()


# ============================================
# side_effect로 예외 발생
# ============================================

def test_payment_gateway_timeout():
    """결제 서버 타임아웃"""

    mock_db = Mock(spec=Database)
    mock_email = Mock(spec=EmailService)
    mock_payment = Mock(spec=PaymentGateway)

    # 결제 시 예외 발생
    mock_payment.charge.side_effect = TimeoutError("결제 서버 응답 없음")

    service = OrderService(mock_db, mock_email, mock_payment)

    with pytest.raises(TimeoutError):
        service.create_order("test@example.com", 10000, "1234-5678")


# ============================================
# 호출 순서 검증
# ============================================

def test_call_order():
    """호출 순서 확인"""

    mock_db = Mock(spec=Database)
    mock_email = Mock(spec=EmailService)
    mock_payment = Mock(spec=PaymentGateway)

    mock_payment.charge.return_value = {"success": True, "payment_id": "PAY123"}
    mock_db.save.return_value = 1

    service = OrderService(mock_db, mock_email, mock_payment)
    service.create_order("test@example.com", 10000, "1234-5678")

    # 호출 순서: 결제 -> DB 저장 -> 이메일
    # MagicMock의 호출 기록 확인
    assert mock_payment.charge.called
    assert mock_db.save.called
    assert mock_email.send.called


# ============================================
# 호출 인자 상세 검증
# ============================================

def test_verify_call_arguments():
    """호출된 인자 상세 확인"""

    mock_db = Mock(spec=Database)
    mock_email = Mock(spec=EmailService)
    mock_payment = Mock(spec=PaymentGateway)

    mock_payment.charge.return_value = {"success": True, "payment_id": "PAY123"}
    mock_db.save.return_value = 99

    service = OrderService(mock_db, mock_email, mock_payment)
    service.create_order("hong@test.com", 50000, "9999-8888")

    # save에 전달된 인자 확인
    save_call_args = mock_db.save.call_args[0][0]  # 첫 번째 인자
    assert save_call_args["email"] == "hong@test.com"
    assert save_call_args["amount"] == 50000
    assert save_call_args["payment_id"] == "PAY123"


# ============================================
# MagicMock (매직 메서드 자동 지원)
# ============================================

def test_magic_mock():
    """MagicMock은 __len__, __iter__ 등 자동 지원"""

    mock_list = MagicMock()
    mock_list.__len__.return_value = 5
    mock_list.__getitem__.return_value = "item"

    assert len(mock_list) == 5
    assert mock_list[0] == "item"


# ============================================
# patch.object (특정 객체의 메서드만 Mock)
# ============================================

def test_patch_object():
    """객체의 특정 메서드만 Mock"""

    db = Database()

    with patch.object(db, 'save', return_value=123):
        result = db.save({"data": "test"})
        assert result == 123


# ============================================
# 여러 patch 중첩
# ============================================

@patch("src.order_service.PaymentGateway")
@patch("src.order_service.EmailService")
@patch("src.order_service.Database")
def test_with_multiple_patches(MockDB, MockEmail, MockPayment):
    """여러 클래스 한번에 Mock"""

    # 인스턴스의 메서드 설정
    MockPayment.return_value.charge.return_value = {"success": True, "payment_id": "P1"}
    MockDB.return_value.save.return_value = 1

    service = OrderService(
        MockDB.return_value,
        MockEmail.return_value,
        MockPayment.return_value
    )

    result = service.create_order("test@test.com", 1000, "1111-2222")

    assert result["order_id"] == 1
```

---

## 12. 비동기 테스트 + Mock

설치:
```bash
uv add --group dev pytest-asyncio
```

`pytest.ini`:
```ini
[pytest]
pythonpath = .
asyncio_mode = auto
```

`src/async_api.py`:

```python
import aiohttp

async def fetch_user_async(user_id: int) -> dict:
    """비동기로 사용자 정보 조회"""
    async with aiohttp.ClientSession() as session:
        async with session.get(f"https://api.example.com/users/{user_id}") as resp:
            return await resp.json()

async def get_user_email_async(user_id: int) -> str:
    """비동기로 사용자 이메일만 조회"""
    user = await fetch_user_async(user_id)
    return user["email"]
```

`tests/test_async_mock.py`:

```python
import pytest
from unittest.mock import AsyncMock, patch
from src.async_api import get_user_email_async

@pytest.mark.asyncio
async def test_async_function():
    """비동기 함수 Mock"""

    with patch("src.async_api.fetch_user_async", new=AsyncMock()) as mock_fetch:
        mock_fetch.return_value = {
            "id": 1,
            "name": "홍길동",
            "email": "hong@example.com"
        }

        result = await get_user_email_async(1)

        assert result == "hong@example.com"
        mock_fetch.assert_called_once_with(1)


@pytest.mark.asyncio
async def test_async_exception():
    """비동기 함수에서 예외 발생"""

    with patch("src.async_api.fetch_user_async", new=AsyncMock()) as mock_fetch:
        mock_fetch.side_effect = ConnectionError("서버 연결 실패")

        with pytest.raises(ConnectionError):
            await get_user_email_async(1)


@pytest.mark.asyncio
async def test_async_multiple_calls():
    """비동기 함수 여러 번 호출"""

    with patch("src.async_api.fetch_user_async", new=AsyncMock()) as mock_fetch:
        mock_fetch.side_effect = [
            {"id": 1, "email": "first@test.com"},
            {"id": 2, "email": "second@test.com"},
        ]

        email1 = await get_user_email_async(1)
        email2 = await get_user_email_async(2)

        assert email1 == "first@test.com"
        assert email2 == "second@test.com"
```

---

## 자주 쓰는 명령어

```bash
uv run pytest                              # 전체 실행
uv run pytest -v                           # 상세 출력
uv run pytest tests/test_calculator.py     # 특정 파일
uv run pytest -k "add"                     # 이름에 add 포함된 것만
uv run pytest -x                           # 첫 실패에서 중단
uv run pytest --lf                         # 실패한 것만 재실행
uv run pytest -s                           # print 출력 보기
uv run pytest --cov=src                    # 커버리지
```

---

## Mock 요약 표

| 용도 | 코드 |
|------|------|
| 함수 Mock | `patch("module.function")` |
| 반환값 설정 | `mock.return_value = value` |
| 예외 발생 | `mock.side_effect = Exception()` |
| 여러 반환값 | `mock.side_effect = [val1, val2]` |
| 호출 확인 | `mock.assert_called_once_with(arg)` |
| 호출 안됨 확인 | `mock.assert_not_called()` |
| 호출 횟수 | `mock.call_count` |
| 비동기 Mock | `AsyncMock()` |
| 클래스 Mock | `Mock(spec=ClassName)` |
| 매직메서드 | `MagicMock()` |

---

## 다음 문서

- [Python-pytest.md](./Python-pytest.md) - 상세 레퍼런스
