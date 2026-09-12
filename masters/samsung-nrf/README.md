# 다중 칩 연산 시스템용 광대역 칩간 인터페이스

| | |
|---|---|
| 과제명 | 다중 칩 연산 시스템을 위한 광대역 칩간 인터페이스 설계 기술 개발<br>*Wide-bandwidth Chip-to-Chip Interface Design Technology for Multi-Chip Computing Systems* |
| 지원 | 삼성전자 미래기술육성사업 (SRFC-IT2301-01) · 한국연구재단 석사과정생연구장려금 |
| 본인 역할 | DMT DSP RTL 설계, 다중 레인 인코더 설계, RFSoC 실시간 검증 환경 구축 |

차동 신호 방식은 wire 2개로 lane 1개를 전송하므로 핀 효율이 절반입니다. 이 과제는 **N개 lane을 (N+1)개 wire로** 전송하는 구조에 DMT 변조를 결합해, 핀을 늘리지 않고 대역폭을 올리면서 레인 간 간섭(FEXT)을 상쇄하는 것을 목표로 합니다. 최종 목표는 7-lane 8-wire입니다.

같은 과제가 삼성전자 미래기술육성사업과 한국연구재단 석사과정생연구장려금 두 곳에서 지원됩니다. 아래 내용은 본인이 작성한 **석사과정생연구장려금 1차년도 연차보고서**와 **미래기술육성사업 2026년 상반기 반기보고서** 중 본인 수행분입니다.

---

## 1. 목표 구조 — 7-lane 8-wire

![7L8W transceiver block diagram](figures/arch-7l8w.png)

Die 1의 DSP TX가 적응형 bit/power loading으로 7개 lane을 만들고, 7-lane 8-wire 인코더가 이를 8개 wire로 확산합니다. 8번째는 redundancy lane입니다. Die 2에서는 clipping detection이 각 wire의 포화 여부를 판단해, 정상 구간에서는 디코더 출력을, 포화 구간에서는 raw 수신값을 선택하도록 MUX를 제어합니다. 클리핑이 디코딩 오류로 번지는 것을 막는 구조입니다.

### 3-lane 4-wire 인코더 — 실제 구현한 중간 단계

2026년 상반기에는 N=3 구성으로 인코더를 설계하고 DMT TX 데이터패스에 통합했습니다.

Walsh-Hadamard 행렬 H₄로 3개 레인을 4개 wire에 직교 확산합니다. 가감산만 쓰며 튜닝 계수가 없습니다.

```
w0 = +L0 +L1 +L2
w1 = -L0 +L1 -L2
w2 = +L0 -L1 -L2
w3 = -L0 -L1 +L2
```

수신단은 (1/4)·H₄ 역변환만으로 3개 레인을 복원합니다. 구조적으로 직교 리던던시가 확보되므로 별도 학습이나 계수 적응이 필요 없습니다. 4개 wire 공통 gain 단으로 진폭을 정규화하고, fixed-point round/shift/clip으로 오버플로우를 막았습니다.

기존 DMT TX 데이터패스(Bit loader → Power loader → PS-IFFT → Shift-mux → CP insertion → Sync insertion → Zero padding)에 인코더를 끼워 넣어 3-lane TX 파이프라인을 완성했고, MATLAB 시뮬레이션과 **bit-exact 일치**를 확인했습니다.

---

## 2. 단일 레인 DMT DSP RTL

![Single-lane DMT TRX datapath](figures/datapath-single-lane.png)

TX는 QAM 변조 → power loading → IFFT → CP 삽입, RX는 CP 제거 → FFT → FDE → QAM 복조입니다. 채널 응답 H(f)에 따라 부반송파마다 다른 QAM 차수를 싣습니다.

**면적 최적화** — 단일 레인은 32-way 128-tap IFFT/FFT이며, Multi-Path Delay Feedback(MDF) 구조를 써서 fully-parallel 대비 면적을 크게 줄였습니다.

**연산 정밀도 최적화** — MATLAB 시뮬레이션 결과를 근거로 IFFT/FFT와 비트/파워 로딩 계수의 연산 해상도(resolution bit)를 결정해 하드웨어 자원 사용량을 최소화했습니다.

MATLAB에서 VHDL을 자동 생성하는 프레임워크를 만들어 per-tap 파이프라인을 파라미터화(Ktap = 32 인스턴스)하고 timing closure를 맞췄습니다.

---

## 3. RFSoC 실시간 검증 환경

![RFSoC verification block diagram](figures/rfsoc-verify-block.png)

