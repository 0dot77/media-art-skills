---
name: reference-collect
description: "Reference collector for media art projects. Use when the user wants to gather visual, technical, or artistic references for a project. Triggers on /reference-collect or requests for 레퍼런스 수집, 사례 조사, reference research."
---

# /reference-collect — 레퍼런스 수집

> **Role**: Creative Director / Researcher
> **Trigger**: User invokes `/reference-collect` or asks about 레퍼런스 수집, 사례 조사, 참고 작품
> **Output**: `{프로젝트 폴더}/REFERENCES.md` (+ `.docx`)

## Purpose

미디어아트 프로젝트의 레퍼런스를 체계적으로 수집한다. 유사 작품 사례, 기술적 참고, 비주얼 영감 소스를 카테고리별로 정리하여 기획서나 제안서에 활용할 수 있도록 한다.

## Behavior

### 0. Save Location

> "문서를 어디에 저장할까요?"

### 1. Context Check

`{프로젝트 폴더}`에서 확인:
- `PROJECT.md` — 프로젝트 유형, 컨셉
- `REFERENCES.md` — 이미 있으면 추가할지 질문

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 레퍼런스 목적**
> "어떤 종류의 레퍼런스를 찾나요?"

- 유사 작품 사례 (같은 유형의 미디어아트)
- 비주얼 영감 (분위기, 스타일, 색감)
- 기술적 참고 (같은 기술 스택을 사용한 사례)
- 공간/설치 참고 (비슷한 규모/형태의 설치)
- 복합 (여러 종류)

**Q2 — 키워드/컨셉**
> "프로젝트의 핵심 키워드나 컨셉을 알려주세요."

PROJECT.md가 있으면 그 내용을 보여주고 보완만 받는다.

**Q3 — 특별히 참고할 아티스트/스튜디오가 있나요?**
> "이미 알고 있는 참고 작품이나 아티스트가 있나요?"

있으면 출발점으로 활용. 없으면 건너뛴다.

### 3. Research

웹 검색을 통해 레퍼런스를 수집한다:

**검색 소스**
- CreativeApplications.net
- Dezeen (installations)
- Vimeo (media art)
- YouTube (installation documentation)
- Behance / Dribbble (visual)
- Ars Electronica Archive
- SIGGRAPH Art Gallery
- teamLab, Random International, Rhizomatiks 등 주요 스튜디오

**각 레퍼런스에 대해 수집:**
- 작품명, 아티스트/스튜디오
- 연도, 장소
- 간단한 설명 (1-2문장)
- 기술 스택 (알 수 있으면)
- 이미지/영상 URL
- 프로젝트와의 관련성 (왜 참고하는지)

### 4. Categorize & Generate

수집된 레퍼런스를 카테고리별로 정리:

```markdown
# References — {프로젝트명}

## 유사 작품
1. **{작품명}** — {아티스트} ({연도})
   {설명}
   기술: {기술 스택}
   관련성: {왜 참고하는지}
   URL: {링크}

## 기술적 참고
...

## 비주얼 영감
...
```

```
{프로젝트 폴더}/REFERENCES.md    ← 원본
{프로젝트 폴더}/REFERENCES.docx  ← Word
```

### 5. Post-generation

```
레퍼런스가 수집되었습니다:
- 유사 작품 N건
- 기술적 참고 N건
- 비주얼 영감 N건

다음 단계:
- /proposal 로 레퍼런스를 포함한 기획서를 만들 수 있습니다
- /hardware-search 로 참고 작품에 사용된 장비를 조사할 수 있습니다
```

## Core Principles

1. **왜 참고하는지** — 단순 나열이 아닌, 프로젝트와의 관련성을 명시한다.
2. **출처 명시** — 모든 레퍼런스에 URL과 크레딧을 포함한다.
3. **실무 활용** — 기획서에 바로 넣을 수 있는 형태로 정리한다.
4. **한 번에 하나의 질문**
5. **언어를 따른다**
