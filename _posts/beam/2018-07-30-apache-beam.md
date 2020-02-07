---
layout: single
title: "[번역] Beam Programming Guide"
tags: ['data processing', 'apahce beam', 'python']
date: 2018-07-30 09:00:00
toc: true
toc_label: 목차
toc_icon: "cog"
---

[Apache Beam Programming Guide](https://beam.apache.org/documentation/programming-guide/)를 번역한 것입니다.

Python SDK 기준으로 작성되었습니다.

## Overview
* Beam을 사용하기 위해서는 드라이버 프로그램을 작성해야 한다.
    * 드라이버 프로그램은 모든 입력과 변환, 출력을 포함한 파이프라인을 정의한다.
    * 파이프라인의 실행 옵션도 설정한다.(보통은 CLI 옵션으로 pass된다)
    * 어떤 백엔드로 파이프라인을 실행할 것인지 결정하는 Pipeline Runner를 포함한다.

* `Pipeline`: 전체 데이터 처리 태스크를 캡슐화한 것이다. 
    * 모든 Beam 드라이버 프로그램은 반드시 `Pipeline`을 생성해야 한다. 
    * `Pipeline` 생성시 여러 실행 옵션을 줄 수 있다.
* `PCollection`: 분산된 데이터를 나타낸다.
    * _bounded_: 고정된 파일(로컬 파일)
    * _unbounded_: 지속적으로 파일 source가 바뀌거나 스트리밍될 때
    * 외부 데이터, 인메모리 데이터 등을 읽어 첫 `PCollection`을 생성할 수 있다
    * 파이프라인 각 단계의 입려과 출력을 의미한다.
* `PTransform`: 데이터 처리 연산을 의미한다.
    * `PTransform`은 하나 이상의 `PCollection`을 받아 `PCollection`을 출력한다.

보통 Beam 드라이버 프로그램은 다음과 같이 작성된다.
1. `Pipeline`을 생성한다. Pipeline Runner을 포함하는 실행 옵션을 설정한다.
2. 첫 `PCollection`을 생성한 후 데이터를 읽는다.