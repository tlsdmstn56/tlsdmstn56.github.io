---
layout: single
title: "[gradle] Ch1 build scan"
tags: ['build automation', 'development']
date: 2018-08-19 21:00:00
---

[Creating Build Scans](https://guides.gradle.org/creating-build-scans/)를 참고하였습니다.

> A **build scan** is a shareable and centralized record of a build that provides insights into what happened and why.

빌드 전체적인 상황을 확인할 수 있도록 설정하는 과정을 말하는 것 같다.

## 빌드 스캔 플러그인 자동 적용시키기

4.3버전 이상부터는 별도의 추가적인 설정 없이 아래 명령으로 시작할 수 있다. 아래 명령어를 사용하게 되면 자동으로 빌드 스캔 플러그인이 적용된다. 단 라이센스에 동의한다고 해야만 사용할 수 있다. 라이센스 동의는 자체적인 클라우드에서 빌드 상황을 확인할 수 있도록 하는 것에 대한 약관 동의인 것 같다.

```bash
$ ./gradlew build --scan

BUILD SUCCESSFUL in 6s

Do you accept the Gradle Cloud Services license agreement (https://gradle.com/terms-of-service)? [yes, no]
yes
Gradle Cloud Services license agreement accepted.

Publishing build scan...
https://gradle.com/s/czajmbyg73t62
```

## 프로젝트의 모든 빌드에 대해 빌드 스캔 활성화시키기
`plugins` 블록을 `build.gradle`에 추가한다.
```gradle
/* in build.gradle */
plugins {
    id 'com.gradle.build-scan' version '1.16'
}
```

> 이미 `plugins`가 존재한다면 빌드 스캔 플러그인을 가장 위에 놓도록 한다. 그렇지 않은 경우에도 정상적으로 작동하나 유용한 정보들이 빠질 수 있다.

## 라이센스 동의하기
빌드 스캔을 [https://scans.gradle.com](https://scans.gradle.com/?_ga=2.10666868.1723102034.1536900384-1970392633.1536900384)에 퍼블리시 하려면 라이센스에 동의해야 한다. 커맨드라인에서도 할 수 있으나 `build.gradle`에서도 할 수 있다.

```gradle
/* in build.gradle */
buildScan {
    termsOfServiceUrl = 'https://gradle.com/terms-of-service'
    termsOfServiceAgree = 'yes'
}
```

`buildScan`블록에서 빌드 스캔에 대한 설정을 할 수 있다.