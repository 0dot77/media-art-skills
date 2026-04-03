# Color Space Reference

미디어아트 설치의 색공간 설정 가이드.

## Common Color Spaces

| 색공간 | Gamut | 감마 | 용도 |
|--------|-------|------|------|
| sRGB | 표준 | 2.2 | 모니터, 웹, 일반 디스플레이 |
| Rec.709 | sRGB 동일 | 2.4 (BT.1886) | HD 영상 |
| DCI-P3 | 넓음 | 2.6 | 시네마, 고급 프로젝터 |
| Rec.2020 | 매우 넓음 | PQ/HLG | HDR 콘텐츠 |
| AdobeRGB | 넓음 | 2.2 | 인쇄, 사진 |

## Device-Specific Settings

### Projector
- 기본: **sRGB** / Rec.709
- 고급: DCI-P3 (Barco, Christie 설치용 프로젝터)
- 주의: 프로젝터 내장 색온도 설정 (6500K = D65 표준)
- 멀티 프로젝터 블렌딩 시 반드시 동일 색공간/색온도 통일

### LED Display
- 기본: **sRGB** (대부분의 LED 프로세서)
- 고급: Rec.709 / DCI-P3 (고급 프로세서)
- 밝기 캘리브레이션이 색공간보다 중요한 경우 많음
- 실내: 300-800 nits, 옥외: 5000+ nits

### Monitor
- 기본: **sRGB**
- 크리에이티브: AdobeRGB / DCI-P3

## Calibration Checklist

1. **White Point**: D65 (6500K) 기본
2. **Gamma**: 2.2 (sRGB) 또는 2.4 (영상)
3. **Brightness**: 환경광에 맞춰 조절
4. **Color Matching**: 멀티 디스플레이 시 색온도/감마 통일
5. **LUT**: 필요 시 3D LUT 적용 (CalMAN, DisplayCAL 등)

## HDR

| 표준 | Peak Brightness | 전송 함수 | 용도 |
|------|----------------|----------|------|
| HDR10 | 1,000 nits | PQ (ST.2084) | 고정 메타데이터 |
| HDR10+ | 4,000 nits | PQ + 동적 | 삼성 계열 |
| Dolby Vision | 10,000 nits | PQ + 동적 | 시네마, 고급 디스플레이 |
| HLG | 1,000 nits | HLG | 방송, 라이브 |

## 미디어아트 실무 팁

- **멀티 프로젝터**: 색온도/밝기 불일치가 가장 흔한 문제. 설치 후 현장 캘리브레이션 필수.
- **LED + 프로젝터 혼용**: 색공간과 감마 커브가 다르므로 반드시 LUT으로 매칭.
- **주변광 영향**: 암실이 아니면 명부(highlight) 대비가 떨어짐. 컨텐츠 밝기를 올리는 것보다 주변광 차단이 효과적.
- **모니터 작업 → 프로젝터 출력**: 반드시 현장에서 최종 확인. 모니터와 프로젝터의 색역/밝기 차이는 예상보다 큼.
