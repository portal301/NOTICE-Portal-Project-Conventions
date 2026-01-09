# Pull Request Convention

## 핵심 규칙

| 항목 | 규칙 |
|------|------|
| PR 크기 | 빌드 파일 제외 **+500/-500 이내** |
| Merge 방식 | **Squash & Merge** 권장 |
| Merge 후 | 원격 브랜치 삭제 |

---

## PR 메시지 작성법

### 제목
```
[타입] 간결한 설명
```

| 타입 | 설명 |
|------|------|
| `Feature` | 새로운 기능 |
| `Fix` | 버그 수정 |
| `Refactor` | 코드 리팩토링 |
| `Docs` | 문서 수정 |
| `Test` | 테스트 추가/수정 |
| `Chore` | 빌드, 설정 등 기타 |

**예시:**
- `[Feature] 사용자 로그인 기능 추가`
- `[Fix] 결제 금액 계산 오류 수정`
- `[Refactor] API 응답 처리 로직 개선`

### 본문

```markdown
## 변경 사항
- 무엇을, 왜 변경했는지

## 테스트 방법
- 리뷰어가 확인할 수 있는 방법

## 관련 이슈
- Closes #123
```

---

## PR 프로세스

### 1. 등록
1. GitHub에서 **Pull Request** 클릭
2. `Base Branch` (목적지) ← `Compare Branch` (작업) 설정
3. 제목, 본문 작성
4. **Reviewer**, **Assignee** 지정
5. **Create Pull Request** 클릭

### 2. 리뷰
- 파일별로 확인 후 `Viewed` 체크
- 라인별 코멘트: 해당 줄의 `+` 버튼 클릭
- 완료 시 `Review Changes` → `Approve`

### 3. Merge
1. **Squash and Merge** 클릭
2. 원격 브랜치 삭제

---

## Squash & Merge 이후

> Squash & Merge는 커밋을 합쳐서 히스토리를 정리하지만, 브랜치 간 기준점이 달라져 충돌이 발생할 수 있습니다.

### 필수 작업

```bash
# 1. develop 브랜치를 main 기준으로 rebase
git fetch origin
git checkout develop
git rebase origin/main
git push --force-with-lease origin develop

# 2. 작업 브랜치를 develop 기준으로 rebase
git checkout feature/<branch-name>
git rebase origin/develop
git push --force-with-lease origin feature/<branch-name>
```

### 주의사항
- DLL/바이너리는 PR에 포함하지 말 것 → CI에서 빌드 권장
- rebase 주기를 짧게 가져가면 충돌 감소

---

## PR 템플릿 설정

`.github/PULL_REQUEST_TEMPLATE.md` 파일 생성:

```markdown
## 변경 사항
-

## 테스트 방법
-

## 관련 이슈
- Closes #

## 스크린샷 (선택)

```