검증 대상(DUT)은 TX DSP · BIDI · RX DSP입니다. PC가 UART로 ZYNQ PS와 통신하고, GPIO로 BIDI를 제어합니다. TX 입력은 BRAM(16 kSa)의 시험 파형과 TX DSP 출력 중에서 고릅니다. 클럭 도메인은 제어 100 MHz, DSP 64 MHz, BIDI 16 MHz, 변환기 인터페이스 256 MHz로 나눴고, 32-to-8 / 8-to-32 FIFO로 폭을 맞췄습니다. DAC/ADC 실제 입출력은 14b·12b이지만 AXI-Stream 버스에 맞추려고 16b로 확장합니다.

### 2보드 구성

보드 1대 안에서 loopback으로 도는 검증은 칩간 통신이 아닙니다. 실제 조건에 맞추려고 ZCU111 **2대**를 각각 TX와 RX로 놓고 연동했습니다.

<table>
<tr>
<td width="50%"><img src="figures/setup-2board-diagram.png" alt="Two-board setup diagram"></td>
<td width="50%"><img src="figures/setup-2board-photo.jpg" alt="Two-board setup photo"></td>
</tr>
<tr>
<td>PC ↔ ZCU111 TX + RFMC500(3.2 GS/s DAC) → ISI Board → RFMC500(3.2 GS/s ADC) + ZCU111 RX ↔ PC.</td>
<td>실제 구성. 100 MHz 기준 클럭을 power splitter로 나누고 한쪽에 phase shifter를 넣어 두 보드의 ref clock을 맞춥니다.</td>
</tr>
</table>

보드마다 내부 클럭을 쓰면 두 보드 사이에 클럭 스큐가 생깁니다. 외부 클럭 소스(VSG25A) 하나를 공유하도록 바꾸고, phase shifter로 보드 간 위상차를 보정해 동기화를 맞췄습니다.

---

## 4. 실시간 검증 결과

![RFSoC measurement results](figures/result-rfsoc-ber.png)

ZCU111 2보드에서 단일 레인 DMT TX/RX DSP를 실시간 구동한 결과입니다.

| 항목 | 값 |
|---|---|
| BER | **1.51 × 10⁻⁸** |
| EVM (32-QAM) | −28.52 dB |

(a) 부채널별 BER이 측정 BER 선 부근에서 고르게 분포합니다. (b) 32-QAM 성상의 32개 점이 분리되어 나타납니다. (c) FDE 출력 수직 히스토그램의 결정 레벨이 모두 분리됩니다.

DSP datapath 로직이 하드웨어에서 안정적으로 동작함을 확인했습니다.

---

## 5. 본인 수행 범위

수행한 항목입니다.

- N-lane N-wire 구조 MATLAB 모델링, FEXT 상쇄 알고리즘과 전송 효율 정량 검증
- 비트/파워 로딩을 포함한 DMT DSP RTL 설계 — 단일 레인 구현 완료
- Walsh-Hadamard 기반 3L4W 인코더 설계 및 DMT TX 데이터패스 통합
- MATLAB → VHDL 자동 생성 프레임워크 구축, fixed-point 모델과 bit-exact 대조
- ZCU111 2보드 외부 클럭 동기화 실시간 검증 환경 구축 및 단일 레인 IP 하드웨어 검증

**같은 보고서에 실린 다음 성과는 과제 참여자의 다른 IP이며 본인 수행분이 아닙니다.**

- 7-bit binary-weighted DAC + 4:1 serializer, TSMC 28 nm tape-out
- GROSC 기반 Time-based ADC(VTC + TDC), Time-Interleaving 구조, TSMC 28 nm tape-out

---

## 6. 다음 단계

| 항목 | 내용 |
|---|---|
| 멀티 레인 RTL 통합 | 7L8W 인코더·디코더 블록 통합, 레인 간 FEXT 상쇄 로직 RTL 구현 |
| 2보드 실시간 검증 | ISI 보드를 적용해 다양한 채널 조건에서 부채널별 로딩 동작과 인접 레인 간섭 상쇄를 목표 BER 기준으로 분석 |
| 논리 합성 분석 | Synopsys Design Compiler로 면적·전력·타이밍 분석, critical path 확인, netlist에 SDF를 적용한 타이밍 시뮬레이션 |

---

## 관련 페이지

- [On-chip Adaptive Bit/Power Loading](../adaptive-bpl/README.md) — 이 과제의 bit/power loading을 칩 내부 폐루프로 옮긴 졸업연구
- [RFSoC 하드웨어 검증](../../engineering/rfsoc/README.md)

## 자료 출처

figure는 본인이 작성한 **2026년도 석사과정생연구장려금 1차년도 연차보고서**의 그림 1~6입니다. 3L4W 인코더 서술은 **미래기술육성사업 2026년 상반기 반기보고서**의 본인 수행분입니다. 상세 기록은 [`figure-sources.json`](figure-sources.json)에 있습니다.
