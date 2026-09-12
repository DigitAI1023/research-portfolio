# KETI DMT Transceiver

DAC/ADC 기반 DMT 송수신기. 채널별 SNR에 따른 비트·전력 할당과 FDE를 통해 주파수 선택적 채널에 대응합니다. **삼성 14 nm FinFET 공정으로 tape-out**했습니다.

**사용 도구** — MATLAB (고정소수점 모델링 · SPI 제어 시퀀싱) · VHDL · ModelSim · Cadence Xcelium (post-simulation) · Synopsys Design Compiler

**측정 장비** — Keysight M8196A AWG · 샘플링 오실로스코프 · real-time 오실로스코프 · Total Phase Cheetah SPI Host Adapter

---

## 본인 담당 범위

여러 기관이 블록을 나눠 맡은 공동 과제입니다. **본인 담당은 TX / RX DMT DSP입니다.**

| 블록 | 담당 |
|---|---|
| **TX / RX DMT DSP** | **본인** — 고정소수점 모델링, VHDL RTL, ModelSim·Xcelium 검증, DC 합성 |
| DAC · ADC | 한양대학교 |
| PLL · SPI | KETI |
| PnR | 외주 |
| PCB 보드 | 외주 |

PnR과 PCB는 외주로 진행됐습니다. 넘겨줄 RTL의 제약과 핀 배치를 제대로 협의하기 위해 back-end 흐름을 따로 익혔고, 그 과정은 [TSMC 28 nm Back-end Flow 실습](../pnr-28nm/README.md)에 정리했습니다.

> 아래 성능은 위 블록들이 합쳐진 **시스템 전체 결과**입니다.
>
> 이 페이지의 figure와 수치는 **IEEE ISSCC 2027 투고 원고**에서 가져왔습니다. ASSCC 2026에 먼저 투고했으나 채택되지 않았고, 보강해 ISSCC 2027에 재투고한 **심사 중** 원고입니다. 자세한 내용은 [논문 · 발표 페이지](../../publications/README.md)를 참고하세요.

## 설계 IP 구조

![TRX architecture](figures/architecture.png)

TX는 비트·전력 로딩, IFFT, CP 및 동기화 시퀀스 삽입을 수행하고, RX는 동기화, CP 제거, FFT, FDE와 QAM 복조를 수행합니다.

![FFT and scaling](figures/fft-and-scaling.png)

128-tap FFT/IFFT 구조, 고정소수점 스케일링 및 가변 CP를 설명하는 원고 figure입니다.

## Layout과 die photo

![Layout and die](figures/layout-die.png)

원고 Fig. 7. TX/RX DSP와 데이터 변환기 위치 및 DSP 면적 구성을 함께 제시합니다.

## PCB layout

![PCB top](figures/pcb-page-1.png)

![PCB bottom](figures/pcb-page-4.png)

![PCB component legend](figures/pcb-page-6.png)

DMT_TRX8.pdf의 top copper, bottom copper, 상면 부품 표기 도면입니다. PDF의 아래쪽 여백을 제외해 렌더링했습니다. 함께 제공된 DMT_TRX8.brd와 DMT_TX_RX.DSN은 PCB 원본 설계 자료로 보관하며, 본 페이지에서는 도면을 통해 보드 구성을 보여줍니다. PCB 설계 담당 범위는 별도 확인이 필요합니다.

## 모델링과 RTL 검증

1차년도 보고서 16–18·23페이지는 고정소수점 모델링, power-loader 정밀도 검토 및 TX/RX RTL 검증을 설명합니다. 초기 설계의 시뮬레이션 결과와 이후 원고의 실칩 측정을 구분해 개발 과정으로 연결합니다.

보고서에 제시된 128 Gb/s는 과제 목표이며 본 포트폴리오의 실측 달성값으로 사용하지 않습니다.

## 실측 환경

![Setup](figures/measurement-setup.png)

![Lab](figures/lab-setup.jpg)

![Boards](figures/boards.jpg)

측정 발표자료 2페이지에서 추출한 실험실 전경과 보드 사진입니다. 원고 결과와의 세부 배선·측정 시점 일치 여부는 검토 중입니다.

| 장비 | 역할 |
|---|---|
| Keysight M8196A AWG | TX / RX 외부 클럭 공급 (TX는 on-chip PLL과 선택 가능) |
| 샘플링 오실로스코프 | 반복 파형 관측 — 아이 다이어그램·지터 |
| Real-time 오실로스코프 | 단발 파형 캡처 — 프레임 단위 동작 확인 |
| **Total Phase Cheetah** SPI Host Adapter | PC ↔ 칩 SPI 제어. 설정 시퀀스는 MATLAB으로 작성 |

칩은 PCB에 wire-bonding해 특성을 측정했습니다. TX/RX DSP는 0.9 V, DAC/ADC는 0.8 V로 동작하며, RX 입력은 AC 결합에 common mode 250 mV입니다.

## 실측 결과

| 조건 | Sampling rate | Data rate | Aggregate BER |
|---|---:|---:|---:|
| Loopback | 20 GS/s | 51 Gb/s | 5.2 × 10⁻³ |
| Notch channel | 16 GS/s | 27 Gb/s | 3.9 × 10⁻³ |

![Loopback constellation and channel](figures/loopback-and-channel.png)

Loopback의 constellation과 notch 보드·채널 응답입니다. 1.8 GHz와 5.5 GHz에 notch가 나타납니다.

![Notch SNR loading constellation](figures/notch-results.png)

Notch 채널의 SNR, 로딩 분포와 constellation을 함께 제시합니다. 원문은 일부 constellation BER을 EVM으로 추정했다고 기술하므로 aggregate BER과 구분합니다.

![Power](figures/power.png)

최종 원고는 loopback 기준 551 mW, 10.8 pJ/b를 보고합니다.

## 근거

ISSCC2027_DMT.docx 및 ISSCC2027_Figures.docx 본문·Fig. 1–7. 실측 사진: 2509_DMT_TRX_Measurement_V1.pptx, slide 2.

[RFSoC 단계별 검증](../rfsoc/README.md) · [← 석사 연구](../README.md)

