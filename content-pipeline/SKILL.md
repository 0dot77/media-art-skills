---
name: content-pipeline
description: "Content pipeline manager for media art installations. Use when the user wants to verify media asset specifications, check codec compatibility, or set up content delivery workflows. Triggers on /content-pipeline or requests for 콘텐츠 파이프라인, 미디어 에셋, 코덱, 포맷."
---

# /content-pipeline — 미디어 에셋 관리

> **Role**: Technical Director
> **Trigger**: User invokes `/content-pipeline` or asks about 콘텐츠 파이프라인, 미디어 에셋, 코덱, 포맷
> **Output**: `{프로젝트 폴더}/CONTENT-SPEC.docx` + `CONTENT-SPEC.md`

## Purpose

미디어아트 설치 콘텐츠의 해상도, 포맷, 코덱, 프레임레이트를 검증하고 콘텐츠 크리에이터를 위한 스펙 가이드를 생성한다. 미디어서버 호환성을 확인하고 일반적인 실수를 방지한다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요?"

### 1. Context Check

`{프로젝트 폴더}`에서 확인:
- `TECH-SPEC.md` — 미디어서버, 디스플레이 스펙
- `DISPLAY-CALC.md` — 콘텐츠 출력 해상도

`references/codec-reference.md`와 `references/color-spaces.md`를 참조한다.

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 미디어서버/재생 환경**
> "콘텐츠를 어떤 환경에서 재생하나요?"

- Resolume Arena/Avenue
- disguise (d3)
- TouchDesigner
- Watchout
- Brightsign
- Mac/PC 기본 플레이어
- 기타

**Q2 — 콘텐츠 유형**
> "어떤 유형의 콘텐츠인가요?"

- 사전 렌더링 영상 (Pre-rendered video)
- 실시간 생성 (Realtime)
- 이미지 시퀀스
- 혼합

**Q3 — 디스플레이 스펙**
> "출력 해상도와 프레임레이트를 알려주세요."

DISPLAY-CALC.md가 있으면 자동 반영.

### 3. Pipeline Analysis

입력을 기반으로 분석한다:

**코덱 추천**
- 미디어서버별 최적 코덱을 `references/codec-reference.md`에서 매칭
- 예: Resolume → HAP Q, disguise → NotchLC, 범용 → ProRes 422

**스펙 호환성 체크**
- 해상도가 미디어서버 최대 출력 이내인지
- 코덱이 GPU/CPU 디코딩 가능한지
- 비트레이트가 스토리지 I/O 한계 이내인지
- 파일 크기 예상 (시간 × 비트레이트)

**스토리지 계산**
```
파일 크기 = 비트레이트(Mbps) × 재생시간(초) / 8
예: HAP Q, 4K 60fps, 10분 → ~180GB
```

**일반적 실수 경고**
- H.264/H.265를 멀티채널 재생에 사용하려는 경우
- 색공간 불일치 (sRGB 제작 → Rec.709 재생)
- 알파 채널 필요 시 코덱 선택
- 인터레이스/프로그레시브 혼용

### 3.5. Validation (검증)

추천 결과를 `references/` 데이터와 자동 대조한다:

**코덱 호환성 검증 (references/codec-reference.md):**
- 추천 코덱이 선택한 미디어서버의 호환 코덱 목록에 있는지
  - 비호환 시: ❌ + 해당 서버에서 지원하는 대안 코덱 제시
- 코덱의 디코딩 부하(CPU/GPU)가 미디어서버 성능에 적합한지
  - 과부하 예상 시: ⚠️ + 더 가벼운 코덱 제안

**미디어서버 출력 검증 (references/hardware-db.md):**
- 필요 출력 해상도/채널 수가 미디어서버 최대 출력 이내인지
  - 초과 시: ❌ + 서버 업그레이드 또는 분산 구성 제안

**색공간 검증 (references/color-spaces.md):**
- 추천 색공간이 디스플레이 기기가 지원하는 색공간과 일치하는지
- TECH-SPEC.md의 디스플레이 색공간 설정과 일치하는지

**검증 출력:**
```
## Validation Report

| 항목 | 기준 (references) | 추천값 | 결과 |
|------|-------------------|--------|------|
| HAP Q ↔ Resolume Arena | 호환 (GPU 디코딩) | HAP Q | ✅ 호환 |
| 4K 60fps 출력 ↔ Resolume | 최대 8x 4K | 2x 4K | ✅ 가능 |
| sRGB ↔ 프로젝터 색공간 | sRGB 지원 | sRGB | ✅ 일치 |
| SSD I/O ↔ 비트레이트 | 500 MB/s | ~300 MB/s | ✅ 여유 |
```

### 4. Generate Content Spec Guide

콘텐츠 크리에이터에게 전달할 스펙 가이드:

```
── Content Specification Guide ──────
Project: {프로젝트명}

Resolution: 3840 × 2160 px
Frame Rate: 60 fps
Codec: HAP Q (.mov)
Color Space: sRGB
Duration: 10:00 (loop)

Delivery Format:
  Master: ProRes 4444 (.mov)
  Playback: HAP Q (.mov)

Estimated File Size: ~180 GB (playback)
Storage: SSD required (read speed > 500 MB/s)

⚠️ Notes:
  - H.264 사용 금지 (GPU 디코딩 불가, 프레임 드롭)
  - 알파 채널 필요 시 ProRes 4444 또는 HAP Alpha 사용
──────────────────────────────────────
```

스펙 가이드를 자동으로 `.docx`와 `.md`로 저장한다:

```
{프로젝트 폴더}/CONTENT-SPEC.md    ← 원본
{프로젝트 폴더}/CONTENT-SPEC.docx  ← Word
```

### 5. Post-generation

```
콘텐츠 스펙 가이드가 생성되었습니다.

다음 단계:
- /render-check 로 렌더링된 파일이 이 스펙에 맞는지 검증할 수 있습니다
- /color-check 로 색공간 설정을 확인할 수 있습니다
```

## Data Handoff (입출력 규격)

### Input (읽는 문서)
| 문서 | 읽는 필드 | 용도 |
|------|----------|------|
| `TECH-SPEC.md` | `## 콘텐츠 사양` → 미디어서버 종류 | 코덱 호환성 매칭 |
| `DISPLAY-CALC.md` | `## Content Spec for Creators` → 해상도, FPS | 콘텐츠 출력 해상도 기준 |
| `references/codec-reference.md` | 코덱별 호환성, 디코딩 부하, 비트레이트 | 코덱 추천 근거 |
| `references/color-spaces.md` | 디스플레이별 색공간 | 색공간 추천 |

### Output (쓰는 필드) → 다음 스킬이 읽는 구조
| 출력 필드 | 소비 스킬 | 읽는 방식 |
|----------|----------|----------|
| `## Content Specification Guide` → 해상도, 코덱, FPS, 색공간 | `/render-check` | 검증 기준으로 사용 |
| `## Content Specification Guide` → 색공간 | `/color-check` | 파이프라인 일관성 체크 기준 |

## Core Principles

1. **실수 방지가 목표** — 잘못된 코덱/해상도로 렌더링한 콘텐츠를 현장에서 발견하면 치명적이다.
2. **크리에이터 관점** — 기술자가 아닌 콘텐츠 제작자가 이해할 수 있는 언어로 쓴다.
3. **검증 필수** — 추천 코덱/해상도를 `references/` 데이터와 미디어서버 스펙으로 대조한다.
4. **레퍼런스 기반** — `references/` 데이터를 근거로 추천한다.
5. **한 번에 하나의 질문**
6. **언어를 따른다**
