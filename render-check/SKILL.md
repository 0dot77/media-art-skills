---
name: render-check
description: "Render pipeline validator for media art content. Use when the user wants to verify that rendered files match the required specifications before delivery to site. Triggers on /render-check or requests for 렌더 검증, 렌더 체크, 파일 확인."
---

# /render-check — 렌더 파이프라인 검증

> **Role**: Technical Director
> **Trigger**: User invokes `/render-check` or asks about 렌더 검증, 렌더 체크, 파일 확인
> **Output**: `{프로젝트 폴더}/RENDER-CHECK.docx` + `RENDER-CHECK.md`

## Purpose

렌더링된 미디어 파일이 프로젝트 사양에 맞는지 검증한다. 해상도, 코덱, 프레임레이트, 파일 크기, 오디오 채널 등을 체크하여 현장에서의 문제를 사전 방지한다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

사용자가 지정한 폴더를 `{프로젝트 폴더}`로 사용한다. 이후 같은 세션에서는 동일 경로를 재사용한다.

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

### 5.5. Cross-Validation (교차 검증)

파일 분석 결과를 `references/` 데이터와 추가 대조한다:

**코덱 호환성 (references/codec-reference.md):**
- 파일의 실제 코덱이 CONTENT-SPEC.md에서 추천한 코덱과 일치하는지
- 해당 코덱이 미디어서버에서 지원되는지 (TECH-SPEC.md의 미디어서버 참조)
  - 비호환 코덱 감지 시: ❌ + 트랜스코딩 명령어 제시 (ffmpeg)

**비트레이트 vs 스토리지 I/O (references/codec-reference.md):**
- 파일 비트레이트가 SSD/HDD 읽기 속도 한계 이내인지
  - 초과 시: ⚠️ + "프레임 드롭 위험. SSD 필요" 또는 비트레이트 낮은 코덱 제안

**색공간 (references/color-spaces.md):**
- 파일의 색공간이 디스플레이 기기의 색공간과 일치하는지
  - 불일치 시: ❌ + 색공간 변환 방법 제시 (소프트웨어별 메뉴 경로)

## Data Handoff (입출력 규격)

### Input (읽는 문서)
| 문서 | 읽는 필드 | 용도 |
|------|----------|------|
| `CONTENT-SPEC.md` | 해상도, 코덱, FPS, 색공간 | 검증 기준 (1차) |
| `TECH-SPEC.md` | 콘텐츠 사양, 미디어서버 종류 | 검증 기준 (2차), 호환성 체크 |
| `DISPLAY-CALC.md` | 출력 해상도 | 해상도 검증 기준 |
| `references/codec-reference.md` | 코덱 호환성, 비트레이트 가이드라인 | 교차 검증 |
| `references/color-spaces.md` | 색공간 기준 | 색공간 교차 검증 |

## Core Principles

1. **자동 검증** — 사람이 일일이 확인하지 않아도 되는 자동화된 체크.
2. **ffprobe 기반** — 실제 파일 메타데이터를 읽어 검증한다 (추측하지 않는다).
3. **교차 검증** — CONTENT-SPEC 기준뿐 아니라 `references/` 데이터로 이중 검증한다.
4. **명확한 Pass/Fail** — 결과가 한눈에 보여야 한다.
5. **해결 방법 제시** — 문제를 지적만 하지 않고 해결법을 알려준다.
6. **언어를 따른다**
