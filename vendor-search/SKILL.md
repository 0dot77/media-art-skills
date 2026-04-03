---
name: vendor-search
description: "Vendor and outsourcing research tool for media art production. Use when the user wants to find modeling studios, fabrication shops, content creators, or installation companies with pricing research. Triggers on /vendor-search or requests for 외주 업체, 업체 조사, 견적 비교, vendor search."
---

# /vendor-search — 외주 업체/견적 조사

> **Role**: Production Manager
> **Trigger**: User invokes `/vendor-search` or asks about 외주 업체, 업체 조사, 견적 비교
> **Output**: `{프로젝트 폴더}/VENDOR-SEARCH.md` (+ `.docx`)

## Purpose

미디어아트 프로젝트의 외주 항목에 대해 업체를 조사하고 견적 기준을 정리한다. 3D 모델링, 제작/가공, 콘텐츠 제작, 설치 등 카테고리별로 업체 정보와 시장 가격대를 파악한다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요?"

### 1. Context Check

`{프로젝트 폴더}`에서 확인:
- `PROJECT.md` — 프로젝트 규모, 위치
- `ESTIMATE.md` — 외주 항목 목록
- `TECH-SPEC.md` — 외주 산출물 스펙

`references/vendor-categories.md`를 참조한다.

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 외주 분야**
> "어떤 분야의 외주 업체를 찾나요?"

`references/vendor-categories.md`의 분류를 참조:
- 3D 모델링 / CG
- 제작/가공 (Fabrication)
- 하드웨어/전자
- 콘텐츠 제작
- 설치
- 렌탈
- 기타

**Q2 — 산출물 상세**
> "외주로 만들어야 하는 것의 구체적 내용을 알려주세요."

예: "높이 3m 금속 구조물", "10분짜리 4K 모션그래픽", "캐릭터 3D 모델링 5종"

**Q3 — 조건**
> "예산, 일정, 위치 등 조건이 있나요?"

- 예산 범위
- 납기
- 지역 (수도권 / 전국 / 해외 가능)
- 기타 (포트폴리오 필수, NDA 가능 등)

### 3. Research

웹 검색을 통해 업체를 조사한다:

**조사 항목:**
- 업체명, 웹사이트
- 주요 작업 분야
- 포트폴리오 (유사 작업 경험)
- 시장 가격대 (견적 기준)
- 연락처
- 비고 (강점, 약점, 후기 등)

**시장 가격대 기준** (`references/vendor-categories.md` 참조):
- 견적 기준 항목 (디테일 수준, 크기, 복잡도 등)
- 일반적인 가격 범위

### 4. Generate

```
{프로젝트 폴더}/VENDOR-SEARCH.md    ← 원본
{프로젝트 폴더}/VENDOR-SEARCH.docx  ← Word
```

업체별 비교표:
```markdown
## 3D 모델링 업체 비교

| | 업체 A | 업체 B | 업체 C |
|---|---|---|---|
| 웹사이트 | url | url | url |
| 주요 분야 | 캐릭터 | 건축 | 제품 |
| 유사 경험 | ✅ | ✅ | ❌ |
| 가격대 | ₩3-5M | ₩2-4M | ₩4-6M |
| 납기 | 3주 | 4주 | 2주 |
| 비고 | 포트폴리오 우수 | 가성비 좋음 | 빠른 납기 |
```

### 5. Post-generation

```
외주 업체 조사가 완료되었습니다:
- {분야}: N개 업체, 가격대 ₩X-Y

다음 단계:
- /estimate 로 외주비를 포함한 전체 견적을 산출할 수 있습니다
```

## Core Principles

1. **비교 가능하게** — 동일 기준으로 비교할 수 있도록 정리한다.
2. **가격 근거** — 시장 가격대의 근거를 제시한다.
3. **포트폴리오 중요** — 미디어아트 외주는 경험이 중요. 유사 작업 경험 여부를 확인.
4. **한 번에 하나의 질문**
5. **언어를 따른다**
