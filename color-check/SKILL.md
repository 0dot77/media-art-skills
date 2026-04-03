---
name: color-check
description: "Color space and calibration checker for media art installations. Use when the user wants to verify color settings across the content pipeline or troubleshoot color inconsistencies between displays. Triggers on /color-check or requests for 색공간, 컬러 체크, 캘리브레이션, color calibration."
---

# /color-check — 색공간/캘리브레이션 검증

> **Role**: Technical Director
> **Trigger**: User invokes `/color-check` or asks about 색공간, 컬러 체크, 캘리브레이션
> **Output**: `{프로젝트 폴더}/COLOR-CHECK.docx` + `COLOR-CHECK.md`

## Purpose

미디어아트 설치의 색공간 설정을 파이프라인 전체에 걸쳐 검증한다. 제작(모니터) → 출력(미디어서버) → 표시(프로젝터/LED) 각 단계의 색공간/감마/색온도 일관성을 확인하고 불일치를 찾아낸다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

사용자가 지정한 폴더를 `{프로젝트 폴더}`로 사용한다. 이후 같은 세션에서는 동일 경로를 재사용한다.

### 1. Context Check

`{프로젝트 폴더}`에서 확인:
- `TECH-SPEC.md` — 디스플레이 스펙, 색공간 설정
- `CONTENT-SPEC.md` — 콘텐츠 색공간

`references/color-spaces.md`를 참조한다.

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 파이프라인 단계**
> "어떤 단계의 색공간을 확인하나요?"

- 전체 파이프라인 (제작→출력→표시)
- 제작 단계 (소프트웨어 설정)
- 출력 단계 (미디어서버 설정)
- 표시 단계 (프로젝터/LED 설정)
- 멀티 디스플레이 매칭

**Q2 — 현재 설정**
> "현재 사용 중인 색공간 설정을 알려주세요."

단계별로 확인:
- 제작 소프트웨어 (After Effects, Premiere, DaVinci 등)의 프로젝트 색공간
- 미디어서버의 출력 색공간
- 디스플레이의 입력 색공간/색온도/감마

**Q3 — 증상 (있으면)**
> "색상 관련 문제가 있나요?"

- 모니터와 프로젝터 색이 다름
- 멀티 프로젝터 간 색 불일치
- 어두운 부분이 뭉개짐 (감마 문제)
- 채도가 과하거나 빠짐

### 3. Analysis

**파이프라인 일관성 체크:**
```
── Color Pipeline Check ─────────────────
제작: After Effects → sRGB / gamma 2.2 / D65
  ↓
출력: Resolume Arena → sRGB / gamma 2.2
  ↓
표시: Projector A → sRGB / gamma 2.2 / 6500K
      Projector B → sRGB / gamma 2.2 / 6500K

✅ 전체 파이프라인 일관성: OK

⚠️ 주의:
- 프로젝터 주변광 보정: 현장에서 밝기/대비 재조정 필요
──────────────────────────────────────────
```

**불일치 감지:**
```
❌ 불일치 감지:
- 제작: sRGB (gamma 2.2)
- 표시: Rec.709 (gamma 2.4)
→ 어두운 부분이 더 어둡게 보임. 감마 차이 0.2.

권장 조치:
1. 프로젝터 감마를 2.2로 변경 (메뉴 → Image → Gamma)
2. 또는 콘텐츠를 Rec.709 감마로 재렌더링
```

**멀티 디스플레이 매칭:**
```
Projector A: 6500K / sRGB / 2.2 / 3200 lm
Projector B: 7200K / sRGB / 2.2 / 3100 lm
                ^^^^                ^^^^
❌ 색온도 불일치: 700K 차이
⚠️ 밝기 불일치: 100 lm 차이

권장 조치:
1. 두 프로젝터 모두 6500K로 통일
2. 밝은 쪽을 낮춰서 밝기 매칭
3. 최종적으로 현장 캘리브레이션
```

### 4. Output

검증 결과를 자동으로 `.docx`와 `.md`로 저장한다:

```
{프로젝트 폴더}/COLOR-CHECK.md    ← 원본
{프로젝트 폴더}/COLOR-CHECK.docx  ← Word
```

### 5. Post-generation

```
다음 단계:
- /content-pipeline 으로 콘텐츠 스펙을 통일할 수 있습니다
- /render-check 로 렌더링 결과를 검증할 수 있습니다
```

### 3.5. Validation (검증)

분석 결과를 `references/color-spaces.md`와 자동 대조한다:

**색공간 지원 검증:**
- 제작 소프트웨어의 색공간이 `references/color-spaces.md`의 표준 색공간 목록에 있는지
- 디스플레이 기기가 해당 색공간을 지원하는지 (gamut 범위 내인지)
  - DCI-P3 콘텐츠를 sRGB 프로젝터에 출력하려는 경우: ⚠️ + gamut 클리핑 경고

**HDR 호환성 검증:**
- HDR 콘텐츠인 경우 디스플레이의 HDR 표준 지원 여부 (references/color-spaces.md HDR 테이블)
- peak brightness가 HDR 표준 요구 사양 이상인지

**멀티 디스플레이 편차 검증:**
- 같은 모델이라도 개체별 색온도/밝기 편차가 허용 범위(±200K, ±5%) 내인지
  - 초과 시: ⚠️ + 현장 캘리브레이션 필수 명시

## Data Handoff (입출력 규격)

### Input (읽는 문서)
| 문서 | 읽는 필드 | 용도 |
|------|----------|------|
| `TECH-SPEC.md` | 디스플레이 스펙 → 색공간 설정 | 표시 단계 색공간 |
| `CONTENT-SPEC.md` | 색공간, 감마 | 제작 단계 색공간 |
| `references/color-spaces.md` | 색공간 테이블, HDR 표준 | 검증 기준 |

## Core Principles

1. **파이프라인 전체를 본다** — 한 단계만 맞아도 앞뒤가 안 맞으면 의미 없다.
2. **구체적 조치** — 문제를 지적하고 끝나지 않고, 어떤 메뉴에서 무엇을 바꿔야 하는지까지.
3. **검증 필수** — `references/color-spaces.md`로 색공간 호환성을 대조한다.
4. **레퍼런스 기반** — `references/color-spaces.md` 데이터를 근거로 판단.
5. **한 번에 하나의 질문**
6. **언어를 따른다**
