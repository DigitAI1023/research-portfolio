# 학부 과정

홍익대학교 전자전기공학부. 아날로그 회로 설계, 디지털 연산기 설계, full-custom layout을 각각 다뤘습니다.

| 프로젝트 | 분야 | 대표 결과 |
|---|---|---|
| [Regulated-Supply 8-Phase Ring VCO](ring-vco/README.md) | 아날로그 · CMOS 90 nm | supply ripple **100 mV → 6.27 mV** |
| [2D FSM 기반 Stochastic Computing](stochastic-computing/README.md) | 디지털 · 학부연구생 | FoM **1.37× 개선**, IEEE TENCON 발표 |
| [LEGv8 ARM CPU EX단 최적화](legv8-cpu/README.md) | 디지털 · 45 nm | EX stage **6.71 → 4.88 ns** |
| [1-bit Full Adder Layout](full-adder-layout/README.md) | Layout · CMOS 90 nm | 6.8 × 4 µm², worst-case delay 분석 |
| [2-Stage Cascode Amplifier](amplifier-design/README.md) | 아날로그 · 이산소자 | 목표 gain 51 → 실측 **51.0** |
| [DGIST 연구 인턴](dgist-internship/README.md) | PCB · full-custom | RF PCB 설계, Ring OSC **PEX 9.35 → 4.75 GHz** |

---

## 흐름

**아날로그** — 전자회로실험에서 이산소자로 2-stage cascode 증폭기를 설계해 gain 51을 맞춘 뒤, IC Design Term Project에서 CMOS 90 nm 공정으로 Ring VCO와 레귤레이터를 설계했습니다. 인턴에서는 TSMC 28 nm full-custom layout까지 진행했습니다.

**디지털** — 집적회로설계에서 1-bit full adder를 schematic부터 layout까지 그린 경험이, IT 시스템 종합설계의 LEGv8 ARM 프로세서 설계와 근사 곱셈기 연구로 이어졌습니다.

**연구** — 학부연구생으로 Stochastic Computing의 2D FSM 구조를 연구해 IEEE TENCON에 논문을 발표했고, CPU EX단 최적화는 2024 반도체공학회 하계학술대회에서 포스터로 발표했습니다.

---

## 논문 · 발표

| 구분 | 제목 | 발표 |
|---|---|---|
| 논문 | *Practical 2D FSM for Stochastic Computing with Improved Hardware Efficiency and Accuracy* | IEEE TENCON, 포스터 발표 (제2저자) |
| 포스터 | *Mitigating Data Hazards in LEGv8 ARM Processor Using Geometric Approximation Speed Unit* | 2024 반도체공학회 하계학술대회 (제2저자) |

---

[← 메인으로](../README.md)
