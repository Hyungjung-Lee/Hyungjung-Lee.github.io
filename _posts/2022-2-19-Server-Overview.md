---
title: "Server Env Overview"
date: 2022-2-13 12:15:00 +0900
excerpt: "python selenium"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: server
tags: server study
---
# Server 환경 Overview

그동안 다양한 서비스, 프레임워크를 공부하고 직접 체험해 보았습니다. 대부분의 서비스를 로컬에서 구동하였는데, 실제로 서비스를 운영하기 위해서는 로컬 환경에서는 한계가 있습니다. 여러가지 서비스를 띄우면 컴퓨터가 느려지고, 여러가지 작업을 로컬 컴퓨터에서 작업해야하는데 다른 서비스들이 동작하고 있으면 개발하기도 힘들기 때문입니다. 또한 외부에서 개인 로컬 컴퓨터에 접근하게 하는 것도 위험합니다.

그렇기 때문에 실제 서비스를 위해 지금까지 공부한 서비스들을 안정적으로 동작하게 하게 하려면 외부 컴퓨터에 자신의 서비스를 설치하고 공개하여야 합니다.

이러한 역할을 하는 것을 서버라고 합니다.

# Server

서버와 유저, 개발자의 관계를 가장 간단하게 표현하면 아래와 같습니다.

![git](/assets/images/server_1.png)

개발자는 자신의 컴퓨터에서 개발을 하고 개발 내용을 서버로 배포합니다. 

배포된 서비스는 서버에서 동작하고 유저는 그 서버로 접근하여 서비스를 사용합니다.

그렇다면 서버는 무엇일까요. 여러분들이 생각하고 있는 컴퓨터와 크게 다르지 않습니다. 

그렇기 때문에 여러가지 서비스를 배포하거나, 여러 사용자가 접근하면 속도가 느려지는 것은 동일합니다.

![git](/assets/images/server_2.png)

이럴때는 보통 서버를 여러개로 만들어서 운영하는 서비스를 안정화 합니다. 

이 과정에서 서버가 많이 늘어나기때문에 여러가지 이슈가 생기게 됩니다.

우선 각각의 서버는 컴퓨터이기 때문에 비용이 발생할 수 밖에 없습니다.

그리고 서버가 늘어날 수록 여러대의 서버를 동시에 제어하는 것도 어렵게 됩니다.

서버 개발자들은 이런 과정에서 생기는 이슈들을 효율적으로 해결하기위해서 여러가지 방법을 사용하고 있습니다.

그 중 대표적인 몇가지를 소개하겠습니다.

## VM

서버는 하나의 컴퓨터라 하였습니다. 아래 이미지와 같이 서버 하나에 서비스 하나만 배포되어있다고 생각해보면 엄청난 낭비입니다. 하지만 아까 말한 것처럼 여러개의 서비스가 동작하고 있으면 서로 간섭이 일어날 수 있습니다.

![git](/assets/images/server_3.png)

그렇기 때문에 나온 개념이 `가상머신 (Virtual Machine)` 입니다. 

서버 한 대에 여러개의 가상 OS 를 올리고 그 위에 단일 서비스를 올려서 간섭을 최소화 하고 서버를 빈틈 없이 사용할 수 있게 하는 방법입니다.

![git](/assets/images/server_4.png)

위 그림처럼 서버에 기본 OS를 설치하고 그 위에 Hypervisor 라는 서비스를 설치합니다. 그 위에 가상의 OS 를 설치하고 그 위로 어플리케이션을 설치합니다. 어플리케이션 들은 독립적인 OS 에 설치되어있으므로 서로 간섭되지 않습니다. 이후 Hypervisor는 각각의 OS들이 정상적으로 동작할 수 있도록 중재자 역할을 합니다.

## Container

하지만 이런 VM도 한계가 있었습니다. 위에 그림처럼 여러개의 OS 가 설치되는 것이 서버 자원을 너무 많이 소모하는 문제가 있었습니다. 

![git](/assets/images/server_5.png)

그래서 나온 것이 위 그림과 같은 Container 방식입니다. Docker 라는 Engine을 이용하여 서버에 기본적으로 설치되어있는 OS를 사용합니다. 그리고 Docker Engine은 OS와 통신하기 위한 경량화 된 `Kernel` 을 이용하여 각각의 어플리케이션을 독립된 환경에서 동작하게 합니다. 

이런 Container 방식이 최근에 가장 많이 사용되는 방식입니다.

하지만 만약 어플리케이션 한개가 매우 중요하고 성능을 많이 차지하는 경우는 Container, VM을 사용하지 않고 가장 첫번째 방식인 서버 하나에 어플리케이션 한개를 사용합니다. 보통 DB를 이런 방식으로 사용하는데요. 이처럼 용도에 따라서 위에 소개 된 방법 중 하나를 선택해서 서버를 구성하게 됩니다. 

