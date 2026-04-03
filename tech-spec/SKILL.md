---
name: tech-spec
description: "Technical specification writer for media art projects. Use when the user wants to create a technical specification document for client delivery, internal production, or vendor RFQ. Triggers on /tech-spec or requests for 기술 사양서, 기술 스펙, technical specification."
---

# /tech-spec — 기술 사양서

> **Role**: Technical Director
> **Trigger**: User invokes `/tech-spec` or asks about 기술 사양서, 기술 스펙, technical specification
> **Output**: `{프로젝트 폴더}/TECH-SPEC.docx` + `TECH-SPEC.md`

## Purpose

미디어아트 프로젝트의 기술 사양서를 생성한다. 디스플레이, 컴퓨팅, 네트워크, 센서, 오디오, 전력 등 전체 시스템의 기술적 구성을 문서화하여 클라이언트 납품, 내부 제작, 외주 발주에 활용한다.

## Behavior

### 1. Context Check

### 0. Save Location

> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

사용자가 지정한 폴더를 `{프로젝트 폴더}`로 사용한다. 폴더가 없으면 자동 생성. 이후 같은 세션에서는 동일 경로를 재사용한다.

### 1. Context Check (계속)

`{프로젝트 폴더}`에서 다음 파일을 확인한다:
- `PROJECT.md` — 프로젝트 범위와 요구사항
- `SITE-SURVEY.md` — 현장 조건 (전력, 네트워크, 공간)
- `DISPLAY-CALC.md` — 디스플레이 계산 결과
- `TECH-SPEC.md` / `TECH-SPEC.docx` — 이미 있으면 업데이트할지 새로 만들지 질문

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 사양서 용도**
> "이 사양서의 용도는 무엇인가요?"

| 용도 | 문서 스타일 |
|------|-----------|
| 클라이언트 납품 (Client Delivery) | 정식 문서, 한/영 이중 언어 옵션, 비기술 용어 병행 |
| 내부 제작 (Internal Production) | 기술 디테일 최대, 전문 용어 OK |
| 외주 발주 (Vendor RFQ) | 산출물 중심, 수락 기준 명시 |

이 선택에 따라 문서 톤과 디테일 수준이 달라진다.

**Q2 — 시스템 구성**
> "프로젝트에 포함되는 시스템을 알려주세요."

해당하는 항목을 선택/설명:
- Display (프로젝터, LED, 모니터)
- Computing (미디어서버, 컨트롤 PC)
- Network (LAN, WiFi, 제어 네트워크)
- Sensor / Input (카메라, 뎁스센서, 터치, 기타)
- Audio (스피커, 앰프, DSP)
- Lighting (조명, DMX)
- Structure (구조물, 마운트, 리깅)

PROJECT.md가 있으면 자동 추론하고 확인만 받는다.

**Q3~QN — 시스템별 상세 (선택된 시스템만)**

각 시스템에 대해 **한 번에 하나씩** 질문한다. 이전 단계에서 읽은 컨텍스트 (DISPLAY-CALC.md, SITE-SURVEY.md 등)가 있으면 해당 정보를 보여주고 확인/보완만 받는다.

**Display 상세:**
> "디스플레이 시스템의 상세를 알려주세요."
- 유형, 모델, 수량, 해상도, 밝기, 배치
- DISPLAY-CALC.md가 있으면 자동 반영

**Computing 상세:**
> "미디어서버/컴퓨팅 장비를 알려주세요."
- 장비명, 사양, 역할 (재생, 렌더링, 제어 등)
- `references/hardware-db.md`의 미디어서버 목록 참조

**Network 상세:**
> "네트워크 구성을 알려주세요."
- 토폴로지 (스타, 체인 등)
- IP 대역, VLAN 분리 여부
- 프로토콜 (ArtNet, sACN, OSC, NDI, MIDI 등)

**Sensor 상세:**
> "센서/입력 시스템을 알려주세요."
- 센서 종류, 모델, 감지 범위, 인터페이스
- `references/hardware-db.md`의 센서 목록 참조

**Audio 상세:**
> "오디오 시스템을 알려주세요."
- 스피커 수량/배치, 채널 수, 앰프, DSP

**Power 계산 (자동):**
모든 장비 정보가 모이면 전력 소비량을 자동 계산:
```
총 소비전력 = Σ(각 장비 소비전력 × 수량)
필요 회로 = ceil(총 소비전력 / 단일 회로 용량)
```
SITE-SURVEY.md의 가용 전력과 비교하여 부족 여부를 경고한다.

### 3. Content Specification

`references/codec-reference.md`를 참조하여 콘텐츠 사양을 자동 생성:
- 해상도 (DISPLAY-CALC.md 기반)
- 포맷/코덱 (미디어서버 호환성 기반으로 추천)
- 프레임레이트
- 비트레이트 가이드라인
- 색공간 (`references/color-spaces.md` 참조)

### 4. Generate TECH-SPEC.md

`templates/TECH-SPEC.md.tmpl`을 기반으로 마크다운을 생성한 뒤, `scripts/md-to-docx.py`로 `.docx`로 변환한다.

```
{프로젝트 폴더}/TECH-SPEC.md    ← 원본
{프로젝트 폴더}/TECH-SPEC.docx  ← Word
```

**시스템 개요 다이어그램**: ASCII art로 전체 신호 흐름을 그린다.
```
[Media Server] --HDMI--> [Splitter] --HDMI--> [Projector 1]
                                    --HDMI--> [Projector 2]
[Control PC]   --LAN---> [Switch]  --LAN---> [Media Server]
                                   --LAN---> [Sensor PC]
[Sensor]       --USB---> [Sensor PC] --OSC--> [Media Server]
```

**클라이언트 납품용**인 경우:
- `{프로젝트 폴더}/TECH-SPEC-ko.docx` + `.md` (한국어)
- `{프로젝트 폴더}/TECH-SPEC-en.docx` + `.md` (영어)
이중 언어 버전을 생성할 수 있다 (사용자에게 확인).

### 5. Post-generation

```
기술 사양서가 생성되었습니다:
- {프로젝트 폴더}/TECH-SPEC.docx (Word)
- {프로젝트 폴더}/TECH-SPEC.md (원본)

시스템 구성: Display(2) + Computing(1) + Network + Sensor(3)
총 소비전력: 2,400W (20A 회로 1개 필요)

다음 단계:
- /estimate 로 이 사양 기반의 견적을 산출할 수 있습니다
- /display-calc 로 디스플레이 배치를 상세 계산할 수 있습니다
- /content-pipeline 으로 콘텐츠 제작 파이프라인을 설정할 수 있습니다
- /install-guide 로 설치 매뉴얼을 생성할 수 있습니다
```

## Core Principles

1. **시스템 전체를 본다** — 개별 장비가 아닌 시스템 간 연결과 신호 흐름을 중심으로 기술한다.
2. **수치 기반** — 모든 스펙은 측정 가능한 수치로 명시한다.
3. **용도에 맞는 톤** — 클라이언트에게는 쉽게, 내부에는 기술적으로, 외주에는 명확하게.
4. **전력은 반드시 계산** — 현장에서 가장 흔한 문제가 전력 부족이다. 항상 총 소비전력을 산출하고 가용 전력과 비교한다.
5. **한 번에 하나의 질문** — 시스템이 많아도 하나씩 질문한다.
6. **레퍼런스 활용** — references/ 디렉토리의 데이터를 근거로 활용한다.
7. **언어를 따른다** — 사용자의 언어로 대화하고 문서를 생성한다.
