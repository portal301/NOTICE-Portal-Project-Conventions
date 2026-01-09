# Git Behavior Convention

## 커밋 규칙

| 항목 | 규칙 |
|------|------|
| 언어 | 한글 |
| 크기 | 약 50줄 추가/삭제 (100줄 이내 변경) |
| 도구 | `git cz` (commitizen) 권장 |

### commitizen 설치

```bash
npm install -g commitizen cz-conventional-changelog cz-emoji-conventional cz-emoji

# Windows
echo { "path": "cz-emoji-conventional" } > %USERPROFILE%\.czrc

# Mac/Linux
echo '{ "path": "cz-emoji-conventional" }' > ~/.czrc
```

### commitizen 사용

```bash
git add .
git cz
```

1. **타입 선택**: 방향키로 작업 타입 선택
2. **스코프**: 변경된 대표 파일명
3. **설명**: 변경 내용 간략히 작성
4. **이슈 연결**: GitHub Issue 번호 입력 (선택)

---

## 브랜치 전략

### 기본 (소규모 프로젝트)

```
main ← Feature-*
```

| 브랜치 | 용도 |
|--------|------|
| `main` | 배포 가능한 안정 버전 |
| `Feature-*` | 기능 개발 (예: `Feature-Login`) |

### Git-flow (대규모 프로젝트)

```
main ← release/* ← develop ← feature/*
                           ← hotfix/*
```

| 브랜치 | 용도 |
|--------|------|
| `main` | 배포 버전 |
| `develop` | 개발 통합 |
| `feature/*` | 기능 개발 |
| `release/*` | 릴리스 준비 |
| `hotfix/*` | 긴급 버그 수정 |

---

## Pull Request

[PR Convention 문서](./PR%20Convention.md) 참고

---

## Issue 작성

### 테스트 Issue 유형

| 유형 | 설명 |
|------|------|
| Task Test | 단일 기능 테스트 |
| Acceptance Test | 통합 기능 테스트 |

### 작성 원칙
- 개발자가 아니어도 따라할 수 있게 작성
- 전문 용어 대신 **Plain Language** 사용

---

## Git 로그 보기 (선택)

`~/.gitconfig`에 추가:

```ini
[alias]
lg = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%s%C(reset) %C(dim white)- %an%C(reset)%C(auto)%d%C(reset)' --all
```

사용: `git lg`
