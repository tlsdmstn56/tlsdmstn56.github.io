---
layout: single
title: "[정리] Hello Minikube"
tags: ['kubernetes', 'cloud', 'docker','cluster']
date: 2018-09-09 10:00:00
---

[Hello Minikube](https://kubernetes.io/docs/tutorials/hello-minikube/)를 정리해 보았습니다.

이 포스트에서 하는 것

* nodejs로 Hello world 앱을 실행한다.
* Minikube에 앱 배포
* 앱 로그 보기
* 앱 이미지 업데이트

## 시작하기 전에
* Minikube 설치
* nodejs 설치
* 도커 설치

## Minikube 클러스터 생성하기

로컬 머신에 Minikube 클러스터를 생성할 것이다.

```bash
# for Mac
# install minikube
brew cask install minikube

# install kubectl 
brew install kubernetes-cli

# proxy 없이 사이트 접근 가능한지 확인하기
curl --proxy "" https://cloud.google.com/container-registry/

# 도커 데몬 실행하기, 아래 명령어로 확인 가능
docker images

# proxy 없이 Minikube 클러스터를 시작하기
#   --vm-driver의 default는 virtualbox
minikube start --vm-driver=hyperkit

# Minikube context 설정하기
kubectl config use-context minikube

# kubectl이 클러스터와 통신 가능한지 확인하기
kubectl cluster-info

# 쿠버네티스 대시보드 열기
minikube dashboard
```

## 배포할 nodejs 앱 만들기

`server.js`라는 이름으로 만든다.

```javascript
var http = require('http');

var handleRequest = function(request, response) {
  console.log('Received request for URL: ' + request.url);
  response.writeHead(200);
  response.end('Hello World!');
};
var www = http.createServer(handleRequest);
www.listen(8080);
```

실행하려면 `node server.js`를 터미널에서 실행하면 된다.

## 도커 파일 만들기

도커 이미지를 빌드할 도커파일을 만든다. 당연히 파일 이름은 `Dockerfile`이다. 단 `server.js`를 이미지 내부에 복사해야하므로 `server.js`와 동일한 디렉토리에 `Dockerfile`이 있어야 한다.

```docker
FROM node:6.9.2
EXPOSE 8080
COPY server.js .
CMD node server.js
```

빌드는 `docker build -t hello-node:v1 .` 로 한다.

## 배포하기

```bash
# --image-pull-policy는 다른 docker repository에서 
# 이미지를 pull하지 않고 로컬에서 사용하도록 강제
kubectl run hello-node --image=hello-node:v1 --port=8080 --image-pull-policy=Never
```

배포 보기

```bash
$ kubectl get deployments
NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
hello-node   1         1         1            1           3m
```

Pod 보기

```bash
$ kubectl get pods
NAME                          READY     STATUS    RESTARTS   AGE
hello-node-57c6b66f9c-4xrmb   1/1       Running   0          1m
```

클러스터 이벤트 보기
```bash
$ kubectl get events
```

`kubectl` 설정 보기
```bash
kubectl config view
```

## 서비스 만들기
디폴트로, pod는 kubernetes 클러스터 내에서 내부 IP 주소로만 접근 가능하다. `hello-node` 컨테이너를 kubernetes 가상 네트워크 밖에서 접근할 수 있게 하려면 pod를 kubernetes service 형태로 expose시켜야 한다.

```bash
kubectl expose deployment hello-node --type=LoadBalancer
```

서비스 확인하기
```bash
$ kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
hello-node   ClusterIP   10.0.0.71    <pending>     8080/TCP   6m
kubernetes   ClusterIP   10.0.0.1     <none>        443/TCP    14d
```

`--type=LoadBalancer` 옵션은 서비스를 클러스터 밖에서 expose 한다는 의미이다. 로드 밸랜서 기능을 제공하는 클라우드를 이용하는 경우 외부 IP 주소가 제공될 수 있다. `Minikube`에서는 `LoadBalancer` 옵션을 줌으로써 `minikube service` 커맨드로 해당 서비스를 접근할 수 있도록 해준다.

```bash
# 자동으로 IP 주소로 접속한다.
minikube service hello-node
```

```bash
# 이 명령어를 통해 로그를 볼 수 있다.(console.log의 출력값)
kubectl logs <POD-NAME>
```

## 앱 업데이트 하기

`server.js`를 다음과 같이 바꿔본다.

```javascript
response.end('Hello World');
// ==>
response.end('Hello World Again!');
```

도커 이미지를 다시 빌드한다.
```bash
docker build -t hello-node:v2 .
```

배포하는 이미지를 업데이트한다.
```bash
kubectl set image deployment/hello-node hello-node=hello-node:v2
```

다시 앱을 실행하여 바뀐 부분을 확인한다.
```bash
minikube service hello-node
```

## 제거 및 정리

클러스터에 생성한 리소스 제거
```bash
kubectl delete service hello-node
kubectl delete deployment hello-node
```

Minikube VM도 stop한다.
```bash
minikube stop
eval $(minikube docker-env -u)
```

Minikube VM을 제거한다.
```bash
minikube delete
```