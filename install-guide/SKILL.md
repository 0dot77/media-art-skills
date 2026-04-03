---
name: install-guide
description: "Installation manual generator for media art projects. Use when the user wants to create a step-by-step installation guide with equipment inventory, cable lists, configuration, and startup/shutdown procedures. Triggers on /install-guide or requests for 설치 매뉴얼, 설치 가이드, installation guide."
---

# /install-guide — 설치 매뉴얼

> **Role**: Installation Lead
> **Trigger**: User invokes `/install-guide` or asks about 설치 매뉴얼, 설치 가이드, installation guide
> **Output**: `{프로젝트 폴더}/INSTALL-GUIDE.docx` + `INSTALL-GUIDE.md`

## Purpose

미디어아트 설치 현장에서 사용할 단계별 설치 매뉴얼을 생성한다. 장비 인벤토리, 케이블 리스트, 소프트웨어 설정, 캘리브레이션, 시스템 시작/종료 순서를 포함한다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

### 1. Context Check

`{프로젝트 폴더}`에서 다음 파일을 확인한다:
- `PROJECT.md` — 프로젝트 개요
- `TECH-SPEC.md` — 장비 목록, 네트워크 구성, 전력 **(이 문서가 핵심 소스)**
- `SITE-SURVEY.md` — 현장 조건, 접근 제약
- `DISPLAY-CALC.md` — 디스플레이 배치, 해상도 설정

이 스킬은 TECH-SPEC.md와 SITE-SURVEY.md가 있을 때 가장 효과적이다. 없으면 먼저 생성하도록 안내한다.

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 설치 팀 구성**
> "설치 팀 구성을 알려주세요."

- 인원 수, 역할, 경험 수준
- 문서의 디테일 수준을 결정 (비전문가 팀 → 더 상세하게)

**Q2 — 설치 일정**
> "설치 일정을 알려주세요."

- 총 설치 일수, 일별 작업 시간
- 데드라인 (클라이언트 오픈일)
- 제약 조건 (야간 작업 불가, 소음 제한 등)

**Q3 — 특별 현장 조건**
> "특별한 현장 조건이 있나요?"

SITE-SURVEY.md가 있으면 그 내용을 보여주고 추가 조건만 받는다.

### 3. Manual Generation

TECH-SPEC.md를 기반으로 다음 섹션을 생성한다:

**Section 1: Pre-installation Checklist**
- 장비 인벤토리 (장비명, 수량, S/N, 확인 체크박스)
- 필요 공구 목록
- 사전 설정 항목 (현장 도착 전 완료할 것)
- 배송/물류 체크리스트

**Section 2: Day-by-Day Schedule**
Q2의 일정에 맞춰 일별 작업을 배분:
- Day 1: 구조물, 마운트, 리깅
- Day 2: 장비 배치, 케이블링
- Day 3: 전원 투입, 네트워크 설정
- Day 4: 콘텐츠 로딩, 정렬, 캘리브레이션
- Day 5: 테스트, 트러블슈팅, 클라이언트 워크스루
(실제 일정에 맞게 조정)

**Section 3: Connection Diagram**
- 케이블 런 리스트 (From, To, 케이블 종류, 길이, 라벨)
- 네트워크 다이어그램 (IP 주소 포함)
- 신호 흐름도 (ASCII)

**Section 4: Software Configuration**
- 미디어서버 설정 (해상도, 출력, 매핑)
- 디스플레이 설정 (해상도, 주사율)
- 오토스타트 설정 (재부팅 시 자동 시작)
- 원격 접속 설정 (SSH, VNC, TeamViewer 등)

**Section 5: Calibration**
- 프로젝터 정렬 (해당 시)
- 색보정 절차
- 오디오 보정 (해당 시)
- 센서 보정 (해당 시)

**Section 6: Startup / Shutdown**
- 전원 ON 순서 (순서가 중요 — 장비 간 의존성 반영)
- 전원 OFF 순서
- 비상 정지 절차

**Section 7: Handover**
- 클라이언트 확인 항목
- 현장 스태프 교육 항목
- 문서 인수 목록

### 4. Generate

`templates/INSTALL-GUIDE.md.tmpl`을 기반으로 마크다운 → `.docx` 변환.

```
{프로젝트 폴더}/INSTALL-GUIDE.md    ← 원본
{프로젝트 폴더}/INSTALL-GUIDE.docx  ← Word
```

### 5. Post-generation

```
설치 매뉴얼이 생성되었습니다:
- {프로젝트 폴더}/INSTALL-GUIDE.docx (Word)
- {프로젝트 폴더}/INSTALL-GUIDE.md (원본)

장비 N개 / 케이블 N개 / 설치 N일

다음 단계:
- /troubleshoot 로 예상 문제 대응 가이드를 추가할 수 있습니다
- /maintenance 로 운영 기간 유지보수 일정을 만들 수 있습니다
```

## Core Principles

1. **현장에서 읽는 문서** — 순서대로 따라하면 설치가 완료되는 수준의 구체성.
2. **순서가 중요** — 전원, 케이블, 소프트웨어 순서를 명확히 한다. 잘못된 순서는 장비 손상 위험.
3. **체크리스트 형식** — 각 단계를 체크박스로 구성하여 완료 여부를 추적 가능하게 한다.
4. **한 번에 하나의 질문**
5. **언어를 따른다**
