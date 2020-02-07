---
layout: single
title: "[gradle] Ch2 gradle build"
tags: ['build automation', 'development']
date: 2018-08-19 21:00:00
---

[Creating New Gradle Builds](https://guides.gradle.org/creating-new-gradle-builds/)를 참고하였습니다.

## 프로젝트 초기화

```bash
$ gradle init
Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 3s
2 actionable tasks: 2 executed
```

위 명령어를 사용하면 다음과 같은 디렉터리 구조가 형성된다.

```text
├── build.gradle  
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar  
│       └── gradle-wrapper.properties  
├── gradlew  
├── gradlew.bat  
└── settings.gradle  
```

* build.gradle: 현재 프로젝트에 대한 설정이 들어있는 빌드 스크립트
* gradle-wrapper.jar: `Gradle Wrapper` JAR
* gradle-wrapper.properties: `Gradle Warpper`에 대한 설정파일
* gradlew/ gradlew.bat: `gradle Wrapper`에 대한 실행 스크립트(유닉스/ 윈도우)
* settings.gradle: gradle build를 설정하는 스크립트

> `gradle init`을 통해 다양한 타입의 프로젝트를 생성해낼 수 있다. [여기](https://docs.gradle.org/4.10-rc-2/userguide/build_init_plugin.html#sec:build_init_types)를 참고하자

## 태스크 만들기
Gradle은 Grovvy와 Kotlin 기반 DSL을 통해 태스크를 설정하고 만들 수 있는 API를 제공한다. `Project`는 여러 개의 `Task` 구성되어 있다고 볼 수 있다.

```gradle
// gralde
task copy(type: Copy, group: "Custom", description: "Copies sources to the dest directory") {
    from "src"
    into "dest"
}
```

이 태스크는 src 폴더에 있는 파일을 `/dest` 디렉토리로 복사한다. 태스크 실행은 아래와 같이 하면 된다.

```bash
$ ./gradlew copy
```

## 플러그인 적용하기

```gradle
// in build.gradle
// add plugin
plugins {
    id "base"
}

// add task
task zip(type: Zip, group: "Archive", description: "Archives sources in a zip file") {
    from "src"
    setArchiveName "basic-demo-1.0.zip"
}
```

## 빌드 확인 및 디버깅

### 실행 가능한 태스크 확인
```bash
$ ./gradlew tasks

> Task :tasks

------------------------------------------------------------
All tasks runnable from root project
------------------------------------------------------------

Archive tasks
-------------
zip - Archives sources in a zip file

Build tasks
-----------
assemble - Assembles the outputs of this project.
build - Assembles and tests this project.
clean - Deletes the build directory.

Build Setup tasks
-----------------
init - Initializes a new Gradle build.
wrapper - Generates Gradle wrapper files.

Custom tasks
------------
copy - Simply copies sources to a the build directory

/* ... more tasks ...*/

To see all tasks and more detail, run gradlew tasks --all

To see more detail about a task, run gradlew help --task <task>

BUILD SUCCESSFUL in 0s
1 actionable task: 1 executed
```

### 빌드 분석 및 디버깅
빌드 스캔을 이용한다.

예를 들어 위의 zip 태스크를 스캔하려면

```bash
$ ./gradlew zip --scan
```

하면 된다.

### 가능한 속성(properties) 확인하기
```bash
$ ./gradlew properties
> Task :properties

------------------------------------------------------------
Root project
------------------------------------------------------------

buildDir: /Users/.../basic-demo/build
buildFile: /Users/.../basic-demo/build.gradle
description: null
group:
name: basic-demo
projectDir: /Users/.../basic-demo
version: unspecified

BUILD SUCCESSFUL
```