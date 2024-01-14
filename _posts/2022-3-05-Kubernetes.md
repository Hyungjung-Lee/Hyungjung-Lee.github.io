---
title: "Kubernetes"
date: 2022-2-13 12:15:00 +0900
excerpt: "Kubernetes"
header:
  overlay_image: /assets/images/wallpaper.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: server
tags: server study
---
# Kubernetes

![git](/assets/images/k8s.jpg)

Kubernetes, 또는 쿠버네티스, 또는 간단히 "k8s"는 Linux 컨테이너 작업을 자동화하는 오픈소스 플랫폼을 뜻합니다. 이 플랫폼에서는 컨테이너화된 애플리케이션을 배포하고 확장하는 데 수동 프로세스가 필요하지 않습니다. 즉, Linux 컨테이너를 실행하는 호스트 그룹을 함께 클러스터링할 수 있으며 쿠버네티스를 통해 이러한 클러스터를 쉽고 효율적으로 관리할 수 있습니다.

쿠버네티스는 원래 Google 엔지니어들이 개발하고 설계한 플랫폼입니다. Google은 초창기에 Linux 컨테이너 기술에 기여하였으며 Google 제품이 컨테이너에서 어떻게 작동하는지 대중에게 공개하였습니다. 이는 Google의 클라우드 서비스를 구동하는 기술이기도 합니다. Google은 내부 플랫폼인 Borg를 통해 일주일에 20억 개 이상의 컨테이너 배포를 생성하고 있습니다. Borg는 쿠버네티스의 전신이었으며 수년 동안 Borg를 개발하는 과정에서 축적된 경험은 쿠버네티스 기술의 주요 원동력이 되었습니다.

쿠버네티스는 현재 가장 대중적인 컨테이너 관리 도구로 사용되고 있습니다.

# Orchestration

쿠버네티스의 가장 큰 기능은 컨테이너의 오케스트레이션(Orchestration)입니다. 컨테이너 오케스트레이션이 하는 일은 여러 개의 서버에 컨테이너를 배포하고 운영하면서 서비스 디스커버리(Service discovery : 서비스가 실제로 어디 위치하는지 찾아주는 기술)같은 기능을 이용하여 서비스 간 연결을 쉽게 해주는 것입니다. 

서버의 IP를 통해 직접 접근하는 것이 아니라 서버 그룹의 이름을 정해 놓고 클러스터로 설정한 적당한 서버에 배포하는 것입니다. 
그리고 부하가 생기면 추가로 컨테이너를 배포하고 부하가 줄어들면 컨테이너를 삭제합니다. 이런 역할을 하는것을 오케스트레이션이라고 합니다.

쿠버네티스가 대중화 되기 전까지는 이런 오케스트레이션 서비스가 굉장히 많았습니다. 가장 유명했던 것은 Docker Compose, Docker Swarm 이었고, 쿠버네티스가 대중화 되면서 현재 대부분의 오케스트레이션은 쿠버네티스를 이용하여 사용되고 있습니다.

# Docker와의 차이

그렇다면 Docker는 이제 사용하지 않는 것일까요? 쿠버네티스의 컨테이너 엔진은 Docker를 이용하고 있습니다. 컨테이너는 Docker를 사용하고 컨테이너 간의 통신을 쿠버네티스의 구성 요소들로 하는 것입니다. 그럼 쿠버네티스 이전의 컨테이너들의 오케스트레이션을 간단하게 알아보겠습니다. 

## Docker Compose

 Docker compose란, 여러 개의 컨테이너로부터 이루어진 서비스를 구축, 실행하는 순서를 자동으로 하여, 관리를 간단히하는 기능입니다. 

 Docker compose에서는 compose 파일을 준비하여 커맨드를 1회 실행하는 것으로, 그 파일로부터 설정을 읽어들여 모든 컨테이너 서비스를 실행시키는 것이 가능합니다.

 즉 여러개의 컨테이너를 하나의 파일에서 관리하는 기능이라고 볼 수 있습니다. 그 파일의 예시는 아래와 같습니다.

 docker-compose.yml
