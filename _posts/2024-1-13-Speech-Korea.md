---
title: "Speech-Korea"
date: 2024-1-13 12:15:00 +0900
excerpt: "Korean Phonology"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: ai/ml
tags: ai/ml study
use_math: true
classes: wide
---
# Korean Phonology
***

## Overview

[ratsgo's speech book](https://ratsgo.github.io/speechbook/docs/) 를 보고 재작성


## 말소리의 생성 과정

인간이 말을 할 때는 폐에서 내보낸 공기가 기류로 작용하여 목을 통과하고, 입이나 코를 지나면서 다양한 말소리로 변환. 
인간의 조음 기관은 수천 가지 소리를 만들 수 있지만, 실제 언어에서는 그 중 일부만을 사용하여 의사소통.

### 발성 기관

폐에서 나온 기류가 후두의 성대를 통과하며 성문의 조절로 유성음과 무성음을 생성하여 말소리를 만드는 기관.

### 공명강

후두를 통과한 기류는 인두, 구강, 비강, 순강을 포함한 공명강을 거치며 음량과 음색의 변화를 일으키며 다양한 말소리를 생성. 
이때 혀의 위치, 입술의 모양, 그리고 코의 통로 여부가 구강과 인두강의 크기 및 모양과 비강의 역할에 영향을 미침.

## 음소와 음운

### 음소
각기 다른 발음들을 동일한 말소리로 인식하며, 말소리의 최소 단위를 '음소(Phoneme)'라 함. 
예를 들어, 한국어에서 '라면'과 '달'의 'ㄹ' 발음은 다르지만 둘 다 같은 음소로 인식 됨.

### 최소대립쌍
음소를 확인하기 위한 방법 중 하나는 '최소대립쌍'. 
예를 들어, '불이 났다', '뿔이 났다', '풀이 났다'는 각각 다른 의미를 가짐. 'ㅂ', 'ㅃ', 'ㅍ'의 차이로 의미가 구별, 이런 음성들은 각각 음소의 자격을 갖게 됨.

### 변이음과 음운 자질
음소는 변할 수 있다. 이를 '변이'라고 함.
'감기'의 첫번째 'ㄱ'이 [k]로, 두번째 'ㄱ'이 [g]로 발음 됨.

음소 구별에 유용한 특징을 '변별적 자질'이라 함. 예를 들어, 모음의 경우 고모음, 저모음, 중모음 등으로 구별되며, 자음은 공명성, 지속성, 소음성 등의 특징을 가짐.

### 음절
한글은 음소 문자로, 각 글자가 하나의 소리를 나타냄. 
예를 들어, 'ㄱ'은 음소 /k/와 대응되고, 'ㅋ'은 /kh/에 대응 됨. 한글은 음절 단위로 모아서 쓰도록 고안 됨, 한국어에서는 모음만으로도 음절을 이룰 수 있다.

![git](/assets/images/syllable.gif)

### 운소
'운소(韻素)'는 음소 이외의 운율적 특징, 소리의 높낮이, 길이, 세기 등을 포함합니다. 예를 들어, '눈[雪]'과 '눈[目]'은 음소만으로는 구별할 수 없고, 소리의 길이(음장)로 구별 됨.

현대 한국어에서는 음장의 구별이 점차 사라지고 있지만, 이러한 개념은 여전히 언어의 이해에 중요한 기초를 제공.

## 형태소와 기저형
형태소(morphoeme)는 의미를 가진 언어의 최소 단위. 발화된 말소리를 분석해 형태소가 머릿속에 어떤 모습으로 저장되어 있을 것이라고 추측. 
이 추정된 음운론적 정보를 **기저형(Underlying Form)** 이라고 부름.

기저형은 실제 발화되는 형태가 아닌, 머릿속에 추정되는 형태. 실제 발화에서 기저형이 실현된 형태를 **표면형(Surface Form)** 이라고 하며, 

표면형의 예, 형태소 /집/은 '집도', '집이'로 발화될 때 [집]으로 실현되지만, '집만'으로 발화될 때는 [짐]으로 실현.

### 말소리의 변화
말소리는 시간이 흐르며 변화하거나, 다른 말과 연결되면서 변동. 네 가지 유형으로 나뉨.

1. **대치(代置)**: 한 소리가 다른 소리로 바뀌는 것.
2. **탈락(脫落)**: 원래 있던 소리가 사라지는 것.
3. **첨가(添加)**: 없던 소리가 끼어드는 것.
4. **축약(縮約)**: 둘 이상의 소리가 합쳐져 새로운 소리가 되는 것.

### 대치의 예

- **평폐쇄음화**: 종성에서 평폐쇄음이 아닌 소리가 평폐쇄음으로 대치. 예: 앞 → 앞, 옷 → 옫
- **경음화**: 평음이 경음으로 대치. 예: 국밥 → 국빱
- **치조비음화**: ㄹ이 ㄴ으로 대치. 예: 결단력 → 결딴녁
- **유음화**: ㄴ이 ㄹ로 대치. 예: 칼날 → 칼랄
- **비음화**: 장애음이 비음으로 대치. 예: 국물 → 궁물
- **양순음화**: ㄴ, ㄷ이 ㅁ, ㅂ으로 대치. 예: 기분만 → 기붐만
- **연구개음화**: ㄴ, ㅁ이 ㅇ[ŋ]으로, ㄷ, ㅂ이 ㄱ으로 대치. 예: 신고 → 싱꼬
- **구개음화**: 치조음이 경구개음으로 대치. 예: 짐받이 → 짐바지
- **움라우트**: 후설모음이 전설모음으로 대치. 예: 아기 → 애기
- **모음조화**: 어간의 말음절 모음에 따라 어미가 결정. 예: 잡아 → 자바
- **활음화**: 단모음이 활음으로 바뀜

### 탈락의 예
- **자음군 단순화**: 자음군이 단순화되어 하나의 자음으로 발음. 예: 앉다 → 안따, 많네 → 만네
- **ㅎ 탈락**: ㅎ이 탈락. 예: 놓아 → 노아, 많아 → 마나
- **어간말 '으' 탈락**: '으'로 끝나는 어간에서 '으'가 탈락. 예: 쓰어 → 써
- **동모음 탈락**: 어간의 말음 '아/어'가 어미 '아/어' 앞에서 탈락. 예: 가아 → 가
- **활음탈락**: 이중모음의 활음이 탈락. 예: 지어 → 저, 보아 → 바

### 첨가의 예
- **ㄴ 첨가**: 자음과 '이' 또는 'j' 사이에 ㄴ이 첨가. 예: 솜이불 → 솜니불, 색연필 → 색년필
- **활음첨가**: 특정 모음 사이에 활음이 첨가. 예: 피어 → 피여, 보아 → 보와

### 축약의 예
- **ㅎ 축약**: ㅎ과 평음의 연쇄가 격음으로 축약. 예: 놓고 → 노코, 않던 → 안턴

## Reference

[ratsgo's speech book](https://ratsgo.github.io/speechbook/docs/).