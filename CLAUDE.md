# media-art-skills

이 프로젝트는 미디어아트 제작 업체(스튜디오)를 위한 Claude Code 스킬팩이다.

## 스킬 명령어

### 리서치/소싱
- `/model-search` — 3D 에셋 검색 (Sketchfab, CGTrader 등)
- `/reference-collect` — 프로젝트 레퍼런스 수집
- `/vendor-search` — 외주 업체/견적 조사
- `/hardware-search` — 장비 스펙 비교 검색

### 프로젝트 관리
- `/estimate` — 견적서 생성
- `/timeline` — 제작 일정표/마일스톤
- `/tech-spec` — 기술 사양서
- `/proposal` — 기획서/제안서

### 제작 파이프라인
- `/render-check` — 렌더 파이프라인 검증
- `/content-pipeline` — 미디어 에셋 관리 (해상도, 포맷, 코덱)
- `/display-calc` — 디스플레이 설치 계산
- `/color-check` — 색공간/캘리브레이션 검증

### 현장/설치
- `/site-survey` — 현장 답사 체크리스트/보고서
- `/install-guide` — 설치 매뉴얼
- `/troubleshoot` — 트러블슈팅 가이드
- `/maintenance` — 유지보수 일정/체크리스트

## 문서 저장 규칙

### 저장 경로
스킬 실행 시 **처음 한 번** 저장할 프로젝트 폴더를 질문한다:
> "문서를 어디에 저장할까요? (폴더 경로를 지정해주세요)"

지정된 경로 아래에 문서를 저장한다. 경로가 없으면 자동 생성한다. 이후 같은 세션에서는 동일 경로를 재사용한다. 이전에 저장한 문서가 해당 경로에 있으면 자동으로 읽어 컨텍스트로 사용한다.

### 출력 포맷
모든 문서는 **`.docx` (Word)** 형식으로 저장한다. 기획자가 바로 클라이언트에게 전달할 수 있는 실무 포맷이다.

- `.docx` 변환에는 `python-docx` 라이브러리를 사용한다
- 변환 스크립트: `scripts/md-to-docx.py`
- 마크다운 원본도 함께 저장하여 버전 관리가 가능하도록 한다
- 저장 구조:
  ```
  {지정된 경로}/
  ├── TECH-SPEC.docx       ← 클라이언트 전달용
  ├── TECH-SPEC.md         ← 원본 (버전 관리용)
  ├── ESTIMATE.docx
  ├── ESTIMATE.md
  └── ...
  ```

### 문서 종류

| 문서 | 생성 스킬 | 설명 |
|------|----------|------|
| PROJECT | 수동/proposal | 프로젝트 개요, 클라이언트, 일정, 팀 구성 |
| TECH-SPEC | /tech-spec | 기술 사양서 (디스플레이, 서버, 네트워크, 전력) |
| ESTIMATE | /estimate | 견적서 (장비비, 인건비, 외주비, 현장비) |
| TIMELINE | /timeline | 제작 일정표 |
| PROPOSAL | /proposal | 기획서/제안서 |
| DISPLAY-CALC | /display-calc | 디스플레이 설치 계산 |
| SITE-SURVEY | /site-survey | 현장 조사 보고서 |
| INSTALL-GUIDE | /install-guide | 설치 매뉴얼 |
| MAINTENANCE | /maintenance | 유지보수 일정 |

## 워크플로우 체인

```
수주:  /reference-collect → /hardware-search → /vendor-search → /estimate → /proposal
제작:  /tech-spec → /timeline → /content-pipeline → /render-check → /color-check
설치:  /site-survey → /display-calc → /install-guide → /troubleshoot → /maintenance
```

## 프로젝트 타입 감지

PROJECT.md의 내용을 보고 제작 유형을 자동 감지한다:

- "프로젝션 매핑" / "projection mapping" → 프로젝션 매핑
- "LED" → LED 디스플레이
- "인터랙티브" / "interactive" → 인터랙티브 설치
- "키네틱" / "kinetic" → 키네틱 아트 / 피지컬 컴퓨팅
- "몰입형" / "immersive" → 이머시브 공간
- 감지 불가 시 사용자에게 질문

## 행동 원칙

1. **실무 관점** — 클라이언트 납품과 현장 설치를 전제로 한다.
2. **수치 기반** — 비용, 일정, 스펙은 구체적 수치로 제시한다 (px, lm, A, ₩).
3. **비파괴적** — 기존 파일을 직접 수정하지 않는다. 새 파일 생성이 기본.
4. **한 번에 하나의 질문** — 사용자에게 여러 질문을 동시에 하지 않는다.
5. **이중 언어** — 클라이언트 문서는 한/영 모두 생성 가능. 사용자의 언어를 따른다.
