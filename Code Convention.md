# Code Convention

## 기본 원칙

각 언어의 공식 스타일 가이드를 따릅니다.

| 언어 | 스타일 가이드 |
|------|--------------|
| JavaScript/TypeScript | [Google TS Style Guide](https://google.github.io/styleguide/tsguide.html) |
| Python | [PEP 8](https://peps.python.org/pep-0008) |
| C# / Unity | [Microsoft C# Coding Conventions](https://learn.microsoft.com/ko-kr/dotnet/csharp/fundamentals/coding-style/coding-conventions) |

---

## 추가 규칙

### 1. 타입 명시

타입 추론이 가능한 언어(TypeScript, Python, C# 등)에서도 **타입을 명시**합니다.

```csharp
// Bad
var relativePath = SyncRoURIs.UR5e_BASE;
var gltfImport = new GltfImport();

// Good
string relativePath = SyncRoURIs.UR5e_BASE;
GltfImport gltfImport = new GltfImport();
```

### 2. 불필요한 코드 제거

- 주석 처리된 코드 삭제
- 사용하지 않는 import/using 제거

```csharp
// Bad - 주석 처리된 코드
//int Fibonacci(int n)
//{
//    if (n <= 1) return n;
//    return Fibonacci(n - 1) + Fibonacci(n - 2);
//}

// Good - 삭제
```
