---
title: "Speech-Feature-Extraction"
date: 2024-1-13 12:15:00 +0900
excerpt: "Feature Extraction"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: ai/ml
tags: ai/ml study
use_math: true
classes: wide
---
# Feature Extraction
***

## Overview

[ratsgo's speech book](https://ratsgo.github.io/speechbook/docs/) 를 보고 재작성

## Fourier Transform

푸리에 변환(Fourier Transform)은 시간과 주파수 도메인 관련 함수를 이어주는 수학적인 연산

![git](/assets/images/Fourier-Transform.png)

### Discrete Fourier Transform

시간(time)에 대한 함수(혹은 신호)와 주파수(frequency)에 대한 함수(혹은 신호)를 잇는다.

- 이산 푸리에 변환(DFT)은 시간 도메인의 이산 신호를 주파수 도메인의 이산 신호로 변환하는 과정.
- 컴퓨터는 이산 데이터를 처리하는 데 적합하기 때문에 음성 인식과 같은 디지털 신호 처리 분야에서 DFT가 널리 사용 됨. 
- 이산 푸리에 변환의 결과로 얻어진 주파수 도메인 신호를 다시 시간 도메인으로 변환하는 것을 이산 역푸리에 변환(IDFT)이라고 함.
- DFT와 IDFT는 수학적으로 비슷하지만, 지수 함수 내의 부호 차이로 구분. DFT는 음수의 지수를 사용하고, IDFT는 양수의 지수를 사용.

#### DISCRETE FOURIER TRANSFORM

$$
X_k = \sum_{n=0}^{N-1} x_n \cdot e^{-i \frac{2\pi k n}{N}}
$$

#### INVERSE DISCRETE FOURIER TRANSFORM

$$
X_k = \sum_{n=0}^{N-1} x_n \cdot e^{i \frac{2\pi k n}{N}}
$$


### Concepts

오일러 공식(Euler's formula)

$$
e^{i\theta} = \cos\theta + i\sin\theta
$$

허수 단위 `i`는 제곱하면 `-1`이 되며, `θ`에 `π`를 대입하면 오일러의 등식 `exp(iπ) + 1 = 0`을 얻는다.

복소 평면에서 `exp(iθ)`는 반지름이 1인 단위원을 그리며, 실수부는 `cosθ`, 허수부는 `isinθ`로 표현.

![git](/assets/images/EULER.png)

$$
X_k = \sum_{n=0}^{N-1} x_n \cdot e^{-i \frac{2\pi k n}{N}} = \sum_{n=0}^{N-1} x_n \cdot \left[ \cos\left(\frac{2\pi k n}{N}\right) - i \cdot \sin\left(\frac{2\pi k n}{N}\right) \right]
$$

xn 은 시간 도메인의 n번째 샘플, Xk는 주파수 도메인의  k번째 푸리에 변환 결과물
k/N 은 주파수 

식을 봤을 때 전체 복소평면상 이들 점들을 모두 더하는 것이므로 시간 도메인의 원래 신호의 무게 중심이라고 볼 수 있다.

![git](/assets/images/fourier-transform_2.png)

즉 변환을 하게 되면 k/N 을 x축 Xk y로 하는 주파수에 대한 값으로 변환이 된다.

### DFT Matrix

matrix form 으로 나타냄. 
- 벡터 x 는 시간 도메인의 원래 신호, 
- 행렬 W는 exp 항 
- W 는 N×N 크기로 행 인덱스는 k 열 인덱스는 n
- W 의 각 행은 복소평면상 단위원에서 도는 각기 다른 각 속도

$$
\vec{X} W_{kn} = W \cdot \vec{x} = e^{-i \frac{2\pi k n}{N}}
$$

이산 푸리에 변환의 본질은 선형 변환(linear transform)

![git](/assets/images/fourier-transform-matrix.png)

DFT 행렬은 켤레 전치가 역행렬(inverse matrix)와 같은 복소수 행렬, 즉 유니타리 행렬(unitary matrix).

아래는 Python Code 예시

```python
import numpy as np
def DFT(x):
    x = np.asarray(x, dtype=float)
    N = x.shape[0]
    n = np.arange(N)
    k = n.reshape((N, 1))
    W = np.exp(-2j * np.pi * k * n / N)
    return np.dot(W, x)
```

## Reference

[ratsgo's speech book](https://ratsgo.github.io/speechbook/docs/).


