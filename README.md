# Gayoung Kang — Circuit and System Design

고속 유선 통신 DSP의 구조, 하드웨어 검증과 측정 결과를 정리한 포트폴리오입니다.

> 비공개 검토본. 공동 연구 결과와 개인 기여를 구분하고 있으며, 개인 담당 범위와 일부 측정 조건은 확인 중입니다.

## 석사 연구

### KETI DMT Transceiver

14nm FinFET DMT TRX의 IP 구조, chip layout, PCB 및 측정 결과입니다. 제공된 원고는 loopback 51 Gb/s와 notch 채널 27 Gb/s의 시스템 성능을 보고합니다.

![Chip layout and die](masters/keti/figures/layout-die.png)

[KETI 프로젝트 보기](masters/keti/README.md)

### On-chip Adaptive Bit/Power Loading

호스트 PC가 하던 bit/power loading을 칩 안으로 옮긴 졸업연구입니다. RX가 파일럿으로 부채널 오차를 측정하고, 같은 다이의 엔진이 나눗셈·로그 없이 정수 연산만으로 비트와 전력을 결정해 TX CONFMEM에 직접 씁니다. 고정소수점 시뮬레이터 → RTL → RFSoC 실시간 검증까지 수행했습니다.

![BPL feedback loop](masters/adaptive-bpl/figures/sys-feedback.png)

[설계와 검증 보기](masters/adaptive-bpl/README.md)

### RFSoC 하드웨어 검증

Single tone → Multi tone → SNR 및 로딩 → 동기화 → FFT/FDE → constellation scanner와 BER 순서로 검증 과정을 정리했습니다.

![32-QAM scanner](engineering/rfsoc/figures/scanner-32qam-low-ber.png)

[RFSoC 검증 보기](engineering/rfsoc/README.md)

## 추가 구성 예정

- 석사: 삼성미래기술·한국연구재단 석사과정생연구장려금, Mixed-signal·Full-custom 프로젝트
- 학부: Ring VCO, Stochastic Computing 학부연구생 연구, CPU EX단 최적화, 회로설계 프로젝트 및 연구 인턴

자료 검토를 마친 페이지부터 순차적으로 추가합니다.
