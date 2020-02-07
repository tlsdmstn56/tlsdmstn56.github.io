---
layout: single
title: "[정리] Data Science at the command line"
tags: ['data science', 'stat']
date: 2018-07-18 01:00:00
toc: true
toc_label: 목차
toc_icon: "cog"
---

[Data Science at the Command Line](https://www.datascienceatthecommandline.com/)을 개인적으로 정리한 것입니다.

## Introduction

## Getting Started

### 환경(Environment)

4가지로 구성

* 커맨드라인툴
* 터미널
  * 여기서 입력한 명령을 쉘로 보냄
* 쉘
* OS



### 커맨드라인툴 종류

* 바이너리
* 쉘 빌트인
* 스크립트
* 쉘 함수
* alias

```type```을 통해 확인할 수 있다.

```bash
$ type -a pwd
pwd is a shell builtin
pwd is /bin/pwd
$ type -a cd
cd is a shell builtin
$ type -a fac
fac is a function
fac ()
{
    ( echo 1;
    seq $1 ) | paste -s -d\* | bc
}
$ type -a l
l is aliased to `ls -1 --group-directories-first'
```

### 커맨드라인툴 조합하기

* \> : 출력하기
* \>\> : 이미 있는 파일에 추가하기
* |: 파이프 라인

## Obtaining Data

### Decompressing File

```bash
$ tar -xzvf data/logs.tar.gz
# options
# -x : extract
# -z : gzip 압축/ 압축풀기
# -v : verbose
# -f : 
```