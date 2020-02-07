---
layout: single
title: "[Effective Java] Item 1: 생성자 대신 static factory method를 고려하자"
tags: [java,programming]
date: 2018-08-16 01:00:00
categories:
  - "Effective Java"
toc: true
toc_label: 목차
toc_icon: "cog"
---

Item 1: Consider static factory methods instead of constructors

예시

```java
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Boolean.FALSE;
}
```

디자인패턴의 *Factory Method* 패턴과는 다르다.

## 장점
### 이름을 가질 수 있다.

생성자와 달리 Static Factory Method는 이름을 가질 수 있다.

```java
class BigInteger{
    /* Constructor */
    BigInteger(int, int, Random) {/* ... */}
    /* static factory method */
    static BigInteger probablePrime(int, int, Random) {/*  */}
}
```

비슷한 역할을 하지만 이름을 가진다. 셍성자만 쓰면 어떤 이유에서 클래스를 생성하는지 알 수 없다. 

또한 같은 signature를 같는 생성자는 하나 밖에 없지만 Static Factory 방법을 사용하게 되면 같은 signature를 가져도 여러 방식으로 구현이 가능하다. 

### 함수 호출 시 새로운 객체를 생성할 필요가 없다.

반드시 함수를 호출하는 시점에 객체를 생성할 필요가 없다. 미리 생성된(또는 캐싱된)) 객체를 반환하는 것도 가능하다. 객체를 생성하지 않는다는 점에서 위의 `valueOf` 함수와 비슷하다. *Flyweight* 패턴과도 비슷하다. 같은 객체를 자주 요청하는 경우, 객체 생성 비용이 높은 경우 유용하다.

*instance-control*이 가능하다. 즉, 싱글톤이거나 noninstantiable임을 보장할 수 있다.

### A third advantage of static factory methods is that, unlike constructors,they can return an object of any subtype of their return type. 

TBD