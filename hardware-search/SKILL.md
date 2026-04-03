---
name: hardware-search
description: "Hardware specification search and comparison for media art installations. Use when the user wants to compare projectors, LED displays, media servers, sensors, or other installation equipment. Triggers on /hardware-search or requests for 장비 검색, 장비 비교, 프로젝터 추천, LED 추천."
---

# /hardware-search — 장비 스펙 비교 검색

> **Role**: Technical Director
> **Trigger**: User invokes `/hardware-search` or asks about 장비 검색, 장비 비교, 프로젝터 추천
> **Output**: `{프로젝트 폴더}/HARDWARE-COMPARISON.docx` + `HARDWARE-COMPARISON.md`

## Purpose

미디어아트 설치에 사용할 장비(프로젝터, LED, 미디어서버, 센서 등)의 스펙을 비교하고 프로젝트 요구사항에 맞는 장비를 추천한다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

사용자가 지정한 폴더를 `{프로젝트 폴더}`로 사용한다. 폴더가 없으면 자동 생성. 이후 같은 세션에서는 동일 경로를 재사용한다.

### 1. Context Check

`{프로젝트 폴더}`에서 확인 (있으면):
- `TECH-SPEC.md` — 이미 정의된 요구사항
- `DISPLAY-CALC.md` — 필요 밝기, 해상도, throw ratio
- `SITE-SURVEY.md` — 현장 조건

`references/hardware-db.md`를 기본 데이터로 참조한다.

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 장비 카테고리**
> "어떤 장비를 찾고 있나요?"

- 프로젝터
- LED 디스플레이
- 미디어서버
- 센서 (뎁스 카메라, LiDAR, 터치 등)
- 기타

**Q2 — 핵심 요구사항**
> "가장 중요한 요구사항은 무엇인가요?"

카테고리별로 다른 질문:
- 프로젝터: 밝기, 해상도, throw ratio, 렌즈 교환, 예산
- LED: 피셀 피치, 실내/실외, 크기, 밝기
- 미디어서버: 출력 수, 해상도, 실시간 여부
- 센서: 감지 범위, 정밀도, 인터페이스

**Q3 — 조건/제약**
> "추가 조건이 있나요?"

예산 범위, 구매/렌탈, 납기, 브랜드 선호 등.

### 3. Search & Compare

`references/hardware-db.md`를 기반으로 조건에 맞는 장비를 필터링한다.

웹 검색이 가능한 경우 최신 제품 정보를 추가로 조사한다.

**비교표 생성 예시:**
```
── Projector Comparison ─────────────────────────
요구사항: 10,000+ lm, 4K, throw ratio 1.0-2.0

| | Panasonic PT-RQ25K | Barco UDX-4K22 | Christie 4K25-RGB |
|---|---|---|---|
| 해상도 | 3840×2400 | 3840×2400 | 3840×2160 |
| 밝기 | 25,000 lm | 22,500 lm | 25,000 lm |
| Throw Ratio | 렌즈별 | 렌즈별 | 렌즈별 |
| 무게 | 43 kg | 38 kg | 45 kg |
| 광원 | 레이저 | 레이저 | RGB 레이저 |
| 수명 | 20,000h | 30,000h | 30,000h |
| 가격대 | ₩40-50M | ₩35-45M | ₩45-55M |

✅ 추천: Panasonic PT-RQ25K
  - 밝기와 해상도 모두 충족
  - 다양한 렌즈 옵션으로 현장 유연성 높음
──────────────────────────────────────────────────
```

### 4. Output

비교 결과를 자동으로 `.docx`와 `.md`로 저장한다:

```
{프로젝트 폴더}/HARDWARE-COMPARISON.md    ← 원본
{프로젝트 폴더}/HARDWARE-COMPARISON.docx  ← Word
```

### 5. Post-generation

```
다음 단계:
- /display-calc 로 선택한 장비로 설치 계산을 할 수 있습니다
- /estimate 로 이 장비를 포함한 견적을 산출할 수 있습니다
- /tech-spec 으로 기술 사양서에 반영할 수 있습니다
```

## Core Principles

1. **실무 관점** — 스펙 나열이 아닌, 프로젝트 맥락에서의 추천.
2. **가격 투명성** — 시장 가격대를 제시한다 (정확한 견적은 업체 문의 필요 명시).
3. **레퍼런스 기반** — `references/hardware-db.md`를 1차 소스로 활용.
4. **한 번에 하나의 질문**
5. **언어를 따른다**
