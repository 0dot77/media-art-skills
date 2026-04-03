---
name: estimate
description: "Project cost estimator for media art production. Use when the user wants to calculate project costs including equipment, labor, outsourcing, and site expenses. Triggers on /estimate or requests for 견적, 비용 산출, cost estimation."
---

# /estimate — 견적서 생성

> **Role**: Production Manager
> **Trigger**: User invokes `/estimate` or asks about 견적, 비용 산출, cost estimation
> **Output**: `{프로젝트 폴더}/ESTIMATE.docx` + `ESTIMATE.md`

## Purpose

미디어아트 프로젝트의 견적서를 생성한다. 장비비, 인건비, 외주비, 현장비를 카테고리별로 산출하고 지급 스케줄까지 포함한 실무 견적 문서를 만든다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

사용자가 지정한 폴더를 `{프로젝트 폴더}`로 사용한다. 폴더가 없으면 자동 생성. 이후 같은 세션에서는 동일 경로를 재사용한다.

### 1. Context Check

`{프로젝트 폴더}`에서 다음 파일을 확인한다:
- `PROJECT.md` — 프로젝트 유형, 클라이언트, 범위 파악
- `TECH-SPEC.md` — 장비 요구사항 파악
- `TIMELINE.md` — 기간 기반 인건비 산출
- `SITE-SURVEY.md` — 현장 관련 비용 항목
- `ESTIMATE.md` / `ESTIMATE.docx` — 이미 있으면 업데이트할지 새로 만들지 질문

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 프로젝트 유형**
> "어떤 유형의 프로젝트인가요?"

PROJECT.md가 없을 때만 질문한다. 유형에 따라 견적 항목이 달라진다:
- Projection Mapping
- LED Display
- Interactive Installation
- Kinetic Art
- Immersive Room
- 기타 (직접 설명)

**Q2 — 프로젝트 규모**
> "프로젝트 규모를 알려주세요."

디스플레이 수량/크기, 공간 면적, 예상 관객 규모 등. TECH-SPEC.md가 있으면 여기서 읽은 정보를 보여주고 확인만 받는다.

**Q3 — 장비 조달 방식**
> "장비를 구매하나요, 렌탈하나요?"

- 구매 (Purchase)
- 렌탈 (Rental)
- 혼합 (Mix) — 이 경우 항목별로 구분

이 선택은 비용 구조에 큰 영향을 미친다.

**Q4 — 외주 항목**
> "외주로 진행하는 항목이 있나요?"

`references/vendor-categories.md`의 분류를 참고하여 해당 항목을 체크:
- 3D 모델링 / CG
- 제작/가공 (Fabrication)
- 하드웨어/전자
- 콘텐츠 제작
- 설치
- 기타

**Q5 — 설치 현장 정보**
> "설치 현장 정보를 알려주세요."

- 위치 (국내/해외 — 출장비에 영향)
- 설치 기간 (일)
- 필요 인력 수

SITE-SURVEY.md가 있으면 자동으로 읽고 확인만 받는다.

### 3. Cost Calculation (프로젝트 타입별 분기)

프로젝트 타입에 따라 **비중이 달라지는 비용 구조**를 적용한다:

| 프로젝트 타입 | 장비비 비중 | 외주비 비중 | 인건비 비중 | 주요 항목 |
|-------------|-----------|-----------|-----------|----------|
| 프로젝션 매핑 | 높음 | 중간 | 중간 | 프로젝터, 렌즈, 마운트, 미디어서버 |
| LED 디스플레이 | 매우 높음 | 중간 | 낮음 | LED 패널, 컨트롤러, 구조물, 전력 |
| 인터랙티브 | 중간 | 높음 | 높음 | 센서, 개발비(소프트웨어), 테스트 비용 |
| 키네틱 | 중간 | 매우 높음 | 중간 | 모터/액추에이터, 금속 가공, 안전 장치 |
| 이머시브 | 높음 | 높음 | 높음 | 대면적 장비, 음향, 공간 시공 |

각 카테고리별로 구체적 항목을 산출한다:

**장비비 (Equipment)**
- TECH-SPEC.md의 장비 목록 기반
- `references/hardware-db.md` 참조하여 시장 가격대 제시
- 구매/렌탈에 따라 단가 구분
- 케이블, 커넥터, 소모품 별도 계상

**인건비 (Labor)**
- 역할별 일단가 기준:
  - PM: 400,000-600,000원/일
  - TD (Technical Director): 500,000-800,000원/일
  - Content Creator: 400,000-600,000원/일
  - Programmer: 400,000-700,000원/일
  - Installer: 250,000-400,000원/일
- TIMELINE.md가 있으면 기간 기반 자동 산출

**외주비 (Outsourcing)**
- Q4에서 선택한 항목별로 시장 가격대 제시
- `references/vendor-categories.md` 참조

**현장비 (Site)**
- 교통비 (국내/해외)
- 숙박비 (인원 x 일수)
- 식비
- 물류비 (장비 운송)

**예비비 (Contingency)**
- 기본 10%, 복잡한 프로젝트 15%
- 해외 프로젝트 15-20%

### 4. Validation (검증)

