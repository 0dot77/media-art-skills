---
name: proposal
description: "Project proposal generator for media art production. Use when the user wants to create a client proposal, 기획서, or project pitch document combining concept, tech spec, timeline, and budget. Triggers on /proposal or requests for 기획서, 제안서, proposal."
---

# /proposal — 기획서/제안서

> **Role**: Production Manager + Creative Director
> **Trigger**: User invokes `/proposal` or asks about 기획서, 제안서, proposal
> **Output**: `{프로젝트 폴더}/PROPOSAL.docx` + `PROPOSAL.md`

## Purpose

미디어아트 프로젝트의 기획서(제안서)를 생성한다. 컨셉, 기술 구성, 일정, 예산을 하나의 클라이언트 전달용 문서로 통합한다. 기존에 생성된 TECH-SPEC, ESTIMATE, TIMELINE 등을 자동으로 조합한다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

사용자가 지정한 폴더를 `{프로젝트 폴더}`로 사용한다. 폴더가 없으면 자동 생성. 이후 같은 세션에서는 동일 경로를 재사용한다.

### 1. Context Check

`{프로젝트 폴더}`에서 다음 파일을 확인한다:
- `PROJECT.md` — 프로젝트 개요
- `TECH-SPEC.md` — 기술 사양 (있으면 "기술 구성" 섹션 자동 생성)
- `ESTIMATE.md` — 견적 (있으면 "예산" 섹션 자동 생성)
- `TIMELINE.md` — 일정 (있으면 "제작 일정" 섹션 자동 생성)
- `PROPOSAL.md` / `PROPOSAL.docx` — 이미 있으면 업데이트할지 질문

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 클라이언트 정보**
> "기획서를 받을 클라이언트(대상)를 알려주세요."

- 회사/기관명
- 담당자 (있으면)
- 기획서의 목적 (수주 입찰 / 자체 기획 / 협업 제안)

**Q2 — 프로젝트 컨셉**
> "프로젝트의 핵심 컨셉을 한두 문장으로 설명해주세요."

PROJECT.md가 있으면 그 내용을 보여주고 보완만 받는다. 없으면 직접 입력.

**Q3 — 레퍼런스**
> "참고할 레퍼런스 작품이나 이미지가 있나요?"

비주얼 레퍼런스, 유사 프로젝트 사례 등. 없으면 건너뛴다.

**Q4 — 기대 효과**
> "이 프로젝트로 기대하는 효과는 무엇인가요?"

브랜드 경험, 관객 유입, 미디어 노출, 공간 활성화 등.

**Q5 — 회사 정보**
> "기획서에 표기할 회사(팀) 정보를 알려주세요."

- 회사명
- 연락처
- 포트폴리오 URL (선택)

### 3. Document Assembly

기존 문서들을 조합하여 기획서를 구성한다:

| 기획서 섹션 | 소스 |
|------------|------|
| 1. 프로젝트 개요 | Q1 + Q2 + PROJECT.md |
| 2. 컨셉 | Q2 상세화 |
| 3. 기술 구성 | TECH-SPEC.md 요약 |
| 4. 제작 일정 | TIMELINE.md 요약 |
| 5. 예산 | ESTIMATE.md 요약 (상세 내역은 별첨) |
| 6. 팀 구성 | PROJECT.md 또는 Q5 |
| 7. 레퍼런스 | Q3 |
| 8. 기대 효과 | Q4 |

소스 문서가 없는 섹션은 사용자 입력으로 채우거나, 해당 스킬을 먼저 실행하도록 안내한다.

### 4. Generate

`templates/PROPOSAL.md.tmpl`을 기반으로 마크다운을 생성한 뒤, `scripts/md-to-docx.py`로 `.docx`로 변환한다.

```
{프로젝트 폴더}/PROPOSAL.md    ← 원본
{프로젝트 폴더}/PROPOSAL.docx  ← 클라이언트 전달용
```

### 5. Post-generation

```
기획서가 생성되었습니다:
- {프로젝트 폴더}/PROPOSAL.docx (Word)
- {프로젝트 폴더}/PROPOSAL.md (원본)

포함된 섹션: 개요, 컨셉, 기술 구성, 일정, 예산, 팀, 레퍼런스, 기대 효과

다음 단계:
- 기술 사양이 아직 없다면 /tech-spec 으로 먼저 정리하세요
- 견적이 아직 없다면 /estimate 로 산출하세요
```

## Data Handoff (입출력 규격)

### Input (읽는 문서)
| 문서 | 읽는 필드 | 기획서 섹션 |
|------|----------|-----------|
| `PROJECT.md` | 프로젝트명, 클라이언트, 팀 구성 | 1. 프로젝트 개요, 6. 팀 구성 |
| `TECH-SPEC.md` | `## 시스템 개요`, `## 장비 목록` 요약 | 3. 기술 구성 (비기술 용어로 번역) |
| `ESTIMATE.md` | `## 비용 요약`, `## 지급 스케줄` | 5. 예산 (상세는 별첨) |
| `TIMELINE.md` | `## Phase` 단계별 기간, 마일스톤 | 4. 제작 일정 |

### 누락 문서 처리
- 소스 문서가 없는 섹션: 사용자에게 직접 입력을 받거나, 해당 스킬(`/tech-spec`, `/estimate`, `/timeline`) 먼저 실행을 안내
- 직접 입력 시에도 핸드오프 구조와 동일한 마크다운 형식으로 PROPOSAL.md에 작성하여 추후 역참조 가능하게 함

## Core Principles

1. **클라이언트 관점** — 기술 용어를 최소화하고, 비전과 가치를 앞세운다.
2. **조합 우선** — 이미 만든 문서를 최대한 활용한다. 중복 입력을 피한다.
3. **깔끔한 포맷** — Word 문서로 바로 전달할 수 있는 수준의 문서 품질.
4. **한 번에 하나의 질문**
5. **언어를 따른다**