```
version: "3.7" # 파일 규격 버전
services: # 이 항목 밑에 실행하려는 컨테이너 들을 정의 ( 컴포즈에서 컨테이너 : 서비스 )
  db: # 서비스 명
    platform: linux/x86_64
    image: mysql:latest # 사용할 이미지
    restart: always
    command: --lower_case_table_names=1
    container_name: mysql-test # 컨테이너 이름 설정
    ports:
      - "3306:3306" # 접근 포트 설정 (컨테이너 외부:컨테이너 내부)  <- 컨테이너 내부는 무조건 3306
    environment: # -e 옵션
      - MYSQL_DATABASE=test_db
      - MYSQL_ROOT_PASSWORD=1234  # MYSQL 패스워드 설정 옵션
      - TZ=Asia/Seoul

    command: # 명령어 실행
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
    volumes:
      - /Users/user/Documents/MyWorkspace/mysql/data:/var/lib/mysql # -v 옵션 (다렉토리 마운트 설정)
  web: # 서비스 명
    platform: linux/x86_64
    image: nginx:latest # 사용할 이미지
    ports:
      - "5000:5000
```
위 파일은 mysql과 웹서버 컨테이너를 하나의 서버 위에 띄우는 기능을 합니다. 
`docker-compose up -d` 명령어를 해당 파일이 있는 위치에 실행하면 별다른 작업없이 설정파일에 따라 mysql과 web이 구동됩니다. docker-compose 를 이용하면 두 컨테이너 간 네트워크도 설정이 가능합니다.

그리고 `docker-compose down`을 이용하여 간단하게 컨테이너들을 일괄적으로 내릴 수 있습니다. 설정파일을 git 에서 관리하고 git을 통해 서버에서 실행을 하게 되면 매우 손쉽게 여러 컨테이너를 실행할 수 있습니다.

## Docker Swarm

위 docker compose도 충분한 기능을 하는 것 같습니다만 한계가 있습니다. docker compose는 한개의 서버에서 동작하는 것입니다. 이러한 한계를 해결하기 위해서 등장한 것이 Docker Swarm 입니다. Docker Swarm은 여러개의 서버를 노드(Node)로하여 클러스터(Cluster)로 묶어서 관리할 수 있습니다. 구조적으로 master node, worker node로 분리되고 사용자는 master node에 접근하여 docker 명령어를 실행하면 worker node에 여러가지 컨테이너들이 task로서 배포가 됩니다. master node에서는 이러한 task의 생성, 삭제, scale등을 설정할 수 있습니다. 그리고 각각의 컨테이너들의 통신을 비공개, 공개로 설정할 지에 대한 네트워크 규칙인 Ingress 기능 등을 설정할 수 있습니다.

