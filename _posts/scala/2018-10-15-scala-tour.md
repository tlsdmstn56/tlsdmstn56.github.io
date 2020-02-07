---
layout: single
title: "[정리] Tour of Scala"
tags: ['programming', 'scala']
date: 2018-10-15 21:00:00
---

[Tour of Scala](https://docs.scala-lang.org/tour/tour-of-scala.html)를 보면서 기억 못할만한 개념 위주로 정리했습니다.


## Variance

```scala
class Foo[+A] // A covariant class
class Bar[-A] // A contravariant class
class Baz[A]  // An invariant class
```

 내가 이해한 대로라면 `Variance`라는 개념은 Generic 클래스에서만 존재하는 개념으로 `covariant` 클래스는 A와 A의 서브타입(자식 클래스?) 까지 다 받을 수 있고, `contravariant`는 A와 A의 서브타입의 제네릭 클래스가 있는 경우 A를 받은 제네릭 클래스가 A의 서브타입을 받은 클래스까지 받을 수 있다. `invariant`는 두 경우 모두 해당하지 않는 경우로 반드시 A 타입만 받을 수 있는 것을 말한다. 말로만 쓰면 어려우니까 예시를 들어서 보자.

 List는 `List[+A]`로 구현이 되어있다고 한다. 이를 이용해서 `covariant`한 제네릭 클래스가 무엇을 의미하는지 알아보자.

 ```scala
 /* Test를 위한 추상 클래스와 케이스 클래스 정의*/
abstract class Animal {
  def name: String
}
case class Cat(name: String) extends Animal
case class Dog(name: String) extends Animal
/* 테스틀 위한 Cat과 Dog의 리스트 값을 정의 */
val cats: List[Cat] = List(Cat("Whiskers"), Cat("Tom"))
val dogs: List[Dog] = List(Dog("Fido"), Dog("Rex"))

/* 이 함수의 argument인 animals: List[Animal] 에 dog와 cat을 넣을 것이다. */
def printAnimalNames(animals: List[Animal]): Unit = {
    animals.foreach { animal =>
      println(animal.name)
    }
}
printAnimalNames(cats) 
printAnimalNames(dogs) 
printAnimalNames(cats ::: dogs) // 정상 실행 cats:::dogs = List[Product with Serializable with Animal]
```

`List[Product with Serializable with Animal]`는 잘 모르겠지만 Animal 클래스와 호환이 되는 어떤 것으로 구성된 List를 의미하는 것 같다.

다음은 `contravariant`한 제네릭 클래스에 대해서 알아본다. 참고로 정의는 위에서 처럼 `class Bar[-A]`로 한단다. 정말 헷갈리게도 만들어 놓으셨다.

```scala
/* base 추상 클래스 */
abstract class Printer[-A] {
  def print(value: A): Unit
}

class AnimalPrinter extends Printer[Animal] {
  def print(animal: Animal): Unit =
    println("The animal's name is: " + animal.name)
}

class CatPrinter extends Printer[Cat] {
  def print(cat: Cat): Unit =
    println("The cat's name is: " + cat.name)
}
```

Cat은 Animal의 서브타입이고 `Printer[-A]`로 정의 되었으므로 `Printer[Cat]`은 `Printer[Animal]`의 서브타입이 된다. 그러면 이게 맞는지 직접 실행해서 확인해보자.

```scala
val catPrinter: Printer[Cat] = new CatPrinter
val animalPrinter: Printer[Animal] = new AnimalPrinter

val myCat: Cat = Cat("Boots")
def printMyCat(printer: Printer[Cat]): Unit = {
    printer.print(myCat)
}
printMyCat(catPrinter)    // The cat's name is: Boots 
printMyCat(animalPrinter) // The animal's name is: Boots
```

마지막으로 `Invariant`는 `covariant`도 아니고 `contravariant`도 아닌 제네릭 클래스를 말하며 서브타입과의 호환(?)이 안된다. 예시를 보면 바로 이해가 된다.

```scala
/* invariatn한 클래스를 정의하자 */
class Container[A](value: A) {
  private var _value: A = value
  def getValue: A = _value
  def setValue(value: A): Unit = {
    _value = value
  }
}
/* 테스트 */
val catContainer: Container[Cat] = new Container(Cat("Felix"))
val animalContainer: Container[Animal] = catContainer // 여기서 에러가 뜬다.
```

여기까지 이해가 됐으면 더 골 때리는 상황을 보자. `trait Function1[-T, +R]`은 scala 표준 라이브러리에 정의되어있는 `trati` 정의로 argument를 한 개를 받아서 리턴하는 함수 객체를 나타낸다. `-T`가 argument 타입이 되며 `+R`이 리턴 타입이 된다. 익명 함수의 리터럴한 표현인 `A => B`는 사실은 `Function1[A, B]`를 나타내는 것이다.

```scala
abstract class SmallAnimal extends Animal
case class Mouse(name: String) extends SmallAnimal
def printAnimalName(animal: Animal) = println(animal.name)
/* 모두 정상적으로 이름 출력 */
printAnimalName(Cat("cat"))
printAnimalName(Dog("dog"))
printAnimalName(Mouse("mouse"))
```

## Type Bound

`Upper Type Bound`는 타입이 계속 상속(extend) 되는데 어디까지 호환되는지 상한선을 정하는 개념으로 보면 된다. 백문이불여일견이다. 직접 에시로 보자

```scala
/*
       Animal
      /      \
    Pet     Lion
   /  \
Cat    Dog
*/
abstract class Animal {
 def name: String
}

abstract class Pet extends Animal {}

class Cat extends Pet {
  override def name: String = "Cat"
}

class Dog extends Pet {
  override def name: String = "Dog"
}

class Lion extends Animal {
  override def name: String = "Lion"
}
/* PetContainer의 제네릭 변수 P는 Pet 이하의 클래스(Pet의 서브타입)들 즉 Cat, Dog만 받는다. */
class PetContainer[P <: Pet](p: P) {
  def pet: P = p
}

val dogContainer = new PetContainer[Dog](new Dog)
val catContainer = new PetContainer[Cat](new Cat)
val lionContainer = new PetContainer[Lion](new Lion) // Error!
```

`Lower Type Bound`는 그 반대로 호환되는 서브타입의 하한선을 정하는 것이다. 이것도 예시로 보자.

```scala
trait Node[+B] {
  def prepend(elem: B): Node[B]
}
```

위 trait 정의는 에러가 난다. 그 이유는 prepend는 `Function1[-T, +R]` 타입인데 covariant 클래스인 Node의 제네릭 타입 변수 `+B`와 contravariant한 파라미터의 타입인 `-T`또는 `-B`가 맞지 않기 떄문이다. 이를 해결하기 위해 Lower Type Bound를 사용할 수 있다.

```scala
trait Node[+B] {
  def prepend[U >: B](elem: U): Node[U]
}
```

위 트레이트로 Singly Linked List를 구현하고 테스트를 하는 코드는 아래와 같다.

```scala
case class ListNode[+B](elem: B, next: Node[B]) extends Node[B] {
  def prepend[U >: B](elem: U): ListNode[U] = ListNode(elem, this)
  def elem: B = h
  def next: Node[B] = t
}

case class Nil[+B]() extends Node[B] {
  def prepend[U >: B](elem: U): ListNode[U] = ListNode(elem, this)
}

/* 테스트 */
trait Bird
case class AfricanSwallow() extends Bird
case class EuropeanSwallow() extends Bird


val africanSwallowList= ListNode[AfricanSwallow](AfricanSwallow(), Nil())
val birdList: Node[Bird] = africanSwallowList
birdList.prepend(new EuropeanSwallow)
```

## Inner Class

클래스 내부에 클래스를 선언하는 것을 말한다. 개념 자체는 간단하고 사용하는 방법은 더 간단하다. 간단하게 그래프 클래스를 만든다. `Graph` 클래스 내부에 `Node`라는 Inner Class가 선언되어 있다.

```scala
class Graph {
  class Node {
    var connectedNodes: List[Node] = Nil
    def connectTo(node: Node) {
      if (connectedNodes.find(node.equals).isEmpty) {
        connectedNodes = node :: connectedNodes
      }
    }
  }
  var nodes: List[Node] = Nil
  def newNode: Node = {
    val res = new Node
    nodes = res :: nodes
    res
  }
}

val graph1: Graph = new Graph
val node1: graph1.Node = graph1.newNode
val node2: graph1.Node = graph1.newNode
val node3: graph1.Node = graph1.newNode
node1.connectTo(node2)
node3.connectTo(node1)
```

그러나 서로 다른 `Graph` 인스턴스간 Inner Class끼리는 mix가 되지 않는다. 아래 코드를 보자.

```scala
val graph1: Graph = new Graph
val node1: graph1.Node = graph1.newNode
val node2: graph1.Node = graph1.newNode
node1.connectTo(node2)
val graph2: Graph = new Graph
val node3: graph2.Node = graph2.newNode
// node1: graph1의 Inner class instance
// node3: graph2의 Inner class instance
node1.connectTo(node3)      // ERROR!
```

`node1`은 `graph1`을 통해 생성된 inner class 인스턴스고 `node3`은  `graph2`를 통해 생성된 인스턴스다. 둘 간의 연결 또는 mix는 허용되지 않는다. 만약 이를 가능하게 하려면 `connectTo`의 argument를 `Graph#Node`를 바꾼다. 즉, Inner class 선언을 아래와 같이 바꾸면 된다. 아래와 같이 바꾸고 바로 위의 코드 조각을 실행시키면 정상적으로 수행되는 것을 확인할 수 있다.

```scala
class Graph {
  class Node {
    var connectedNodes: List[Graph#Node] = Nil
    def connectTo(node: Graph#Node) {
      /* same with the code above */
    }
  }
  /* same with the code above */
}
```

##  추상 타입(Abstract Type)

`Trait(트레이트)`과 `abstract class(추상클래스)` 추상 타입 멤버를 가질 수 있다. 

```scala
trait Buffer {
  type T // 추상 타입 멤버
  val element: T
}

abstract class SeqBuffer extends Buffer {
  type U // SeqBuffer가 가진 abstract type member
  type T <: Seq[U] // extend한 후 upper bound를 걸어줌
  def length = element.length // T 타입인 element는 Seq의 인터페이스를 가짐
}
```

추상 타입 멤버를 가진 `trait`이나  `abstract class`는 익명 클래스 인스턴스화를 통해 바로 인스턴스를 만들 수 있다.

```scala
abstract class IntSeqBuffer extends SeqBuffer {
  type U = Int
  // 아직 T의 타입이 결정되지 않았기 떄문에 abstract
}

/* IntSeqBuffer의 팩토리 함수 */
def newIntSeqBuf(elem1: Int, elem2: Int): IntSeqBuffer =
  // abstract class인 IntSeqBuffer의 클래스 정의 및 바로 인스턴스화 
  new IntSeqBuffer {
       type T = List[U] // 타입 T <: Seq[Int] 를 결정
       val element = List(elem1, elem2) // element: T 결정
     }

val buf = newIntSeqBuf(7, 8)

println("length = " + buf.length)
println("content = " + buf.element)
```

추상 타입 멤버를 사용하지 않고 제네릭 클래스의 타입 파라미터 형태로도 사용할 수 있다.
위의 코드 조각과 정확하게 동일하게 동작한다.
내부적으로도 동일하게 동작하는지는 잘 모르겠다.
(Scala의 장점이라고 하는 다양한 표현 방식은 심히 피곤하게 만든다..) 

```scala
abstract class Buffer[+T] {
  // type T은 Buffer[+T]로 타입 파라미터로 바뀜
  val element: T
}
abstract class SeqBuffer[U, +T <: Seq[U]] extends Buffer[T] {
  // type T <: Seq[U], type U는
  // SeqBuffer[U, +T <: Seq[U]]로 타입 파라미터로 바뀜
  def length = element.length
}

def newIntSeqBuf(e1: Int, e2: Int): SeqBuffer[Int, Seq[Int]] =
  // 타입 결정은 SeqBuffer[Int, List[Int]] 형식으로
  new SeqBuffer[Int, List[Int]] {
    val element = List(e1, e2)
  }

val buf = newIntSeqBuf(7, 8)
println("length = " + buf.length)
println("content = " + buf.element)
```

## Compound Type

`Compouynd Type`을 대체할 적절한 한국어 단어가 생각이 안나서 그대로 써야할 것 같다. 
여러 `trait`를 조합하여 새로운 `trait`나 `class`를 만드는 것 같은데 자바 인터페이스와의 차이점을 잘 모르겠다. 
자바 인터페이스에 비해 더 높은 수준의 flexibility를 제공하면서 비슷한 역할을 하는 것 같다.

예시가 이해가 빠르므로 바로 예제 코드로 넘어간다.

아래와 같이 `Cloneable` 트레이트와 `Resetable` 트레이트가 있다고 하자.

```scala
trait Cloneable extends java.lang.Cloneable {
  override def clone(): Cloneable = {
    super.clone().asInstanceOf[Cloneable]
  }
}

trait Resetable {
  def reset: Unit
}
```

이 트레이트로 `cloneAndReset` 함수를 만든다.

```scala
def cloneAndReset(obj: ?): Cloneable = {
  val cloned = obj.clone()
  obj.reset
  cloned
}
```

이 때 `obj` 파라미터의 타입은 뭐가 될까? `clone()` 메소드를 가지면서 `reset()` 메소드를
가져야 하므로 두 trait를 모두 가지고 있는 타입이어야 한다. 스칼라는 이를 `Clonable with Resetable`이라는 타입으로 표현한다. 위 함수를 타입과 함꼐 제대로 정의하면 다음과 같다.

```scala
def cloneAndReset(obj: Cloneable with Resetable): Cloneable = {
  /* same as the code snippet above */
}
```

## Self 타입

트레이드간 종속성을 정의하는 데 사용하는 것 같다. 본문의 설명이 더 정확하므로 원본 출처의 내용으로 갈음한다.

> Self-types are a way to declare that a trait must be mixed into another trait, even though it doesn’t directly extend it. That makes the members of the dependency available without imports.

바로 예시를 보자

```scala
trait User {
  def username: String
}

trait Tweeter {
  this: User =>  // this에 재할당, User의 변수에 접근 가능
  /* User 트레이트의 username 멤버 변수 접근 */
  def tweet(tweetText: String) = println(s"$username: $tweetText")
}
/* Tweeter 트레이트는 User 트레이트를 필요로 하기 때문에 믹스인한다.*/
class VerifiedTweeter(val username_ : String) extends Tweeter with User {
	def username = s"real $username_"
}

val realBeyoncé = new VerifiedTweeter("Beyoncé")
realBeyoncé.tweet("Just spilled my glass of lemonade")  
/* 정상적으로 "real Beyoncé: Just spilled my glass of lemonade"가 출력된다. */
```