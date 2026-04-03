---
name: display-calc
description: "Display installation calculator for media art. Use when the user needs to calculate projection throw, LED resolution, blending zones, viewing distance, brightness, or content output resolution. Triggers on /display-calc or requests for 디스플레이 계산, 프로젝터 계산, LED 해상도, 블렌딩."
---

# /display-calc — 디스플레이 설치 계산

> **Role**: Technical Director
> **Trigger**: User invokes `/display-calc` or asks about 디스플레이 계산, throw ratio, LED 해상도, 블렌딩, 시청 거리
> **Output**: `{프로젝트 폴더}/DISPLAY-CALC.docx` + `DISPLAY-CALC.md`

## Purpose

미디어아트 설치의 디스플레이 시스템을 수치적으로 계산한다. 프로젝터 throw, LED 해상도, 멀티 디스플레이 블렌딩, 시청 거리, 휘도, 콘텐츠 출력 해상도를 산출하여 제작팀과 콘텐츠 크리에이터에게 명확한 스펙을 제공한다.

## Behavior

### 1. Context Check

### 0. Save Location

> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

사용자가 지정한 폴더를 `{프로젝트 폴더}`로 사용한다. 폴더가 없으면 자동 생성. 이후 같은 세션에서는 동일 경로를 재사용한다.

### 1. Context Check (계속)

`{프로젝트 폴더}`에서 다음 파일을 확인한다:
- `PROJECT.md` — 프로젝트 유형 파악
- `TECH-SPEC.md` — 이미 정의된 장비 스펙
- `SITE-SURVEY.md` — 공간 치수, 설치 조건

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 디스플레이 유형**
> "어떤 디스플레이를 사용하나요?"

| 유형 | 이후 필요한 정보 |
|------|----------------|
| 프로젝터 (단일) | 모델/throw ratio, 렌즈 시프트, 스크린 크기 |
| 프로젝터 (멀티/블렌딩) | 대수, 오버랩 비율, 소프트엣지, 곡면 여부 |
| LED (직시형) | 피셀 피치, 캐비닛 크기, 전체 벽 크기 |
| LED (바닥/천장/곡면) | 반경, 시야각, 구조 하중 |
| 모니터 어레이 | 모델, 베젤 보정, 배열 패턴 |
| 혼합 (프로젝터+LED 등) | 각각의 정보를 순차 수집 |

**Q2 — 설치 공간 치수**
> "설치 공간의 치수를 알려주세요."

SITE-SURVEY.md가 있으면 자동으로 읽고 확인만 받는다. 없으면:
- 벽면 크기 (가로 x 세로)
- 투사 거리 (프로젝터의 경우)
- 천장 높이
- 관객 위치/동선

**Q3 — 콘텐츠 해상도 요구사항**
> "콘텐츠 해상도 요구사항이 있나요?"

- 목표 해상도 (있으면)
- 프레임 레이트
- HDR 필요 여부
- 없으면 최적 해상도를 계산하여 제안

### 3. Calculations (프로젝트 타입별 분기)

디스플레이 유형에 따라 해당하는 계산을 수행한다. `references/hardware-db.md`를 참조한다.

**프로젝트 타입별 중점 계산:**

| 프로젝트 타입 | 중점 계산 | 추가 고려사항 |
|-------------|----------|-------------|
| 프로젝션 매핑 | throw ratio, 블렌딩 오버랩, 소프트엣지, 곡면 워핑 보정 | 벽면 재질 반사율(gain), 주변광 차단 여부 |
| LED 디스플레이 | 피셀 피치 선정, 캐비닛 수량, 시청 거리, 밝기(nits) | 실내/실외 구분, 구조물 하중, 전력 소비 |
| 인터랙티브 | 디스플레이 해상도 + 센서-디스플레이 좌표 매핑 | 센서 FOV와 디스플레이 영역 정합, 레이턴시 |
| 키네틱 | 디스플레이 크기/무게 제한 (움직이는 구조물) | 진동 영향, 케이블 가동 반경 |
| 이머시브 | 다면 프로젝션 (바닥/벽/천장), 시야각 360° 분석 | 이음새 최소화, 관객 위치별 왜곡 보정 |

#### 3A. Projector Calculations

**이미지 크기 계산**
```
Image Width = Throw Distance / Throw Ratio
Image Height = Image Width / Aspect Ratio
```

