---
name: render-check
description: "Render pipeline validator for media art content. Use when the user wants to verify that rendered files match the required specifications before delivery to site. Triggers on /render-check or requests for 렌더 검증, 렌더 체크, 파일 확인."
---

# /render-check — 렌더 파이프라인 검증

> **Role**: Technical Director
> **Trigger**: User invokes `/render-check` or asks about 렌더 검증, 렌더 체크, 파일 확인
> **Output**: 검증 결과 (터미널 출력)

## Purpose

렌더링된 미디어 파일이 프로젝트 사양에 맞는지 검증한다. 해상도, 코덱, 프레임레이트, 파일 크기, 오디오 채널 등을 체크하여 현장에서의 문제를 사전 방지한다.

## Behavior

### 1. Context Check

`{프로젝트 폴더}`에서 확인:
- `TECH-SPEC.md` — 콘텐츠 사양
- `DISPLAY-CALC.md` — 출력 해상도
- `CONTENT-SPEC.md` — `/content-pipeline`이 생성한 스펙

### 2. Input

**Q1 — 검증할 파일**
> "검증할 미디어 파일 경로를 알려주세요."

파일 또는 폴더 경로. 폴더의 경우 내부 미디어 파일을 모두 검사한다.

### 3. File Analysis

`ffprobe` (FFmpeg)를 사용하여 파일 정보를 추출한다:

```bash
ffprobe -v quiet -print_format json -show_format -show_streams {file}
```

추출 항목:
- 해상도, 코덱, 프레임레이트, 비트레이트
- 재생 시간, 파일 크기
- 색공간, 비트 뎁스
- 오디오 (있을 경우): 코덱, 샘플레이트, 채널 수

### 4. Validation

TECH-SPEC.md 또는 CONTENT-SPEC.md의 사양과 비교:

```
── Render Check Results ─────────────────
File: content_4k_final.mov

  ✅ Resolution: 3840×2160 (expected: 3840×2160)
  ✅ Codec: HAP Q (expected: HAP Q)
  ✅ Frame Rate: 60.00 fps (expected: 60 fps)
  ⚠️ Duration: 9:58 (expected: 10:00 — 2초 부족)
  ✅ File Size: 175 GB
  ❌ Color Space: Rec.709 (expected: sRGB)
  ✅ Audio: none (expected: none)

Summary: 1 error, 1 warning
──────────────────────────────────────────
```

검증 기준:
- ✅ 통과: 사양과 일치
- ⚠️ 경고: 허용 범위 내 차이 (1-2% 오차)
- ❌ 오류: 사양과 불일치

### 5. Recommendations

오류/경고가 있으면 해결 방법을 제시:

```
수정 필요:
1. ❌ Color Space: sRGB로 재렌더링하거나 LUT 적용
   - After Effects: Project Settings > Color > sRGB
   - Premiere: Sequence > Color Working Space > sRGB

2. ⚠️ Duration: 2초 부족 — 루프 재생 시 점프가 발생할 수 있음
   - 마지막 프레임을 freeze하거나 2초 추가 렌더링
```

## Core Principles

1. **자동 검증** — 사람이 일일이 확인하지 않아도 되는 자동화된 체크.
2. **ffprobe 기반** — 실제 파일 메타데이터를 읽어 검증한다 (추측하지 않는다).
3. **명확한 Pass/Fail** — 결과가 한눈에 보여야 한다.
4. **해결 방법 제시** — 문제를 지적만 하지 않고 해결법을 알려준다.
5. **언어를 따른다**
