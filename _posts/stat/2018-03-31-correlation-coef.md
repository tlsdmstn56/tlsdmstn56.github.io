---
layout: single 
title: "상관 계수"
tags: [statistics,통계]
date: 2018-03-31 00:00:00
categories:
  - "통계"
# toc: true
# toc_label: 목차
# toc_icon: "cog"
---
상관 계수의 용도의 대한 고찰

## 상관 계수의 종류
* 피어슨 상관계수
* 스피어만 상관계수
* 켄달의 상관 계수

## 상관 계수 사용시 주의사항
상관 계수는 매우 광범위하게 사용되는 분석 도구이며 종종 적절하지 않은 방법으로 사용되기도 한다. 다음은 상관계수의 사용과 관련한 주의 사항들이다.

1. 독립적인 데이터에 대해서만 사용해야 한다. 데이터가 독립적이지 않은 반복 측정 데이터에 적용해서는 안된다. 예를 들어, 같은 환자에서 4 번째와 8 번째 혈압 사이의 관계를 설명하기 위해 이러한 상관 관계 측정을 사용하는 것은 적절하지 않다.

2. 많은 독립변수가 있는 데이터를 상관분석할 때 많은 상관 계수가 발생하므로 결과 해석에 주의를 기울여야 한다.

3. 두 변수를 가진 시계열 자료는 잘못 해석할 가능성이 높다. 상관 분석을 하기 전에 시간에 따른 추세(Time Trend)를 제거해야 한다.

4. 상관 분석의 결과를 모집단에 적용하려면 분석 대상이 반드시 모집단을 대표(즉, 랜덤 샘플링)해야 한다. 피어슨 상관 계수는 이상치(outlying observation)에 민감하다. 모든 상관계수는 샘플 선택 편향(Sample Selection Bias)에 영향을 받는다.

5. 한 변수는 부분이고 다른 한 변수는 전체를 나타내는 두 변수에 대해 상관 분석을 할 때에는 특별한 주의를 기울여야 한다. 예를 들어, 두 번째 변수의 값는 첫 번째 변수의 값을 "포함"하기 때문에 10세의 키와 성인의 키 사이에는 양의 상관 관계가 있음을 알 수 있다.

6. Correlation should not be used to study the relation between an initial measurement, X, and the change in that measurement over time, Y - X. X will be correlated with Y - X due to the regression to the mean phenomenon.

7. Small correlation values do not necessarily indicate that two variables are unassociated. For example, Pearson's rp will underestimate the association between two variables that show a quadratic relationship. Scatterplots should always be examined.

8. Correlation does not imply causation. If a strong correlation is observed between two variables A and B, there are several possible explanations: (a) A influences B; (b) B influences A; (c) A and B are influenced by one or more additional variables; (d) the relationship observed between A and B was a chance error.

9. "Regular" correlation coefficients are often published when the researcher really intends to compare two methods of measuring the same quantity with respect to their agreement. This is a misguided analysis, because correlation measures only the degree of association; it does not measure agreement. The next section of this lesson will present a measure of agreement.

------
참고한 사이트: [Use and Misuse of Correlation Coefficients](https://onlinecourses.science.psu.edu/stat509/node/160)

