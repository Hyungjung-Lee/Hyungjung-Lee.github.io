---
title: "Kubernetes Usage"
date: 2022-2-13 12:15:00 +0900
excerpt: "Kubernetes"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: server
tags: server study
---
# Kubernetes with Kustomize

![git](/assets/images/k8s.jpg)

이번 포스팅은 Kubernetes에서 Kustomize는 이용하여 쿠버네티스를 조작하는 방법에 대해서 소개합니다.

## Kustomize 란

Kustomize는 kustomization 파일을 통해 쿠버네티스 오브젝트를 사용자가 원하는 대로 변경하는(customize) 독립형 도구입니다. 이전에는 별도로 설치해야 했지만 쿠버네티스 버전 1.14 버전 이후부터는 Kubectl에 내장되어 별도의 설치 없이 사용할 수 있습니다.

## Kustomize 사용하는 이유

Kustomize를 사용하는 이유는 여러가지가 있습니다. Kustomize는 Kubernetes를 파일로서 관리하기 때문에 쿠버네티스의 환경을 파일을 통해서 쉽게 확인 할 수 있습니다. 개발자가 여러번의 명령어를 실행할 필요없이 파일로써 실행하기 때문에 기존 컴퓨터를 세팅할때 명령어를 사용할때처럼 중간에 빼먹거나, 이전 설정을 어떻게 했는지에 대해서 잊어버릴 문제가 없습니다. 파일이 동일하면 항상 동일한 결과로 배포되기 때문에 개발자는 파일을 신뢰하고 Kubernetes를 사용할 수 있습니다.
더하여 Kustomize파일을 git과 연동하면 서버 설정에 대해서 PR이 가능해서 교차검증도 가능하고 수정 이력에 대해서도 쉽게 파악이 가능합니다. 다른 서버에서 Pull을 받고 서버를 수정하는 것도 쉽게 가능합니다.  

## Kustomize 의 구조 

![wf](/assets/images/k8s-wf.png) 

Kustomize 의 구조는 저번 포스팅에서 소개한 위 그림을 참고하면 됩니다. 우리는 kustomization.yaml을 작성하고 수정하여 Kubectl 호출을 통해 Kubernetes를 수정할 수 있습니다.

## Kustomize 예시

그럼 이제 그림에는 빠져있는 kustomization.yaml의 자세한 구조에 대해서 소개하겠습니다.

### Base와 Overlay

kustomization.yaml은 Base폴더에 하나 Overlay 폴더에 환경 별로 여러개로 나뉘게 됩니다.

디렉토리를 그림으로 보면 아래와 같습니다.

![wf](/assets/images/kustomize-dir.png) 

kustomization 파일 하나는 단일 어플리케이션에서 사용되지만 kustomization 이 포함되어있는 폴더 구조를 보면 Kubernetes 클러스터에 전체에 배포되어 있는 앱의 구조를 파악 할 수 있습니다.

그림의 App B Directoy 하위에는 bases 와 overlays 폴더가 있습니다. 이것이 의미하는 것은 서비스를 운영하다보면 개발환경과 릴리즈 환경을 구분해야할 필요가 있습니다. 추가 기능을 위해서 개발 중인 서버가 유저에게 노출되면 안되기 때문입니다. 그렇기 때문에 이런 bases와 overlays 파일 구조로 kustomization 파일을 사용합니다. 개발을 위한 알파, 베타환경 서비스가 배포되어있는 릴리즈환경은 기본적으로 비슷한 환경을 갖고 있지만 환경에 따라 사용하는 데이터베이스 등 다른 옵션들을 사용하고 있을겁니다. 이때 kustomize는 각각의 앱을 다 별도로 관리하는 것이 아니라 동일한 설정은 base 하위의 kustomization 파일에 두고 환경마다 특정 설정들을 
다른 kustomization에 기록하여 사용하게 됩니다.

### kustomization.yaml

이제 kubectl이 호출하는 kustomization 파일의 자세한 내용에 대해서 확인해보겠습니다.
kustomize는 대부분 kustomization 파일을 통해 시작됩니다.

#### base/kustomization.yaml
```
namespace: study

commonLabels:
  service: flask
  component: study

resources:
  - deployment.yaml
  - service.yaml
```

먼저 base 하위에 있는 파일입니다. 어플리케이션의 분류를 정의하기위해 먼저 네임스페이스를 정해줍니다.
`commonLabels`는 어플리케이션의 라벨을 붙이는 역할을 합니다. 많은 수의 어플리케이션을 관리할때 검색 필터용도로 사용됩니다.