자세한 설정은 [링크](https://doitnow-man.tistory.com/190) 에서 확인 할 수 있습니다.

# Kubernetes 를 사용하는 이유

여러대의 컨테이너를 관리하는 docker compose, 그것을 여러 서버를 통해 관리할 수 있게 도와주는 docker swarm. 이 기능들을 이용하면 여러대의 충분히 여러 컨테이너들을 관리 할 수 있을 것 같습니다. 그럼 쿠버네티스를 쓰는 이유가 무엇일까요? 

쿠버네티스는 도커 스웜보다 조금 더 고차원 적인 개념과 설정으로 구성되어 있습니다. 서비스에대한 설정, 배포에대한 설정, 네트워크에 대한 설정 등 docker swarm 보다 미세한 조정이 가능합니다. 운영을 위한 추가적인 개념들도 존재합니다. 그렇기 때문에 쿠버네티스는 조금 더 대규모의 클러스터 환경에서 사용합니다. 설정하는 것이 많아서 docker swarm 보다 복잡하고 어렵지만, 최근에는 쿠버네티스를 서포트하는  여러 클라우드 서비스들 (AWS, Google) 등에서 간단하게 사용할 수 있도록 지원하고 있습니다. 그래서 최근에는 스웜보다 쿠버네티스를 더 보편적으로 사용하고 있습니다. 스웜과 쿠버네티스를 비교한 [블로그](https://ingeec.tistory.com/83)를 참고하면 이해에 도움이 됩니다.

# Kubernetes 구성

그렇다면 이제 실제로 쿠버네티스가 어떤 구성으로 되어있는지 용어를 통해서 간단하게 알아보도록 하겠습니다.

- Node: 쿠버네티스가 설치되어 있는 실제 서버입니다. 쿠버네티스는 이 서버에 여러가지 서비스들을 배포하여 구동됩니다.

- Pod: 우리들이 작성한 어플리케이션이 배포되는 컨테이너를 파드라고합니다.  

- Deployment: 파드들이 어떠한 규칙으로 배포될지에 대한 정보를 담고 있습니다. 몇 개의 파드를 배포할지 노드의 얼마만큼의 자원을 사용할지 등의 정보를 담고 있습니다.

- ConfigMap: 파드들이 어떠한 정보를 포함하여 실행할지에 대해 작성되어있습니다. 사용자는 파드와 컨피그 맵을 분리하여 동일한 어플리케이션이지만 컨피그에 따라 다른 동작을 하도록하는 어플리케이션을 구동할 수 있습니다.

- Service: 동일한 역할을 하는 파드들의 집합을 서비스라고 합니다. 서비스는 고유한 엔드포인트 (dns)를 갖고 있습니다.

- Ingress: 서비스의 네트워크 규칙을 담고 있습니다. 외부로 노출될지 내부에서 통신할지 어떠한 포트를 사용하여 노출할지에 대한 정보를 담습니다.

- kubectl: 사용자가 쿠버네티스를 사용할 수 있도록하는 명령어 집합입니다. 우리는 kubectl의 명령어를 이용하여 쿠버네티스의 위 항목들을 조작할 수 있습니다.

- kustomize: 쿠스터마이즈라고하며 위 항목들을 Yaml 파일을 통해 관리할 수 있도록 합니다. 이를 통해 우리는 설정파일을 git에 저장하여 쉽게 관리할 수 있게됩니다. 

이 외에도 여러가지 용어들이 있지만 지금은 간단하게 위 용어들만 확인하고 넘어가도록 하겠습니다.

# Work Pipeline

그렇다면 우리는 어떠한 방식으로 쿠버네티스를 사용할 수 있을까요? 아래 도식을 통해 개념적인 부분을 알아보도록 하겠습니다.

![wf](/assets/images/k8s-wf.png)

- kustomize.yaml에 service, ingress, deployment, configmap 에 관련된 정보를 적어 넣습니다.
- kubectl을 통해서 kustomize.yaml 파일을 실행합니다.
- 쿠버네티스는 명령을 받고 kustomize.yaml 에 설정된 내용대로 서버를 배포합니다.
- kustomize.yaml 을 git 에 배포하여 이력을 관리합니다.

## Data Engineering에서의 사용

데이터 엔지니어링에서는 아래와 같은 방식으로 쿠버네티스를 사용하고 있습니다.

![wf](/assets/images/k8s-de.png)

- 쿠버네티스 클러스터를 구성합니다.
- 주피터 노트북을 도커 이미지로 만들어서 필요할 때 파드로 생성합니다.
- 주피터 노트북 파드에 접근하여 EDA, 트레이닝, 모델을 생성합니다.
- 생성된 모델을 저장소에 저장합니다.
- 사용이 완료된 주피터 노트북 파드는 자원의 낭비를 막기위해 삭제합니다.
- 분석가는 필요할 때 다시 주피터 노트북을 생성하여 파라미터 튜닝, 모델을 다시 저장합니다.
- 쿠버네티스 위에 설치 된 에어플로우에서 모델을 주기적으로 이미지화 합니다.
- 이미지화 된 모델을 쿠버네티스에 서비스로 배포합니다.
- 유저는 쿠버네티스위에 배포된 서비스에 접근하여 모델에 맞춰 predict 의 결과를 반환 받습니다.
- 신규 작성 된 모델이 다시 파드로 배포됩니다.

이 과정은 유기적으로 동작하며 쿠버네티스에 의해 중단없이 작성되고 배포됩니다.

# Google Cloud Platform

구글 클라우드 플랫폼은 위에서 설명한 쿠버네티스를 간단하게 사용할 수 있도록합니다. 노드에대한 추가도 자유롭게 가능합니다. 아래 과정을 통해 구글 클라우드 플랫폼 (GCP) 을 설정하면 구글 쿠버네티스 엔진 (GKE)를 사용할 수 있습니다.

## Project 생성

[구글 클라우드 플랫폼](https://console.cloud.google.com/cloud-resource-manager)에 접근하여 프로젝트를 생성합니다.

## cluster 생성

[쿠버네티스 엔진](https://console.cloud.google.com/kubernetes/list/overview?project=jandbox) 에 접근하여 클러스터를 생성합니다.

## google cloud sdk 설치

로컬 컴퓨터에서 구글 서비스에 명령어를 통해 접근할 수 있도록 도와주는 툴입니다.

[google cloud sdk](https://cloud.google.com/sdk/docs/install)를 설치합니다.

[인증서 설치](https://cloud.google.com/kubernetes-engine/docs/how-to/cluster-access-for-kubectl)과정을 통해 컴퓨터에 인증서 설정을 합니다.

## Node 설정

환경설정에서 노드 수를 설정합니다. 

![wf](/assets/images/node.png)

## kubectl 설치

mac : https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-macos/

window : https://arisu1000.tistory.com/27824

## 접근 확인

gcloud auth login

gcloud config set project jandbox

gcloud container clusters get-credentials jandbox-dev --project jandbox

kubectl config use-context gke_jandbox_asia-east1-a_jandbox-dev

## Lens 설치

쿠버네티스를 쉽게 사용할 수 있게 도와주는 툴입니다. 

https://k8slens.dev/

# Reference

https://www.redhat.com/ko/topics/containers/what-is-kubernetes
https://engineer-mole.tistory.com/221