산출된 견적을 `references/` 데이터 및 입력 문서와 자동 대조한다:

**장비비 검증:**
- TECH-SPEC.md의 장비 목록과 견적의 장비 목록이 1:1 대응하는지
  - 누락 장비: ❌ + 해당 장비 추가 제안
  - 잉여 장비: ⚠️ + TECH-SPEC에 없는 장비 표시
- 단가가 `references/hardware-db.md` 기반 시장 가격 범위 내인지
  - 범위 밖이면: ⚠️ + 근거 확인 요청

**인건비 검증:**
- TIMELINE.md의 기간과 인건비 산출 기간이 일치하는지
  - 불일치 시: ⚠️ + 어느 기간 기준인지 명시

**전력 비용 검증:**
- TECH-SPEC.md의 총 소비전력과 SITE-SURVEY.md의 가용 전력 비교
  - 추가 전력 공사가 필요하면 현장비에 반영되었는지

**검증 출력:**
```
## Validation Report

| 항목 | 기준 | 견적 | 결과 |
|------|------|------|------|
| 장비 목록 일치 (TECH-SPEC) | 12개 항목 | 12개 항목 | ✅ 일치 |
| 프로젝터 단가 | 시장가 ₩40-50M | ₩45M | ✅ 범위 내 |
| 인건비 기간 (TIMELINE) | 45일 | 45일 | ✅ 일치 |
| 전력 비용 반영 | 추가 회로 필요 | 현장비에 포함 | ✅ 반영됨 |
```

### 5. Generate ESTIMATE.md

`templates/ESTIMATE.md.tmpl`을 기반으로 마크다운을 생성한 뒤, `scripts/md-to-docx.py`로 `.docx`로 변환한다.

```
{프로젝트 폴더}/ESTIMATE.md    ← 원본 (버전 관리용)
{프로젝트 폴더}/ESTIMATE.docx  ← 클라이언트 전달용
```

포함 사항:
- 카테고리별 소계가 있는 요약 테이블
- 각 카테고리의 상세 내역 (항목, 단위, 수량, 단가, 소계, 비고)
- 지급 스케줄 제안 (계약금 30% / 중도금 40% / 잔금 30% 기본)
- 전제 조건 (Assumptions) 섹션
- 제외 항목 (Exclusions) 섹션
- 견적 유효기간 (기본 30일)

### 5. Post-generation

```
견적서가 생성되었습니다:
- {프로젝트 폴더}/ESTIMATE.docx (Word)
- {프로젝트 폴더}/ESTIMATE.md (원본)

총 견적: ₩XX,XXX,XXX (VAT 별도)

다음 단계:
- /proposal 로 이 견적을 포함한 기획서를 만들 수 있습니다
- /timeline 으로 이 견적 기반의 제작 일정을 생성할 수 있습니다
- /tech-spec 이 아직 없다면, 먼저 기술 사양을 정리하는 것을 권합니다
```

## Data Handoff (입출력 규격)

### Input (읽는 문서)
| 문서 | 읽는 필드 | 용도 |
|------|----------|------|
| `PROJECT.md` | 프로젝트 유형, 범위 | 타입별 비용 구조 분기 |
| `TECH-SPEC.md` | `## 장비 목록` 테이블 (모델, 수량, 소비전력) | 장비비 산출 |
| `TIMELINE.md` | `## Phase` 각 단계 기간 (일수) | 인건비 = 역할별 일단가 × 기간 |
| `SITE-SURVEY.md` | 위치, 접근 조건 | 현장비 (교통/숙박/물류) 산출 |
| `DISPLAY-CALC.md` | `## Display Summary` → 장비 모델, 수량 | 장비비에 반영 |
| `references/vendor-categories.md` | 외주 카테고리별 견적 기준 | 외주비 산출 근거 |
| `references/hardware-db.md` | 장비 시장 가격 참조 | 단가 검증 |

### Output (쓰는 필드) → 다음 스킬이 읽는 구조
| 출력 필드 | 소비 스킬 | 읽는 방식 |
|----------|----------|----------|
| `## 비용 요약` → 카테고리별 소계, 총액 | `/proposal` | 예산 섹션에 요약 포함 |
| `## 지급 스케줄` → 계약금/중도금/잔금 | `/proposal` | 지급 조건 섹션 |
| `## 외주비 상세` → 외주 항목 목록 | `/timeline` | 외주 리드타임 반영 |

## Core Principles

1. **수치 기반** — 모든 항목에 구체적 금액을 제시한다. "상황에 따라 다름"을 최소화한다.
2. **보수적 산정** — 애매할 때는 높은 쪽으로 잡는다. 견적 부족은 프로젝트 리스크다.
3. **투명한 근거** — 각 단가의 근거를 비고란에 표시한다 (시장가, 렌탈가, 일단가 등).
4. **검증 필수** — 장비 목록과 TECH-SPEC 일치 여부, 단가의 시장 범위 내 여부를 대조한다.
5. **한 번에 하나의 질문** — 사용자를 압도하지 않는다.
6. **언어를 따른다** — 사용자의 언어로 대화하고 문서를 생성한다.
