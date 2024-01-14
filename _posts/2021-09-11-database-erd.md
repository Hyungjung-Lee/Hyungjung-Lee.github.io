---
title: "Database ERD"
date: 2021-09-11 21:53:00 +0900
excerpt: "Database ERD"
header:
  overlay_image: /assets/images/wallpaper_2.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: study database khk kdk
tags: khk kdk database
---
Database ERD
=============

## ERD(Entity Relationship Diagram)?


![classic erd](/assets/images/classic_erd_model.png)

개체 속성과 개체 간 관계를 도표로 표현한 것을 의미합니다.

![erd](/assets/images/erd.gif)

## ERD Releation Expression

### 1:1 (Identifier)

![1_1](/assets/images/1_1_erd.png)

person의 id와 detail_address 의 관계가 1:1로 되어있습니다.

detail_address 테이블은 person_id를 기본키로 사용하고 있고, person_id를 통해 person 테이블을 참조 하고 있습니다. 

즉 한명의 사람(person)은 한개의 주소(address)를 갖고 있을 수 있습니다.

이때 상세 주소는 주민 id가 없다면 존재 할 수 없습니다. (식별 관계)

기존 테이블에 Colum을 늘리지 않고 데이터 셋을 확장하고 싶을때 1:1 관계를 사용합니다.

### 1:N (Non-null)

![1_N](/assets/images/1_N_erd.png)

외래키로 지정된 dept_id 값이 반드시 존재해야합니다.

### 1:N(Nullable)

![1_N](/assets/images/1_N_nullable_erd.png)

외래키로 지정된 dept_id 값이 null이어도 됩니다.

# 비 식별관계와 식별관계

### 비식별 관계

![non-identifier](/assets/images/erd_non_identifier.png)

비식별관계는 점선으로 표현합니다.

기본키에 외래키가 포함되어 있지 않다면 비식별 관계라고 합니다.

### 식별 관계

![identifier](/assets/images/erd_identifier.png)

기본키에 외래키가 포함되어있다면 이를 식별 관계라고 합니다. 