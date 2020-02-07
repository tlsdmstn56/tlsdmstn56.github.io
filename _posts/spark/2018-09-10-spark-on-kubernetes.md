---
layout: single
title: "[정리] Kubernetes에서 Spark 실행하기"
tags: ['kubernetes', 'cloud', 'spark','cluster']
date: 2018-09-10 11:00:00
---

[Running Spark on Kubernetes](https://spark.apache.org/docs/2.3.0/running-on-kubernetes.html)를 보고 정리해보았습니다. Spark 2.3+ 이상에서만 지원되며 pyspark 나 자동 스케일업 등의 기능들은 아직 개발 중인 것으로 알고 있습니다.

> The Kubernetes scheduler is currently experimental. In future versions, there may be behavioral changes around configuration, container images and entrypoints.

## 필요 조건
* Spark 버전 2.3 이상
* Kubernetes 버전은 1.6+, 
  - `kubectl`로 접근 가능해야 함 
  - `minikube`로 로컬에서 실행 가능
    - DNS addon이 가능한 최신 버전을 사용하는 것이 좋음
    - 스파크 어플리케이션을 실행하기에는 기본 설정이 충분하지 않음. 하나의 Executor에 3 CPU와 4 GB 메모리를 가지는 것을 추천
* 클러스터의 pods에 대해 list, create, edit, delete할 권한을 반드시 가지고 있어야 함. 
  - `kubectl auth can-i <list|create|edit|delete> pods` 로 확인 가능
* 반드시 [Kubernetes DNS](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)를 설정해야 함

## 어떻게 작동하는가

![kubernetes-how-it-work](https://spark.apache.org/docs/2.3.0/img/k8s-cluster-mode.png)

`submit-commit`가 직접 kubernetes 클러스터에 스파크 어플리케이션을 보낼 수 있다. 그 과정은 아래와 같다.

1. 스파크가 kubernetes pod에서 실행되는 스파크 드라이버를 생성한다.
2. 드라이버가 kubernetes pod안에서 실행되는 여러 개의 executor들을 생성한다. 그 후 어플리케이션 코드를 실행한다.
3. 어플리케이션이 종료되면 executor pod들은 종료되며 clean up 된다. 그러나 driver pod는 로그를 보관하며 "completed" 상태로 남아있는다. 드라이버 pod는 GC로 제거되거나 직접 제거될 때까지 남아있는다.

> 드라이버 pod "Completed" 상태가 되면 추가적인 컴퓨팅 리소스 또는 메모리 리소스를 사용하지 않는다.

[node selector](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#nodeselector)를 통해 클러스터의 특정 노드들만 사용할 수 있도록 할 수 있다. 또한 고급 기능인 [node/pod affinities](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity)를 사용할 수 있게 할 예정이다.

## kubernetes에 어플리케이션 submit하기
### 도커 이미지
`kubernetes/dockerfiles/` 디렉토리에 spark `Dockrfile`이 있다. 원하는 대로 수정한 후에 `bin/docker-image-tool.sh`를 실행하면 도커 이미지를 빌드할 수 있다.

```bash
# examples
$ ./bin/docker-image-tool.sh -r <repo> -t my-tag build
$ ./bin/docker-image-tool.sh -r <repo> -t my-tag push
```

### 클러스터 모드
```bash
$ bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --conf spark.executor.instances=5 \
    --conf spark.kubernetes.container.image=<spark-image> \
    local:///path/to/examples.jar
```

`--master`는 마스터 노드의 주소를 나타낸다.
* `spark.master` 으로 설정 가능
* url 포맷: `k8s://<api_server_url>`
  - http 프로토콜이 설정되어있지 않으면 자동으로 https
  - `k8s://example.com:443`, `k8s://https://example.com:443` 둘 다 동일

`--name`는 kubernetes의 리소스 이름을 나타낸다. 따라서 만드시 **lowercase alphanumeric**과 "." , "_" 여야 한다. `spark.app.name`으로도 설정 가능하다.

apiserver URL을 찾는 방법은 `kubectl cluster-info`로 확인하는 것이다.

로컬 프록시는 `kubectl proxy`로 시작할 수 있다.

## 의존성 관리
* 어플리케이션 의존성은 도커 이미지 빌드할 때 포함할 수 있다.
  - `local://` URI로 찾거나
  - `SPARK_EXTRA_CLASSPATH`를 설정한다.

### 원격 종속성을 이용하는 경우
* `spark.jars`를 이용하거나 `--jars` 옵션을 이용한다.
* `spark.files`를 이용하거나 `--files` 옵션을 이용한다.

```bash
$ bin/spark-submit \
    --master k8s://https://<k8s-apiserver-host>:<k8s-apiserver-port> \
    --deploy-mode cluster \
    --name spark-pi \
    --class org.apache.spark.examples.SparkPi \
    --jars https://path/to/dependency1.jar,https://path/to/dependency2.jar
    --files hdfs://host:port/path/to/file1,hdfs://host:port/path/to/file2
    --conf spark.executor.instances=5 \
    --conf spark.kubernetes.container.image=<spark-image> \
    https://path/to/examples.jar
```

## 시크릿(secret) 관리
kubernetes 시크릿은 스파크 어플리케이션이 secure한 서비스에 접근할 수 있도록 credential을 제공할 때 사용된다.
* `spark.kubernetes.driver.secrets.[SecretName]=<mount path>`
* `spark.kubernetes.executor.secrets.[SecretName]=<mount path>`

시크릿이 driver와 executor 모두의 네임스페이스에 포함되어 있다고 가정한다.

터미널에서는 아래의 옵션으로 시크릿을 줄 수 있다.

```bash
--conf spark.kubernetes.driver.secrets.spark-secret=/etc/secrets
--conf spark.kubernetes.executor.secrets.spark-secret=/etc/secrets
```

## 모니터링 및 디버깅
### 로그
```bash
$ kubectl -n=<namespace> logs -f <driver-pod-name>
```
### 드라이버 UI
```bash
$ kubectl port-forward <driver-pod-name> 4040:4040
```
### 디버깅
```bash
$ kubectl describe pod <spark-driver-pod>
$ kubectl logs <spark-driver-pod>
```

## Kubernetes 기능
### 네임스페이스
* [namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/)
* [ResourceQuota](https://kubernetes.io/docs/concepts/policy/resource-quotas/)
### RBAC
Role Based Access Control
아직은 잘 모르겠다. [여기](https://spark.apache.org/docs/2.3.0/running-on-kubernetes.html#rbac)를 참고하자.