**필요 밝기 계산**
```
필요 루멘 = Screen Area(m²) × 필요 Lux × (1/Screen Gain)
- 암실: 100-200 lux
- 저조도: 300-500 lux
- 일반 조명: 800-1500 lux
```

**멀티 프로젝터 블렌딩** (해당 시)
```
오버랩 영역 = 개별 이미지 폭 × 오버랩 비율
전체 캔버스 폭 = (프로젝터 수 × 개별 이미지 폭) - ((프로젝터 수 - 1) × 오버랩 영역)
출력 해상도 폭 = (프로젝터 수 × 네이티브 해상도 폭) - ((프로젝터 수 - 1) × 오버랩 픽셀)
```

**소프트엣지 블렌딩 영역 (픽셀)**
```
오버랩 픽셀 = 네이티브 해상도 폭 × 오버랩 비율
일반적 오버랩: 10-20% (권장 15%)
```

#### 3B. LED Calculations

**총 해상도 계산**
```
가로 픽셀 = 가로 크기(mm) / Pixel Pitch(mm)
세로 픽셀 = 세로 크기(mm) / Pixel Pitch(mm)
총 픽셀 수 = 가로 픽셀 × 세로 픽셀
```

**캐비닛 수량**
```
가로 캐비닛 수 = ceil(가로 크기 / 캐비닛 가로)
세로 캐비닛 수 = ceil(세로 크기 / 캐비닛 세로)
총 캐비닛 수 = 가로 × 세로
```

**최적 시청 거리**
```
최소 시청 거리(m) = Pixel Pitch(mm) × 1.5 ~ 2.0
최적 시청 거리(m) = Pixel Pitch(mm) × 3.0
```

**필요 밝기**
```
실내: 800-1,500 nits
반옥외 (창가 등): 2,000-3,000 nits
옥외: 5,000+ nits
```

#### 3C. Monitor Array Calculations

**유효 해상도 (베젤 보정 포함)**
```
가로 유효 = (모니터 수(가로) × 네이티브 가로) - 베젤 보정 없음 (논리적 해상도)
물리적 간격 = 모니터 수(가로) × 베젤 두께 × 2
```

#### 3D. Common Calculations

**콘텐츠 출력 해상도**
- 미디어서버가 출력해야 하는 실제 해상도
- 블렌딩 오버랩 영역 포함
- 워핑/마스킹 영역 포함 시 추가 마진

**시청각/관람 분석**
```
시야각(°) = 2 × arctan(이미지 폭 / (2 × 관람 거리))
권장: 40-60° (몰입형), 20-40° (일반), 60°+ (이머시브)
```

### 4. Validation (검증)

계산 결과를 `references/hardware-db.md`의 실제 장비 스펙과 **자동 대조**한다:

**프로젝터 검증:**
- 계산된 throw ratio가 선택 모델의 실제 throw ratio 범위 내인지
  - 범위 밖이면: ❌ + 해당 throw ratio를 지원하는 대안 모델 제시
- 필요 밝기가 모델의 공칭 밝기 이하인지
  - 초과 시: ⚠️ + 더 밝은 모델 제시 또는 프로젝터 추가 제안
- 멀티 블렌딩 시 오버랩 비율이 10-20% 범위 내인지

**LED 검증:**
- 계산된 시청 거리가 피셀 피치 기반 최소 시청 거리(hardware-db.md) 이상인지
  - 미달 시: ⚠️ + 더 작은 피셀 피치 제안
- 필요 밝기(nits)가 실내/실외 기준(hardware-db.md) 이내인지
- 계산된 해상도가 미디어서버 최대 출력 이내인지 (미디어서버 테이블 참조)

**공통 검증:**
- 콘텐츠 출력 해상도가 미디어서버 최대 출력 이내인지
- SITE-SURVEY.md가 있으면: 설치 공간 치수가 계산에 사용된 값과 일치하는지

**검증 출력 예시:**
```
## Validation Report

| 항목 | 기준값 (hardware-db.md) | 계산값 | 결과 |
|------|----------------------|--------|------|
| Throw Ratio (Panasonic PT-RQ25K) | 1.2–2.0:1 | 1.7:1 | ✅ 범위 내 |
| 필요 밝기 | 8,000 lm | 25,000 lm (모델) | ✅ 충분 |
| 블렌딩 오버랩 | 10-20% 권장 | 15% | ✅ 적정 |
| 미디어서버 출력 (Resolume) | 최대 8x 4K | 2x 4K | ✅ 가능 |
| 시청 거리 vs 공간 | 2.0m 최소 | 3.0m | ✅ 적정 |
```

