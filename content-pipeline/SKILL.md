---
name: content-pipeline
description: "Content pipeline manager for media art installations. Use when the user wants to verify media asset specifications, check codec compatibility, or set up content delivery workflows. Triggers on /content-pipeline or requests for 콘텐츠 파이프라인, 미디어 에셋, 코덱, 포맷."
---

# /content-pipeline — 미디어 에셋 관리

> **Role**: Technical Director
> **Trigger**: User invokes `/content-pipeline` or asks about 콘텐츠 파이프라인, 미디어 에셋, 코덱, 포맷
> **Output**: 콘텐츠 스펙 가이드 (터미널 출력 또는 `{프로젝트 폴더}/CONTENT-SPEC.docx`)

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

필요시 `.docx`로도 저장.

### 5. Post-generation

```
콘텐츠 스펙 가이드가 생성되었습니다.

다음 단계:
- /render-check 로 렌더링된 파일이 이 스펙에 맞는지 검증할 수 있습니다
- /color-check 로 색공간 설정을 확인할 수 있습니다
```

## Core Principles

1. **실수 방지가 목표** — 잘못된 코덱/해상도로 렌더링한 콘텐츠를 현장에서 발견하면 치명적이다.
2. **크리에이터 관점** — 기술자가 아닌 콘텐츠 제작자가 이해할 수 있는 언어로 쓴다.
3. **레퍼런스 기반** — `references/` 데이터를 근거로 추천한다.
4. **한 번에 하나의 질문**
5. **언어를 따른다**
