# Repository Naming Convention

## 명명 규칙

```
{ProductName}.{ComponentType}.{TechStack}
```

| 구성 요소 | 규칙 | 예시 |
|-----------|------|------|
| ProductName | 대소문자 혼용 가능, 하이픈 구분 | `Synchro-TR`, `Portal` |
| ComponentType | 소문자 | `gui`, `robot`, `tracker`, `api` |
| TechStack | 소문자 | `electron`, `py`, `unity`, `ts` |

---

## 예시

| 저장소명 | 설명 |
|----------|------|
| `Synchro-TR.gui.electron` | Synchro-TR 프로젝트의 Electron GUI |
| `Synchro-TR.robot.py` | Synchro-TR 프로젝트의 Python 로봇 제어 |
| `Synchro-TR.tracker.unity` | Synchro-TR 프로젝트의 Unity 트래커 |

---

## 금지 사항

- 언더스코어(`_`) 사용 금지 → 하이픈(`-`) 사용
- 공백 사용 금지
- 전체 대문자 사용 금지 (ProductName 제외)

---

<img src="nameing-convention.png">
