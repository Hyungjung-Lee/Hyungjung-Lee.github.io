---
title: "Deep Learning"
date: 2021-12-25 21:53:00 +0900
excerpt: "deeplearning keras"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: deeplearning
tags: deeplearning keras
---

Keras 1장

=============

# Tenser의 종류
** [note] ()는 Python Tuple을 뜻한다.

- 벡터 데이터 : (sample, features) 크기의 2D 텐서
- 시계열 데이터 또는 시퀀스 데이터 : (samples, timesteps, features) 크기의 3D 텐서
- 이미지 : (samples, height, width, channels) 또는 (samples, channels, height, width) 크기의 4D 텐서
- 동영상 : (samples, fraems, height, width, channels) 또는 (samples, frames, channels, height, width) 크기의 5D텐서


# Tenser의 연산

## 원소별 연산
- relu : max(x, 0)
- 덧셈

## 브로드캐스팅

모호하지 않고 실행 가능한 경우 작은 텐서가 큰 텐서의 크기에 맞추어 브로드캐스팅하는 것

1. 큰 텐서의 ndim(numpy 의 차원수)에 맞도록 작은 텐서에 브로드캐스팅 축이 생성
2. 작은 텐서가 새 축을 따라서 큰 텐서의 크기에 맞도록 반복


## Tenser 점곱(내적, Tenser 곱셈)

행렬의 내적이다. 차원수가 다른 텐서를 점곱 했을 경우 차원이 아래와 같이 조정된다.

(a, b, c, d) · (d,) -> (a, b, c)

(a, b, c, d) · (d, e) -> (a, b, c, e)

# Tenser 크기 변환

numpy arry의 reshape 함수를 이용한다.
```
np.reshape((60000, 28 * 28))
```
전치(Transposition)의 경우 transpose 함수를 이용한다.
```
np.transpose(x)
```

# Gradient 기반 최적화

변화율(derivative, 도함수)을 이용해서 가중치의 최적화를 한다.

그래디언트는 텐서 연산의 변화율이다. 텐서를 입력으로 받는 함수에 변화율 개념을 확장시켰다.
입력 데이터 x와 y가 고정되어 있다 가정하고 행렬 W(가중치)를 손실 값에 매핑하는 것으로 볼 수 있다.

## 확률적 경사 하강법

가장 작은 손실 함수의 값을 만드는 가중치의 조합을 해석적으로 찾는 것을 의미한다. 

이는 gradient(f)(W) = 0을 찾는 것이다.

이는 N개의 변수로 이루어진 다항식이고 N은 네트워크의 가중치 개수이다.