resources 하위에는 함께 사용하기위한 다른 역할의 yaml을 추가하는 내용입니다.

#### overlays/alpha/kustomization.yaml

```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization

nameSuffix: -alpha

namespace: study-flask-alpha

resources:
- ingress.yaml
- ../../bases

commonLabels:
  phase: alpha

patchesStrategicMerge:
- deployment.yaml
images:
- name: docker.io/lhj5772/lhj5772/flask_image_x86
  newTag: latest
```

이번에는 알파환경의 kustomization을 보겠습니다. 실제로 kubectl을 통해 서버에 배포할때는 각 환경의 kustomization을 이용하여 호출하게됩니다.

최상단에는 kubectl이 사용할 apiVersion 을 기록합니다. apiVersion버전에 따라 kustomization의 룰이 조금씩 다릅니다. 공식문서를 참고해서 사용할 버전을 확인합니다.

kind 는 현재 호출할 파일의 종류를 지정합니다. kustomize는 파일이름으로 롤이 정해지는 것이 아니라 다음과 같은 kind를 통해 롤이 정해집니다. 

네임스페이스는 base에서 사용한 것을 사용해도 되지만 각 환경별로 다른 네임스페이스를 지정할 수 있습니다. 이렇게 되면 해당 어플리케이션은 base의 네임스페이스를 사용하는 것이아닌 현재파일의 namespace를 사용하게 됩니다.

이렇게 원하는 항목에 대해서 Override 하여 kustomization을 사용하게 됩니다. commonLabels 의 경우 base의 commonLabels 항목과 합쳐져서 사용됩니다.

마지막으로 resources를 통해 base 폴더의 kustomization을 추가하는것을 확인할 수 있습니다. 이런 방식으로 overlay 가 진행됩니다.

### deployment.yaml

다음은 deployment 파일입니다. 배포 규칙에 대해서 적혀있습니다.

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: study-flask
spec:
  replicas: 2
  spec:
  template:
    spec:
      containers:
        - name: study-flask
          image: docker.io/lhj5772/flask_image_x86:latest
          env:
            - name: PHASE
              value: ALPHA
```

마찬가지로 api와 kind를 설정합니다. metadata의 name을 통해 각각의 파드는 이름이 부여됩니다. 즉 우리는 위에서 설정한 네임스페이스와 이름을 통해 각각의 pod를 찾을 수 있습니다. 스펙 항목에는 몇개의 Pod로 배포할지 어떠한 이미지로 배포할지에 대한 규칙이 적혀있습니다.


### service.yaml

다음은 서비스 입니다. 서비스는 우리가 작성한 어플리케이션을 다른어플리케이션을 포함한 외부에 노출하기 위해 작성합니다. 각각의 어플리케이션은 우리가 service.yaml 을 통해 지정한 규칙을 갖고 커뮤니케이션하게됩니다.

```
apiVersion: v1
kind: Service
metadata:
  labels:
    app.kubernetes.io/name: flask
    app.kubernetes.io/part-of: study
  name: study-flask
spec:
  ports:
    - name: http
      port: 8000
      targetPort: http
  # This selector select both real and canary deployment, therefore both will be routed.
  selector:
    app.kubernetes.io/name: flask
    app.kubernetes.io/part-of: study
```

어떠한 포트로 서비스가 노출될지 어떤 디폴로이먼트와 연결할지에 대해서 지정할 수 있습니다.

### ingress.yaml

마지막으로 ingress입니다. 우리가 서비스를 통해 파드를 노출했다면 인그래스는 서비스를 외부로 노출하는 역할로 합니다. 인그래스를 지정함으로써 우리는 클러스터 외부에서 내부로 접근할 수 있습니다.

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: flask-ingress
  annotations:
    # If the class annotation is not specified it defaults to "gce".
    kubernetes.io/ingress.class: "gce"
spec:
  rules:
  - http:
      paths:
      - path: /*
        pathType: ImplementationSpecific
        backend:
          service:
            name: study-flask-alpha
            port:
              number: 8000
```

## Kustomize 를 이용한 Kubernetes 오브젝트 배포

```
kubectl apply -k .
```

## 결과 확인하기

## Kustomize 를 이용한 전체 파이프라인

## Reference

https://kubernetes.io/ko/docs/tasks/manage-kubernetes-objects/kustomization/