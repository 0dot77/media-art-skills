---
name: timeline
description: "Production timeline generator for media art projects. Use when the user wants to create a project schedule with phases, milestones, and dependencies. Triggers on /timeline or requests for 일정표, 일정, 스케줄, timeline, schedule."
---

# /timeline — 제작 일정표

> **Role**: Production Manager
> **Trigger**: User invokes `/timeline` or asks about 일정표, 스케줄, timeline
> **Output**: `{프로젝트 폴더}/TIMELINE.docx` + `TIMELINE.md`

## Purpose

미디어아트 프로젝트의 제작 일정표를 생성한다. 사전제작→제작→테스트→설치→운영→철거의 6단계 프레임워크를 기반으로 마일스톤과 태스크를 배치한다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요?"

### 1. Context Check

`{프로젝트 폴더}`에서 확인:
- `PROJECT.md` — 전체 기간, 데드라인
- `TECH-SPEC.md` — 장비 리드타임, 제작 복잡도
- `ESTIMATE.md` — 외주 항목 (외주 일정 반영)
- `TIMELINE.md` — 이미 있으면 업데이트할지 질문

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 전체 기간**
> "프로젝트 전체 기간을 알려주세요. (시작일 ~ 오픈일)"

**Q2 — 핵심 마일스톤**
> "반드시 지켜야 하는 마일스톤이 있나요?"

클라이언트 리뷰, 중간 발표, 장비 입고일, 설치 시작일 등.

**Q3 — 외주/조달 항목**
> "외주나 장비 조달에 리드타임이 필요한 항목이 있나요?"

ESTIMATE.md의 외주 항목이 있으면 자동으로 나열하고 리드타임만 입력받는다.

**Q4 — 제약 조건**
> "일정 관련 제약 조건이 있나요?"

현장 사용 가능 기간, 병행 프로젝트, 인력 가용성 등.

### 3. Timeline Generation

6단계 프레임워크에 입력된 기간을 배분한다:

| Phase | 일반적 비율 | 내용 |
|-------|-----------|------|
| 1. Pre-production | 15-20% | 기획, 설계, 발주 |
| 2. Production | 30-40% | 콘텐츠 제작, 개발, 가공 |
| 3. Testing | 10-15% | 사내 테스트, 리허설 |
| 4. Installation | 10-15% | 현장 설치, 캘리브레이션 |
| 5. Operation | 가변 | 전시/운영 기간 |
| 6. De-installation | 5% | 철거, 장비 회수 |

각 단계 내에서:
- 마일스톤 배치
- 태스크 나열
- 의존성 표시 (A 완료 후 B 시작)
- 버퍼 시간 배정 (각 단계 사이 1-3일)

### 4. Generate

```
{프로젝트 폴더}/TIMELINE.md    ← 원본
{프로젝트 폴더}/TIMELINE.docx  ← Word
```

### 5. Post-generation

```
제작 일정표가 생성되었습니다:
- {프로젝트 폴더}/TIMELINE.docx (Word)

전체 기간: N일 (YYYY-MM-DD ~ YYYY-MM-DD)
마일스톤: N개
버퍼: N일

다음 단계:
- /estimate 로 이 일정 기반의 견적을 산출할 수 있습니다
- /proposal 로 일정을 포함한 기획서를 만들 수 있습니다
```

## Data Handoff (입출력 규격)

### Input (읽는 문서)
| 문서 | 읽는 필드 | 용도 |
|------|----------|------|
| `PROJECT.md` | 전체 기간, 데드라인 | 일정 프레임 설정 |
| `TECH-SPEC.md` | 장비 목록 | 장비 리드타임, 제작 복잡도 추정 |
| `ESTIMATE.md` | `## 외주비 상세` → 외주 항목 목록 | 외주 리드타임 반영 |

### Output (쓰는 필드) → 다음 스킬이 읽는 구조
| 출력 필드 | 소비 스킬 | 읽는 방식 |
|----------|----------|----------|
| `## Phase` 각 단계 기간 (일수) | `/estimate` | 인건비 = 역할별 일단가 × 기간 |
| `## Phase` + 마일스톤 요약 | `/proposal` | 일정 섹션 |

## Core Principles

1. **버퍼는 필수** — 미디어아트는 예상치 못한 문제가 발생한다. 각 단계 사이 버퍼를 넣는다.
2. **역산 스케줄** — 오픈일에서 역산하여 각 단계의 시작/종료를 배치한다.
3. **의존성 명시** — 무엇이 끝나야 다음이 시작되는지 명확히 표시한다.
4. **한 번에 하나의 질문**
5. **언어를 따른다**
