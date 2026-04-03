# Hardware Database

미디어아트 설치에 자주 사용되는 장비 레퍼런스.

## Projectors

### Short Throw / Ultra Short Throw
| 모델 | 해상도 | 밝기(lm) | Throw Ratio | 용도 |
|------|--------|----------|-------------|------|
| Epson EB-810E | 4K UHD | 5,000 | 0.16:1 | UST, 벽면 설치 |
| Optoma ZH606E | 1080p | 6,000 | 0.8:1 | 중거리 매핑 |
| BenQ LU935ST | WUXGA | 5,500 | 0.5:1 | 소규모 매핑 |

### High Brightness (Installation)
| 모델 | 해상도 | 밝기(lm) | Throw Ratio | 용도 |
|------|--------|----------|-------------|------|
| Panasonic PT-RQ25K | 4K+ | 25,000 | 1.2-2.0:1 (렌즈별) | 대형 프로젝션 매핑 |
| Christie CP4440-RGB | 4K | 40,000 | 렌즈별 | 건물 외벽 매핑 |
| Barco UDX-4K40 | 4K | 40,000 | 렌즈별 | 대형 이벤트 |
| Epson EB-PU2220B | WUXGA | 20,000 | 렌즈별 | 중대형 상설 |

### Throw Ratio 계산
- **Image Width = Throw Distance / Throw Ratio**
- 예: TR 1.5:1, 거리 6m → 이미지 폭 4m
- 예: TR 0.38:1, 거리 1m → 이미지 폭 2.63m

## LED Displays

### Indoor (Direct View)
| Pixel Pitch | 해상도/m² | 최적 시청거리 | 밝기(nits) | 용도 |
|-------------|----------|-------------|-----------|------|
| P1.2 | 694,444 | 1.2m+ | 600-800 | 컨트롤룸, 근접 관람 |
| P1.5 | 444,444 | 1.5m+ | 600-800 | 전시장, 로비 |
| P2.0 | 250,000 | 2.0m+ | 800-1,200 | 미디어 파사드(실내) |
| P2.5 | 160,000 | 2.5m+ | 800-1,200 | 무대, 이벤트 |
| P3.0 | 111,111 | 3.0m+ | 1,000-1,500 | 대형 공간 |

### Outdoor
| Pixel Pitch | 최적 시청거리 | 밝기(nits) | 용도 |
|-------------|-------------|-----------|------|
| P4.0 | 4m+ | 5,000-8,000 | 옥외 미디어 파사드 |
| P6.0 | 6m+ | 5,000-8,000 | 건물 외벽 |
| P10.0 | 10m+ | 8,000+ | 원거리 사이니지 |

### LED 캐비닛 일반 크기
- 500 x 500mm (실내 일반)
- 500 x 1000mm (실내 대형)
- 960 x 960mm (옥외 일반)

### LED 해상도 계산
- **가로 픽셀 = 가로(mm) / Pixel Pitch(mm)**
- 예: P2.5, 가로 5m → 5000/2.5 = 2000px

## Media Servers

| 제품 | 출력 | 용도 | 비고 |
|------|------|------|------|
| disguise d3 | 최대 16x 4K | 프로젝션 매핑, LED | 워프/블렌딩 내장 |
| Resolume Arena | 최대 8x 4K | 실시간 VJ, 매핑 | 소프트웨어 기반 |
| Watchout | 최대 12x FHD | 멀티 디스플레이 | 타임라인 기반 |
| TouchDesigner | 유연 | 인터랙티브 | 실시간 처리 |
| Brightsign | 1x 4K | 단순 재생 | 독립형 플레이어 |
| Mac Mini (M-series) | 1-2x 4K | 소규모 설치 | 비용 효율적 |

## Sensors

| 유형 | 모델 | 범위 | 용도 |
|------|------|------|------|
| Depth Camera | Intel RealSense D455 | 0.4-6m | 인터랙티브, 트래킹 |
| Depth Camera | Azure Kinect DK | 0.5-5.5m | 바디 트래킹 |
| LiDAR | Livox Mid-360 | 0.1-40m | 공간 스캐닝, 대형 인터랙티브 |
| IR Sensor | PIR 모듈 | 3-7m | 존재 감지 |
| Pressure Mat | 커스텀 | 접촉 | 바닥 인터랙션 |
| Camera | Basler ace 2 | 가변 | CV 기반 트래킹 |
| Mic | Shure MX395 | 가변 | 사운드 리액티브 |
