---
layout: single
title: "[디자인패턴] strategy pattern"
tags: ['java', 'design pattern']
date: 2018-09-16 01:00:00
---

[Head First Design Pattern](http://www.yes24.com/24/goods/1778966?scode=032&OzSrank=1) *Ch1 디자인 패턴 소개*을 정리한 것입니다.

## 디자인 원칙

* 어플리케이션에서 달라지는 부분을 찾아 달라지지않는 부분으로부터 분리시킨다.
  * 바뀌는 부분을 따로 뽑아 캡슐화시킨다.
  * 나중에 바뀌지 않는 부분에는 영향을 미치지 않으면서 바뀌는 부분을 수정하거나 확장할 수 있다.
* 상속보다는 구성을 활용한다.
  * 구성은 객체간 상호작용을 일컫는 듯 하다.
* 구현(Implementation)이 아닌 인터페이스에 맞춰서 프로그래밍한다.

## Stretegy Pattern

> 알고리즘군을 정의하고 각각을 캡슐화하여 교환해서 사용할 수 있도록 만든다. 이 패턴을 활용하면 알고리즘을 사용하는 클라이언트와는 독립적으로 알고리즘을 변경할 수 있다.

에를 들어 (너무나 유명한 예제이지만 빠른 이해를 위해) 오리를 다음과 같이 구현했다고 하자.

![hf-design-pattern-ch1-1](/assets/hf-design-pattern-ch1/hf-design-pattern-ch1-1.svg)

여기에 fly라는 메소드를 추가한다고 하자. 가장 쉽게 생각해볼 수 있는 방법은 Duck 클래스에 fly를 추상메소드로 선언한 수 상속받은 클래스에서 구현하는 것을 생각해볼 수 있다. 그림으로 나타내면 아래와 같다.

![hf-design-pattern-ch1-2](/assets/hf-design-pattern-ch1/hf-design-pattern-ch1-2.svg)

위 방식의 문제점은 추가된 메소드 fly가 각 클래스마다 서로 다른 방식으로 작동해야 될 수도 있다는 것이다. 날 수 있는 오리가 있고 날 수 없는 오리가 있을 수도 있다는 뜻이다. 또한 똑같이 날 수 있는 오리에 대해서 서로 다른 서브클래스들에 동일한 fly메소드가 들어갈 수도 있다. 위 예시는 상속의 활용이 부적절한 예시임을 나타낸다.

> 상속이 전부는 아니다.

인터페이스를 사용해 해결해보기로 하자.

![hf-design-pattern-ch1-3](/assets/hf-design-pattern-ch1/hf-design-pattern-ch1-3.svg)

인터페이스를 각 서브클래스에서 구현하면 각 서브클래스가 `fly`를 어떤 방식으로 구현했는지 확인하기 위해 모든 서브 클래스를 확인해야 한다. 또한 서브클래스 중에는 동일한 방식으로 `fly`할 수 도 있는데 이 메소드의 구현을 바꾸려는 경우 동일한 동작을 하는 모든 클래스들을 찾아 전부 바꾸는 수고를 해야 한다. 이때 디자인 첫 번째 디자인 원칙을 사용해보자.

> 어플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분으로 부터 분리시킨다.

Duck의 각 서브클래스마다 달라지는 부분은 `fly`와 `quack`이다. `fly`와 `quack`에 대해 서로 다른 구현(`quack`의 소리가 다르지만 소리를 내기는 내는 경우, 책에 있는 클래스로 예시를 드려고 하니 좀 그렇네요..)을 관리할 수 있는 클래스 집합을 디자인할 것이다. 각 클래스 집합에 대해 인터페이스를 정의한 후 인터페이스에 따라 메소드를 구현할 것이다.

> 구현이 아닌 인터페이스에 맞춰서 프로그래밍한다.

각 행동은 `FlyBehavior`과 `QuackBehavior`라는 인터페이스로 나타내고 이 인터페이스를 구현함으로써 서로 다른 행동들을 나타내는 메소드를 만든다. `Duck`클래스에서 구현하지 않는다. 인터페이스를 구현한 클래스를 Duck 클래스에 포함시킴으로써 Duck이 `fly`와 `quack`을 실행할 수 있도록 한다. 이를 그림으로 나타내면 아래와 같다.

![hf-design-pattern-ch1-4](/assets/hf-design-pattern-ch1/hf-design-pattern-ch1-4.svg)

서브클래스와 함께 나타내면 다음과 같다.

![hf-design-pattern-ch1-5](/assets/hf-design-pattern-ch1/hf-design-pattern-ch1-5.svg)

이렇게 하면 각 서브클래스들이 `fly`나 `quack`를 어떻게 구현하고 있는지 한 눈에 확인할 수 있으며 새로운 구현을 추가하거나 기존의 구현을 수정하더라도 모든 서브 클래스들을 볼 필요가 없기 때문에 유지 보수가 용이하다.

구체적인 코드로 나타내면 다음과 같다.


## 자바 코드 예시
### Duck과 서브클래스
```java
public abstract class Duck {
    FlyBehavior flybehavior;
    QuackBehavior quackBehavior;
    public Duck() { }
    public abstract void display();
    public void performFly() {
      /** 
        flybehavior에 원하는 구현을 집어넣으면 실제 
        구현을 몰라도 fly 메소드를 호출하면 된다.  
      */
        flybehavior.fly();
    }
    public void performQuack() {
        quackBehavior.quack();
    }
    public void swim() {
        System.out.println("All ducks can swim");
    }
    public void setFlyBehavior(FlyBehavior fb) {
        flybehavior = fb;
    }
    public void setQuackBehavior(QuackBehavior qb) {
        quackBehavior = qb;
    }
}
/* 예시로 2개만 만든다. */
public class MallardDuck extends Duck {
    public MallardDuck() {
      /**
          클래스에 맞는 구현을 생성한다. fly메소드나 
          quack메소드를 호출하면 여기서 생성된 
          구현을 호출하게 된다.
        */
        quackBehavior = new Quack();
        flybehavior = new FlyWithWings();
    }

    public void display() {
        System.out.println("mallard duck");
    }
}

public class ModelDuck extends Duck {
    public ModelDuck() {
        flybehavior = new FlyNoWay();
        quackBehavior = new Quack();
    }
    public void display() {
        System.out.println("model duck");
    }
}
```

### FlyBehavior와 구현
```java
public interface FlyBehavior {
    public void fly();
}

class FlyWithWings implements FlyBehavior {
    public void fly() {
        System.out.println("Flying!!");
    }
}

class FlyNoWay implements FlyBehavior {
    public void fly() {
        System.out.println("Cannot Fly");
    }
}

class FlyRocketPowered implements FlyBehavior {
    public void fly() {
        System.out.println("rocket powered fly");
    }
}
```

### QuackBehavior와 구현
```java
public interface QuackBehavior {
    public void quack();
}

class Quack implements QuackBehavior {
    public void quack() {
        System.out.println("Quack!!");
    }
}

class MuteQuack implements QuackBehavior {
    public void quack() {
        System.out.println("...");
    }
}

class Squeak implements QuackBehavior {
    public void quack() {
        System.out.println("Squcak!!");
    }
}
```

### 데모 예시
```java
public class DuckDemo {
    public static void main(String[] args) {
        /* 구현 생각할 필요 없이 performFly,
           performQuack을 호출하면 된다. */
        Duck mallard = new MallardDuck();
        mallard.performFly();
        mallard.performQuack();

        Duck model = new ModelDuck();
        model.performFly();
        model.setFlyBehavior(new FlyRocketPowered());
        model.performFly();
    }
}
```