### 5. Output

**`TECH-SPEC.md`가 이미 존재하면**: 디스플레이 섹션을 업데이트 제안
**없으면**: 마크다운을 생성한 뒤 `scripts/md-to-docx.py`로 `.docx`로 변환한다.

```
{프로젝트 폴더}/DISPLAY-CALC.md    ← 원본
{프로젝트 폴더}/DISPLAY-CALC.docx  ← Word
```

출력 내용:
- 계산 요약 (한눈에 보는 핵심 수치)
- 상세 계산 과정 (공식 포함)
- ASCII 레이아웃 다이어그램
- 콘텐츠 크리에이터를 위한 스펙 요약 (해상도, 비율, FPS)
- 장비 추천 (references/hardware-db.md 기반)

**계산 요약 예시:**
```
── Display Summary ──────────────────
Setup: Dual 4K Projector, Edge Blending
Total Canvas: 7680 × 2160 px
Blending Overlap: 320 px (15%)
Per-projector Output: 4000 × 2160 px
Throw Distance: 5.2m
Image Size: 6.0m × 1.7m (21:9)
Brightness Required: 8,000 lm/ea
Min Viewing Distance: 2.0m
Viewing Angle: 56° @ 3m

Content Spec for Creators:
  Resolution: 7680 × 2160 px
  Frame Rate: 60 fps
  Format: HAP Q (.mov)
─────────────────────────────────────
```

### 5. Post-generation

```
디스플레이 계산이 완료되었습니다:
- {프로젝트 폴더}/DISPLAY-CALC.docx (Word)
- {프로젝트 폴더}/DISPLAY-CALC.md (원본)

다음 단계:
- /content-pipeline 으로 이 스펙에 맞는 콘텐츠 파이프라인을 설정할 수 있습니다
- /install-guide 로 이 구성의 설치 매뉴얼을 생성할 수 있습니다
- /estimate 로 이 장비 구성의 견적을 산출할 수 있습니다
```

## Data Handoff (입출력 규격)

### Input (읽는 문서)
| 문서 | 읽는 필드 | 용도 |
|------|----------|------|
| `PROJECT.md` | 프로젝트 유형 | 타입별 계산 분기 |
| `TECH-SPEC.md` | 장비 모델/스펙 | 이미 정의된 장비가 있으면 해당 스펙 기반 계산 |
| `SITE-SURVEY.md` | 벽면 크기, 투사 거리, 천장 높이 | 공간 치수 자동 반영 |
| `references/hardware-db.md` | 프로젝터/LED/미디어서버 스펙 | 검증 기준 데이터 |

### Output (쓰는 필드) → 다음 스킬이 읽는 구조
| 출력 필드 | 소비 스킬 | 읽는 방식 |
|----------|----------|----------|
| `## Display Summary` → 출력 해상도 | `/content-pipeline` | 콘텐츠 해상도 기준으로 사용 |
| `## Display Summary` → 장비 모델, 수량 | `/estimate` | 장비비 산출 |
| `## Display Summary` → 디스플레이 구성 전체 | `/tech-spec` | 디스플레이 서브시스템으로 반영 |
| `## Content Spec for Creators` → 해상도, FPS, 포맷 | `/content-pipeline` | 스펙 가이드 기준 |

## Core Principles

1. **수치 기반** — 모든 결과를 구체적 숫자로 제시한다. 공식을 함께 보여준다.
2. **콘텐츠 크리에이터 관점** — 계산 결과를 "콘텐츠를 만들 때 어떤 해상도/비율로 작업해야 하는가"로 번역한다.
3. **보수적 산정** — 밝기, 해상도는 넉넉하게, 시청 거리는 보수적으로 잡는다.
4. **검증 필수** — 계산 결과를 `references/hardware-db.md` 실제 스펙과 대조한다. Validation Report를 반드시 포함한다.
5. **한 번에 하나의 질문** — 기술적으로 복잡한 주제일수록 질문을 쪼갠다.
6. **레퍼런스 활용** — `references/hardware-db.md`의 실제 장비 스펙을 근거로 제시한다.
7. **언어를 따른다** — 사용자의 언어로 대화하고 문서를 생성한다.
