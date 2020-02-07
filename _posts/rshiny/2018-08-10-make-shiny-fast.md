---
layout: single
title: "Make Shiny Fast"
tags: ['r', 'shiny','webapp']
date: 2018-08-10 09:00:00
toc: true
toc_label: 목차
toc_icon: "cog"
---
강연 [Make Shiny Fast](https://www.rstudio.com/resources/videos/make-shiny-fast-by-doing-as-little-work-as-possible/)을 보고 정리한 것입니다.

## Optimization Loop Method
1. Benchmark
2. Analyze
3. Recommend
4. Optimize, then go back 1.

> Recommend vs Optimize
>   Recommend: 어떤 부분을 Optimize 할 지 결정, 예를 들어 20%의 속도를 증가시키는데 너무 오랜 시간의 작업이 필요하면 하지 않는다고 결정을 내린다.
>   Optimize: 실제로 구현을 하는 과정

## Benchmark
1. Model: Representative user actions(User scenario)
2. Metrics: Latencies experienced by model user

## Analyze
1. Exercise model to produce _metric_ data
2. Identify the **one slowest thing**

Optimizing slowest thing

### `Rprof` and `profvis`
`Rprof`: sample what R is doing
* computing(ggplot2, dplyr)
* Waiting (db, network, disk)

`profvis`: visualize `Rprof` output

## Optimization
### pre-process
### beware dplyr::group_by