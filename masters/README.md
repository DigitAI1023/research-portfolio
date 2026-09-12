# 석사 과정

DGIST CASSP Lab. 고속 유선 통신 DSP의 알고리즘 설계부터 RTL 구현, FPGA 실시간 검증, 실칩 측정까지 다뤘습니다.

| 프로젝트 | 분야 | 대표 결과 |
|---|---|---|
| [KETI DMT Transceiver](keti/README.md) | 14 nm FinFET · 설계 → tape-out → 실측 | loopback **51 Gb/s** · 551 mW |
| [On-chip Adaptive Bit/Power Loading](adaptive-bpl/README.md) | 졸업연구 · 시뮬레이터 → RTL → FPGA | FXP BER **2.16 × 10⁻⁵** |
| [광대역 칩간 인터페이스](samsung-nrf/README.md) | 삼성미래기술 · NRF · TSMC 28 nm | 디코더 ON/OFF **BER 300배** |
| [RFSoC 하드웨어 검증](rfsoc/README.md) | ZCU111 · 단계별 검증 방법론 | 최대 **128-QAM** 성상 확인 |
| [Mixed-Signal 회로 설계](mixed-signal/README.md) | 수업 프로젝트 · 28 nm | LC VCO FoM **186.68 dBc/Hz** |

---

## 흐름

**DMT 송수신기** — KETI 과제에서 14 nm FinFET DMT TRX를 설계해 tape-out하고 실측까지 마쳤습니다. 여기서 쓴 bit/power loading은 호스트 PC가 계산해 SPI로 써 넣는 방식이었습니다.

**칩 안으로** — 졸업연구에서는 이 로딩 연산을 칩 내부 폐루프로 옮겼습니다. RX가 파일럿으로 부채널 오차를 측정하고, 같은 다이의 엔진이 나눗셈·로그 없이 정수 연산만으로 비트와 전력을 결정해 TX에 직접 씁니다.

**레인 확장** — 삼성미래기술·NRF 과제에서는 같은 DMT 데이터패스를 다중 레인으로 확장했습니다. 3개 lane을 4개 wire로 보내면서 레인 간 상관 잡음을 디지털 영역에서 상쇄합니다.

**검증 기반** — 위 세 과제 모두 RFSoC로 실시간 검증했습니다. 단계별 검증 방법론을 별도 페이지로 정리했습니다.

**회로 설계** — 수업 프로젝트로 BGR, LC VCO, Charge-Pump PLL을 28 nm에서 설계했습니다.

---

## 발표 · 논문

| 제목 | 발표 | 상태 |
|---|---|---|
| *Approximation of Cross Correlation for Energy-Efficient Synchronization of Discrete Multitone Wireline Transceivers* | 2025 IEEE ICCE-Asia | 발표 완료 |
| *A 51 Gb/s DAC/ADC-Based Discrete Multitone Wireline Transceiver Datapath in 14 nm FinFET* | IEEE ISSCC 2027 투고 | 심사 중 |

**→ [논문 · 발표 전체 보기](../publications/README.md)**

---

[← 메인으로](../README.md)