# Network

앞서서 각각의 서버가 어떤형태로 되어있는지 대략적으로 알아봤습니다. 하지만 결국 서버는 외부에서 접근할 수 있어야 배포를 하든 외부 유저가 접근하든 할 수 있습니다. 서버를 의미있게 하는 서버와 유저, 서버와 서버 간의 연결을 Network라고 합니다. 이번에는 Network를 구성하는 몇 가지 개념을 알아보겠습니다.

## Port

본래의 의미로 직역하면 '항구'라는 뜻으로, 컴퓨터 관련 분야에서의 의미로는 운영 체제 통신에서의 종단점을 뜻합니다. 즉 서버에서 데이터가 왔다갔다하는 입구라고 할 수 있습니다. 어플리케이션이 외부로 데이터를 내보내거나 받으려면 반드시 포트를 열어야합니다. 이러한 포트는 숫자로 표현되고 예를들면 80, 443, 8080, 5000 등의 다양한 숫자가 사용됩니다. 

하드웨어적 의미 : 컴퓨터의 주변 장치와 연결하기 위한 연결단 (물리적 통로)
소프트웨어적 의미 : 네트워크 서비스나 특정 프로세스를 식별하는 논리적 단위 (논리적 통로)

## IP

많이 들어보셨겠지만 IP에 대해서도 간단하게 확인하고 넘어가겠습니다. IP(Internet Protocol) 란 인터넷에 연결되어 있는 모든 장치들(컴퓨터, 서버 장비, 스마트폰 등)을 식별하는 고유값이라고 생각하면 됩니다.

아이피를 표현하는 방식은 `IPv4` `IPv6` 가 있는데 각각 `255.255.255.255`, `2001:0DB8:1000:0000:0000:0000:1111:2222` 와 같은 형태입니다.

이때 중요한 것은 IP는 망에따라 다릅니다. 외부에서 접근가능한 외부망에 오픈되어있는 IP와 사내에서 처럼 내부에서 사용되는 내부망에서 사용되는 IP가 다르다는 것입니다. 망에 따라 IP가 달라지는 것을 잘 알고 넘어갑시다. 

## DNS

도메인 이름 시스템(DNS)이라고 하며 사람이 읽을 수 있는 도메인 이름(예: www.naver.com)을 머신이 읽을 수 있는 IP 주소(예: 192.0.2.44)로 변환하는 역할을 합니다.

공인된 도메인 서버에 도메인과 IP를 연결하면 www.naver.com 처럼 웹 브라우저로 부터 우리가 개발할 서버에 접근 할 수 있게 됩니다.

## LoadBalance

![git](/assets/images/server_2.png)

아까 보았던 그림과 같이 여러 서버가 동작하는 경우 접근하는 유저들을 어느 서버에 접근하게 할지 결정이 필요합니다. 이런 역할을 `LoadBalance` 라 하고 이것을 하는 것을 `LoadBalancer` 라고 합니다. 

균일하게 유저를 접근하게 할지, 특정 주소일때는 특정 서버에 접근하게 할지 등을 결정하는 네트워크 서비스에서 중요한 역할을 합니다.

## Cluster

서버 클러스터란 각기 다른 서버(Server or DB)들을 하나로 묶어서 하나의 시스템같이 동작하게 함으로써, 클라이언트들에게 고가용성의 서비스를 제공하는것을 말합니다. 

클러스터로 묶인 한시스템에 장애가 발생하면, 정보의 제공 포인트는 클러스터로 묶인 다른 정상적인 서버로 이동하게 할 수있습니다. 서버 클러스터는 사용자로 하여금 서버 기반 정보를 지속적이고, 끊기지않게 제공받을수 있게 합니다.

요즘과 같이 대용량 트래픽이 당연시 되는 환경에서는 서버들을 클러스터로 구성하여 장애가 발생했을 때도 문제가 없게 처리하고 있습니다.

# Develpment & Deployment

이제 이러한 서버에 어떤식으로 개발자가 접근하는지 알아보겠습니다.

## ssh
SSH란 Secure Shell Protocol 로 22 포트를 통해 서버에 접근하는 것 입니다. Terminal, Command Line 을 이용하여 접근합니다.

# Management

서버 환경이 고도화 되면서 이러한 서버들을 어떻게 생산성 있게 관리할 수 있는지에 대해서 최근 많이 연구되고 있습니다. 가장 보편적이고 많이 사용되는 방식에 대해서 소개하겠습니다.

# Docker

https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html

# Kubernetes

https://kubernetes.io/ko/docs/concepts/overview/what-is-kubernetes/

https://kubernetes.io/ko/docs/concepts/overview/components/

## Google Cloud Platform (GKE)

https://cloud.google.com/kubernetes-engine

# reference

https://allpartner.tistory.com/11