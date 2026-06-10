---
title: "[Data Compression] Scalar Quantization"
date: 2026-06-05 00:00:00 +0900
categories: [Data Compression]
tags: [Data Compression]
---

## 양자화의 기초 개념
- **정의:** 매우 크거나 무한한 값의 집합을 더 적은 수의 유한한 집합으로 표현하는 과정이다.
- **특징:** 원래의 소스값이 영구적으로 손실되므로, 양자화가된 모든 압축은 Lossy Compression이 된다.
- **구조:** 입력구간을 나누는 Decision Boundaries($b_i$)와 각 구간을 대표하는 Reconstruction Levels($y_i$)로 구성된다.

## Quantization Problem
양자화기 설계의 목표는 데이터의 확률 밀도 함수 (pdf)가 주어졌을 떄 오차와 비트율 사이의 최적점을 찾는 것이다.

### 1. 성능 측정 지표
- **Distortion:** 평균 제곱 양자화 오차(MSQE)를 사용한다

$$
σ_q^2 = E[(X-Q(X))^2] = \sum_{i=1}^M ∫^{b_j}_{b_{j-1}}(x-y_i)^2f_X(x)dx
$$

= **Bit Rate:** 양자화 출력을 표현하는 데 필요한 평균 비트수이다.

### 2. 설계 방향
- **Fixed Length Coding(FLC):** 출력 레벨 수가 고정된 상태에서 왜곡을 최소화하는 $b_i$와 $y_i$를 찾는다. 

$$
R=[log_2M]
$$

- **Variable length coding(VLC):** 왜곡 제약 조건($\sigma^2_q≤D^*$)하에서 Bit Rate를 최소화하는 $b_i$, $y_i$ 및 이진코드를 찾는다.

## Uniform Quantizer
모든 양자화 구간의 크기($Δ$, step size)가 일정한 방식이다.

### 유형
- **Midrise:** 0이 출력 레벨에 포함되지 않음
- **Midtread:** 0이 출력 레벨에 포함되어 무음 구간 표현에 적합함

### 수학적 성능
입력소스가 $[-X_{MAX}, X_{MAX}]$에서 균든 분포(Uniform Distribution)일때
- **단계 크기:** $Δ = \frac{2X_{MAX}}{M}$
- **MSQE:** $\sigma^2_q = \frac{Δ^2}{12}$
- **SNR 법칙:** 신호 대비 잡음비(SNR)는 비트수 $n$에 대해 약 $6.02n$ dB가 된다. 즉, 1비트 추가기 SNR이 $6$dB 개선된다. 



## 양자화의 부작용과 해결책
- **Contouring:** 양자화 레벨이 부족할 때 부드러운 명암 변화가 급격한 계단 현상으로 보이는 현상
- **Dithering:** Contouring을 완화하기 위해 의도적으로 잡음을 섞어 경계를 부드럽게 만드는 기술이다.

## Non-uniform Quantization
입력 데이터가 특정 값에 몰려 있는 경우, 구간의 크기를 다르게 설정하여 효율을 높인다.

### 1. Lloyd-Max 알고리즘(PDF 최적화)
MSQE를 최소화하기 위해 다음 두 조건을 반복적으로 만족시킨다.
- **재구성 조건:** $y_j$는 해당 구간 $[b_{j-1}, b_j]$의 무제 중심이어야 한다.

$$
y_j = \frac{∫^{b_j}_{b_{j-1}}xf_X(x)dx}{∫^{b_j}_{b_{j-1}}f_X(x)dx}
$$
- **경계 조건:**  $b_j$는 인접한 두 재구성 레벨의 중간값이어야 한다.

$$
b_j = \frac{y_j+y_{j+1}}{2}
$$

### 2. Companded Quantization
비선형 함수를 이용해 uniform quantizer로 비uniform 효과를 낸다.
- **Compressor:** 확률이 높은 원점 근처 신호를 확장하고 외곽 신호를 압축한다.
- **Expander:** 양자화 후 역함수를 적용해 원래 신호 스케일로 복원한다.
- **표준:** 전화 통신에서 사용한 $\mu$-law와 A-law가 대표적이다.

## Adaptive Quantization
입력 신호의 통계적 특성이 시간에 따라 변할때 사용한다.
- **Forward Adaptive Quantization(FAQ):** 데이터를 블록 단위로 나누어 분석한 뒤, 취적의 파라미터를 부가정보로 전송한다.

- **Backward Adaptive Quantization(BAQ):** 부가 정보 없이 Quantizer의 출력값만을 이용해 파라미터를 조절한다

- **Jayant Quantizer:** 현재 출력이 외곽 레벨이면 $Δ$를 확장하고, 안쪽 레벨이면 $Δ$를 축소하여 신호를 추적한다.