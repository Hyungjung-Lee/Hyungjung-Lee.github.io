---
title: "Hidden Markov Chain"
date: 2024-1-20 12:15:00 +0900
excerpt: "Hidden Markov Chain"
header:
  teaser: /assets/images/wallpaper_1.png
  overlay_image: /assets/images/wallpaper_2.png
  overlay_filter: 0.1 # same as adding an opacity of 0.5 to a black background
categories: ai-ml
tags: ai-ml study
use_math: true
classes: wide
---
# Hidden Markov Chain

## Intro

[automatic-speech-recognition-asr-introduction](https://web.stanford.edu/class/cs224s/syllabus/#week-4-course-project--automatic-speech-recognition-asr-introduction
) 과 [ratsgo's HMM](https://ratsgo.github.io/machine%20learning/2017/03/18/HMMs/) 을 보고 재작성



## Markov Model

$$
P(X_{n} = j | X_{n-1} = i, X_{n-2} = i_{n-2}, ..., X_0 = i_0) = P(X_{n} = j | X_{n-1} = i)
$$

(X_k)는 시간 (k)에서의 상태.

Output-independence를 가정한다

$$
P(o_t | O_{1}^{t-1}, q_{1}^{t}) = P(o_t | q_t)
$$

현재 상태가 오직 바로 이전의 과거 상태에만 의존하고 더 과거의 상태나 어떻게 현재 상태에 도달했는지에 대한 정보에는 의존하지 않는 모델

과거의 역사보다는 현재 상태가 미래의 상태를 결정하는 데 더 중요하다는 가정을 기반으로 함.

## Markov Chain

`Markov chain = First-order observable Markov Model`

특정 시점 t에서의 State는 바로 이전 시점 t-1에서의 State의 영향을 받는다는 의미

마코브 체인은 마코브 특성을 갖고 이산적인 시간 흐름에 따른 시스템의 상태 변화를 확률적으로 나타낸 이산확률과정(또는 Discrete Markov Process)이다.

FSM(Finite State Machine)처럼 특정 시점 t에 대한 State가 존재하며, 시간의 진행에 따라 특정 State에서 다른 State로 Transition을 한다

$$
Q = \{q_1, q_2, \ldots, q_N\}; \text{ the state at time } t \text{ is } q_t
$$
Q 는 각 시간 별 state 를 의미한다.

아래 식은 스테이트를 변환하는 전이 행렬이 된다.
$$
a_{ij} = P(q_t = j \mid q_{t-1} = i) \quad 1 \leq i, j \leq N \\
\sum_{j=1}^{N} a_{ij} = 1; \quad 1 \leq i \leq N
$$

### Example

![git](/assets/images/hmm/markov_chain_for_weather.png)

![git](/assets/images/hmm/markov_chain_for_word.png)

### Start state

![git](/assets/images/hmm/markov_model_example.png)

$$
[ \pi_i = P(q_1 = i) \quad 1 \leq i \leq N ]

[ \sum_{i=1}^{N} \pi_i = 1 ]
$$

## Problem

기존 마코브 체인에서는 출력 심볼이 상태 심볼과 같다.
예를 들어, 더운 날씨를 보면 우리는 '더운' 상태에 있다고 할 수 있음.
하지만 음성 인식에서는 출력 심볼은 음향 벡터(Cepstrum: 푸리에 변환한 특징), 숨겨진 상태는 'phones'(음소)이다.

그래서 마코브 체인을 그대로 사용하지 못한다. 그래서 입력 심볼과 상태가 같지 않은 마코브 체인의 확장인 
히든 마코브 모델(Hidden Markov Model)을 사용한다.
우리가 어떤 상태에 있는지 알 수 없다는 것을 의미.

## Hidden Markov Chain

관측치 뒤에 은닉되어 있는 상태(state)를 추정하고자 할 때 사용

$$
P(q_i | q_1, \ldots, q_{i-1}) = P(q_i | q_{i-1})
$$

### Hidden Markov Chain Case

아래는 대표적인 HMM이 사용되는 케이스 

- 당신은 2799년의 기후학자입니다. 
- 지구 온난화를 연구하고 있습니다. 
- 2008년 여름, 메릴랜드주 볼티모어의 날씨 기록을 찾을 수 없습니다. 
- 하지만 제이슨 아이스너의 일기를 발견했습니다. 
- 그 일기에는 그 여름, 제이슨이 매일 몇 개의 아이스크림을 먹었는지가 기록되어 있습니다. 
- 우리의 임무: 그 여름 얼마나 더웠는지 알아내는 것입니다.

![git](/assets/images/hmm/hmm_example.png)

$$
\textbf{Q} = \{q_0, q_1, q_2, \ldots, q_n, q_F\} : \text{상태(state)의 집합(set). } q_0 \text{은 시작상태, } q_F \text{는 종료상태, } n \text{은 상태의 개수를 나타낸다.} \\
\textbf{A} : \text{전이확률 행렬(} n \times n \text{). } a_{ij} \text{는 } i \text{번째 상태에서 } j \text{번째 상태로 전이할 확률(} \sum_{j=1}^{n} a_{ij} = 1 \text{).} \\
\textbf{B} = b_i(o_t) : i \text{번째 상태에서 } t \text{번째 관측치 } o_t \text{가 나타날 방출확률.} \\
\textbf{O} = [o_1, o_2, \ldots, o_t, \ldots, o_T] : \text{길이가 } T \text{인 관측치의 시퀀스.} \\
\alpha_t(j) = P(o_1, o_2, \ldots, o_t, q_t = j \mid \lambda) : \text{모델 } \lambda \text{가 주어졌을 때 } j \text{번째 상태와 } o_1, \ldots, o_t \text{가 나타날 확률. 전방확률(Forward Probability)} \\
\beta_t(j) = P(o_{t+1}, o_{t+2}, \ldots, o_T, q_t = j \mid \lambda) : \text{모델 } \lambda \text{가 주어졌을 때 } j \text{번째 상태와 } o_{t+1}, \ldots, o_T \text{가 나타날 확률. 후방확률(Backward Probability)}
$$

## The Three Basic Problems for HMMs 

- 문제 1 (Evaluation): 관측 시퀀스 O=(o1o2…oT)와 HMM 모델 F = (A,B)가 주어졌을 때, 모델을 고려한 관측 시퀀스의 확률 P(O| F)를 어떻게 효율적으로 계산할 수 있을까?

- 문제 2 (Decoding): 관측 시퀀스 O=(o1o2…oT)와 HMM 모델 F = (A,B)가 주어졌을 때, 어떤 의미에서 최적인 상태 시퀀스 Q=(q1q2…qT)를 어떻게 선택할 수 있을까? (즉, 관측값을 가장 잘 설명하는 시퀀스는 무엇인가요?)

- 문제 3 (Learning): 모델 파라미터 F = (A,B)를 어떻게 조정해야 P(O| F)를 최대화할 수 있을까?

## Viterbi intuition

## Reference

스탠퍼드 
