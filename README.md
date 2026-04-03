# media-art-skills

미디어아트 제작 업체(스튜디오)를 위한 [Claude Code](https://claude.com/claude-code) 스킬팩.

수주부터 설치·운영까지, 미디어아트 프로덕션의 실무 워크플로우를 자동화하는 16종의 스킬을 제공한다.

## 누구를 위한 건가요?

- 미디어아트 제작 업체의 **기획자 / PM**
- **테크니컬 디렉터 (TD)**
- **설치 엔지니어**

기존 [medialab-stack](https://github.com/0dot77/medialab-stack)(개인 아티스트/교육자용)과 겹치지 않으며, **클라이언트 납품과 현장 설치**를 전제로 설계되었다.

## 설치

### 1. 요구사항

- [Claude Code](https://claude.com/claude-code) CLI 또는 데스크톱 앱
- Python 3.10+ (`python-docx` 라이브러리 사용)

### 2. 저장소 클론

```bash
git clone https://github.com/0dot77/media-art-skills.git
cd media-art-skills
```

### 3. 스킬 등록

`setup` 스크립트가 python-docx 설치 + 스킬 심링크 등록을 한번에 처리한다.

#### 글로벌 설치 (어디서든 사용)

```bash
./setup
```

모든 스킬이 `~/.claude/skills/`에 등록되어, 어떤 폴더에서든 사용 가능.

#### 로컬 설치 (특정 프로젝트에서만 사용)

```bash
./setup --local /path/to/my-project
```

해당 프로젝트의 `.claude/skills/`에만 등록되어, 그 폴더에서 Claude Code를 실행할 때만 스킬이 활성화된다.

### 4. Claude Code 재시작

설치 후 Claude Code를 재시작하면 슬래시 명령어로 스킬을 사용할 수 있다.

## 스킬 목록

### 리서치/소싱
| 스킬 | 설명 |
|------|------|
| `/model-search` | 3D 에셋 검색 (Sketchfab, CGTrader 등) |
| `/reference-collect` | 프로젝트 레퍼런스 수집 |
| `/vendor-search` | 외주 업체/견적 조사 |
| `/hardware-search` | 장비 스펙 비교 검색 |

### 프로젝트 관리
| 스킬 | 설명 |
|------|------|
| `/estimate` | 견적서 생성 (장비비, 인건비, 외주비, 현장비) |
| `/timeline` | 제작 일정표/마일스톤 |
| `/tech-spec` | 기술 사양서 |
| `/proposal` | 기획서/제안서 |

### 제작 파이프라인
| 스킬 | 설명 |
|------|------|
| `/render-check` | 렌더 파이프라인 검증 |
| `/content-pipeline` | 미디어 에셋 관리 (해상도, 포맷, 코덱) |
| `/display-calc` | 디스플레이 설치 계산 (throw, 해상도, 블렌딩) |
| `/color-check` | 색공간/캘리브레이션 검증 |

### 현장/설치
| 스킬 | 설명 |
|------|------|
| `/site-survey` | 현장 답사 체크리스트/보고서 |
| `/install-guide` | 설치 매뉴얼 자동 생성 |
| `/troubleshoot` | 트러블슈팅 가이드 |
| `/maintenance` | 유지보수 일정/체크리스트 |

## 워크플로우

스킬들은 세 가지 체인으로 연결된다:

```
수주:  /reference-collect → /hardware-search → /vendor-search → /estimate → /proposal
제작:  /tech-spec → /timeline → /content-pipeline → /render-check → /color-check
설치:  /site-survey → /display-calc → /install-guide → /troubleshoot → /maintenance
```

각 스킬이 생성하는 문서는 다음 스킬의 컨텍스트로 자동 연결된다.

## 문서 출력

### 저장 경로
스킬 실행 시 **저장할 프로젝트 폴더**를 지정한다. 같은 세션에서는 동일 경로를 재사용한다.

### 출력 포맷
모든 문서는 **`.docx` (Word)** + **`.md` (마크다운)** 두 가지로 저장된다:

```
{지정한 폴더}/
├── ESTIMATE.docx        ← 클라이언트 전달용 (Word)
├── ESTIMATE.md          ← 원본 (버전 관리용)
├── TECH-SPEC.docx
├── TECH-SPEC.md
└── ...
```

- `.docx`는 기획자가 바로 클라이언트에게 전달할 수 있는 실무 포맷
- `.md`는 Git 등으로 버전 관리가 가능한 원본

## 레퍼런스 데이터

`references/` 폴더에 미디어아트 실무 레퍼런스가 내장되어 있다:

| 파일 | 내용 |
|------|------|
| `hardware-db.md` | 프로젝터, LED, 미디어서버, 센서 스펙 DB |
| `codec-reference.md` | 비디오 코덱/포맷 선택 가이드 |
| `color-spaces.md` | 색공간 설정 가이드 |
| `vendor-categories.md` | 외주 업체 분류 체계 |

스킬들이 자동으로 참조하며, 직접 열어서 참고할 수도 있다.

## 프로젝트 구조

```
media-art-skills/
├── CLAUDE.md              # Claude Code 행동 원칙
├── README.md              # 이 파일
├── setup                  # 설치 스크립트
├── scripts/
│   └── md-to-docx.py      # Markdown → Word 변환
├── templates/             # 문서 템플릿 (8종)
├── references/            # 장비/코덱/색공간 레퍼런스
│
├── estimate/SKILL.md      # 스킬 정의 파일들
├── display-calc/SKILL.md
├── tech-spec/SKILL.md
└── ... (16개 스킬)
```

## 기여

버그 리포트나 새 스킬 제안은 [Issues](https://github.com/0dot77/media-art-skills/issues)에 남겨주세요.

## 라이선스

MIT
