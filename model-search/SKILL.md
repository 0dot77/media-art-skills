---
name: model-search
description: "3D asset search tool for media art production. Use when the user wants to find 3D models, textures, or CG assets from online marketplaces and free repositories. Triggers on /model-search or requests for 3D 모델 검색, 에셋 검색, 모델링 리서치."
---

# /model-search — 3D 에셋 검색

> **Role**: CG Artist / Technical Director
> **Trigger**: User invokes `/model-search` or asks about 3D 모델 검색, 에셋 검색, 모델링 리서치
> **Output**: 검색 결과 (터미널) 또는 `{프로젝트 폴더}/MODEL-SEARCH.md`

## Purpose

미디어아트 프로젝트에 필요한 3D 모델, 텍스처, CG 에셋을 온라인 마켓플레이스와 무료 저장소에서 검색한다. 키워드, 카테고리, 포맷, 가격대 등을 기준으로 적합한 에셋을 찾아 비교한다.

## Behavior

### 1. Context Check

`{프로젝트 폴더}`에서 확인 (있으면):
- `PROJECT.md` — 프로젝트 컨셉, 스타일
- `TECH-SPEC.md` — 필요 포맷, 렌더 엔진

### 2. Input Gathering (한 번에 하나씩)

**Q1 — 무엇을 찾나요?**
> "어떤 3D 에셋을 찾고 있나요?"

구체적 키워드 또는 설명. 예: "도시 야경 건물", "유기적 형태 조형물", "파티클 이펙트"

**Q2 — 용도**
> "에셋을 어떻게 사용하나요?"

- 그대로 사용 (As-is) → 완성도 높은 에셋 필요
- 수정/커스텀 → 편집 가능한 포맷 필요
- 레퍼런스용 → 시각적 참고만

**Q3 — 조건**
> "포맷, 가격, 기타 조건이 있나요?"

- 포맷: .fbx, .obj, .gltf, .usdz, .blend 등
- 가격: 무료 / 유료 OK / 예산 한도
- 렌더 엔진: Blender, Cinema 4D, Unreal, Unity 등
- 라이선스: 상업적 사용 가능 여부

### 3. Search

웹 검색을 통해 다음 플랫폼에서 에셋을 탐색한다:

**무료 플랫폼**
- Sketchfab (Free section)
- TurboSquid (Free section)
- Poly Haven (CC0)
- ambientCG (CC0 텍스처/HDRI)
- Mixamo (애니메이션/캐릭터)

**유료 플랫폼**
- Sketchfab Store
- TurboSquid
- CGTrader
- Quixel Megascans
- KitBash3D

각 결과에 대해:
- 이름, 썸네일 URL, 가격
- 폴리곤 수, 포맷, 텍스처 포함 여부
- 라이선스 유형
- 다운로드 링크

### 4. Output

```
── 3D Asset Search Results ──────────────
Query: "futuristic building low poly"
Found: 12 results

1. Sci-Fi City Block
   Platform: Sketchfab | Price: Free
   Polygons: 45K | Format: .fbx, .obj, .gltf
   License: CC BY 4.0
   URL: [link]

2. Cyberpunk Buildings Pack
   Platform: TurboSquid | Price: $49
   Polygons: 120K | Format: .fbx, .obj, .max
   License: Royalty-free
   URL: [link]

...
──────────────────────────────────────────
```

필요 시 `{프로젝트 폴더}/MODEL-SEARCH.md`로 저장.

### 5. Post-generation

```
다음 단계:
- /vendor-search 로 커스텀 모델링 외주 업체를 찾을 수 있습니다
- /reference-collect 로 비주얼 레퍼런스를 더 수집할 수 있습니다
```

## Core Principles

1. **라이선스 확인 필수** — 상업적 사용 가능 여부를 반드시 명시한다.
2. **실용성 중심** — 예쁜 것보다 프로젝트에 맞는 것을 우선한다 (포맷, 폴리곤 수, 호환성).
3. **가격 투명성** — 무료/유료를 명확히 구분한다.
4. **한 번에 하나의 질문**
5. **언어를 따른다**
