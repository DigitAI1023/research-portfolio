# 2D FSM 기반 Stochastic Computing

**학부연구생 · IEEE TENCON 포스터 발표 (제2저자)**

*Practical 2D FSM for Stochastic Computing with Improved Hardware Efficiency and Accuracy*
Jiho Kim, **Gayoung Kang**, Youngmin Kim — Hongik University

---

## 문제

Stochastic Computing은 값을 비트스트림에서 '1'이 나올 확률로 표현합니다. 길이 N인 비트스트림에서 '1'이 N₁개면 값은 `P = N₁/N`입니다. 이렇게 두면 **곱셈이 AND 게이트 하나**로 끝나 하드웨어가 극단적으로 단순해집니다. 대신 정확도가 떨어져 실제로 쓰기 어렵다는 문제가 있습니다.

기존 1차원 FSM은 **상태 수에 따라 구현 가능한 함수와 입력 범위가 묶여** 있습니다. 8-state FSM으로는 sigmoid(x)를 직접 구현하지 못하고 sigmoid(8x) 형태로 입력 범위를 접어야 했고, 그러려면 추가 비트 연산이 붙어 오차가 더 커집니다.

---

## 제안 구조 — 4×4 2D FSM

![Proposed 4x4 2D FSM architecture](figures/sc-2dfsm-arch.png)

입력 X와 별도의 확률 입력 K를 각각 SNG로 변환해 **두 독립 변수로 상태를 전이**시킵니다. 4×4 = 16개 상태가 MUX의 선택 신호가 되고, 상태마다 대응하는 가중치 비트스트림 `W₀ ~ W₁₅`를 골라 출력합니다. 마지막에 counter로 '1'의 개수를 세어 값을 복원합니다.

두 입력이 독립이므로 비트스트림 간 상관이 줄고, 가중치를 상태별로 따로 줄 수 있어 **구현 가능한 함수의 자유도**가 올라갑니다.

![4x4 state transition](figures/sc-2dfsm-states.png)

X와 K의 조합 4가지(`00`, `01`, `10`, `11`)에 따라 가로·세로로 상태가 전이합니다. **M = N = 4로 균형을 맞춘 것**이 정확도에 유리했습니다.

---

## 결과

![sigmoid accuracy](figures/sc-sigmoid-accuracy.png)

sigmoid(4x) 구현 결과입니다. 1차원 FSM(검정)은 입력이 −0.5 이하에서 0, 0.5 이상에서 1로 붙어 곡선을 따라가지 못합니다. 제안한 2D FSM(파랑)은 실제 곡선(빨강)에 근접합니다.

**Verilog HDL로 구현하고 Synopsys Design Compiler로 전력·면적을 측정했습니다.**

| 구성 | Power (mW) | Area (µm²) | Error (MSE) | FoM (P×A×E) |
|---|---:|---:|---:|---:|
| 2×4 (8-state) | 5.28 | 64,001 | 1.47 × 10⁻³ | 498.04 |
| **4×4 (16-state, 제안)** | 9.02 | 108,818 | **0.37 × 10⁻³** | **362.69** |
| 8×4 (32-state) | 16.63 | 198,834 | 0.54 × 10⁻³ | 1,773.50 |

FoM은 전력 × 면적 × 오차로, 낮을수록 좋습니다. 4×4 구성이 2×4 대비 **1.37배**, 8×4 대비 **4.89배** 우수합니다.

**상태를 무작정 늘리면 손해입니다.** 8×4는 4×4보다 면적과 전력이 약 2배인데 오차는 오히려 나빠졌습니다(0.37e-3 → 0.54e-3). 가로·세로 균형이 깨지면서 상태 전이가 한쪽으로 쏠린 결과로 보입니다.

4×4에서 전체 값의 **96.5 %가 오차 0.05 이내**에 들어옵니다.

---

[← 학부 과정](../README.md) · [자료 출처](figure-sources.json)
