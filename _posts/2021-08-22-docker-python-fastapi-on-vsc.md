---
title: "Docker X Python X FastAPI 환경 Visual Studio Code로 작업하기"
date: 2021-08-22 09:00:00 +0900
excerpt: "Docker X Python X FastAPI 환경 Visual Studio Code로 작업하기"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: study service kdk
tags: kdk service
---
Docker X Python X FastAPI 환경 Visual Studio Code로 작업하기
=============

## Overview
Python FastAPI를 Docker 환경위에서 구동하도록 Visual Studio Code를 이용한다.
* * *

### Docker 설치
[Docker 공식 홈페이지](https://docs.docker.com/desktop/windows/install/)

### Docker Command
#### Docker Build
docker build -t myimage .    
#### Docker Run
docker run -d --name mycontainer -p 8000:8000 myimage
#### Docker Push
docker push lhj5772/myimage:latest

### FastAPI
[FastAPI 공식 홈페이지](https://fastapi.tiangolo.com/deployment/docker/)


### 참고
[Github](https://github.com/Hyungjung-Lee/StudyFastAPITemplate)