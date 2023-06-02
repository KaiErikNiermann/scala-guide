# Polymorphism, common higher order functions

## class, abstract class and traits

### class

```scala
class basic(var x : Int) {
	// class constructor	
	def this() = this(0)
}
```

Classes can contain *********members********* such as

- variables
- methods
- values

************Notes ~************ ⚠️

→ class members are public by default, use `private` keyword to hide them

→ You can use `this(0)` to provide a default value of `0` or you can simply assign the variable in the constructor for it to have a default value

### class inheritance

```scala
// superclass
class animal {
	def speak() = 
		println("I am an animal")
}

// subclass
class dog extends animal {
	override def speak() = 
		println("I am a dog")
}
```

********************key terms******************** 

| abstract | concrete |
| --- | --- |
| Something which has yet to be implemented and cannot be directly instantiated  | Something which has been implemented and can thus be directly instantiated |

`override` **keyword** 

→ Used to override (take over) methods from a superclass 

Note ~ ****Not**** necessary when you want to override an abstract method  

**************`super` keyword**

> Used to invoke methods already defined in the *************parent/super class*************
> 

```scala
class FourLeggedAnimal {
    def walk { println("I'm walking") }
    def run { println("I'm running") }
}

class Dog extends FourLeggedAnimal {
    def walkThenRun {
        super.walk
        super.run
    }
}
```

### case class

> Classes defined by the `case` keyword which are good for modeling immutable data.
> 

```scala
case class Book(isbn: String) // isbn is a val AKA immutable
val frankenstein = Book("978-0486282114")
```

********************************Important notes********************************

→ ⚠️ case class parameters are `val` by default 

→ ⚠️ comparing in case classes does not work by reference but by value

```scala
case class Message(sender: String, recipient: String, body: String)

val message2 = Message("jorge@catalonia.es", "guillaume@quebec.ca", "Com va?")
val message3 = Message("jorge@catalonia.es", "guillaume@quebec.ca", "Com va?")
val messagesAreTheSame = message2 == message3  // true
```

### abstract class

```scala
abstract class Pet(name: String) {
	// abstract methods
  def greeting: String  
  def age: Int
  override def toString = s"My name is $name, I say $greeting, and I’m $age"
}

class Dog(name: String, var age: Int) extends Pet(name) {
	// concrete implementation of abstract method
  val greeting = "Woof" // Note ~ does not need `override` keyword
}

val d = new Dog("Fido", 1)
```

**************************************************************************************methods to implementing something abstract**************************************************************************************

**************************passing to extended class************************** 

```scala
class Dog(name: String ... extends Pet(name)
```

*******************************declaring in class argument list*******************************

```scala
class Dog(name: String, var age: Int) // var age is a concrete implementation of def age
```

**************************************declaring something in the class body**************************************

```scala
... {
	val greeting = "Woof" // concrete implementation of def greeting
} 
```

⚠️ A subclass of an abstract class ************has************ to implement all abstract methods

****************************************************notes on abstract classes****************************************************

→ Abstract classes can inherit from other (abstract or non-abstract) classes

```scala
abstract class Animal {
	def animalID: String
}

abstract class Pet(name: String) 
	extends Animal {
	def greeting: String
	def age: Int
}
```

```scala
class Animal {
	def animalID: String = "1234"
}

abstract class Pet(name: String) 
	extends Animal {
	def greeting: String
	def age: Int
}
```

→ Abstract classes *********************cannot********************* be instantiated

```scala
abstract class Animal {
	def animaID: String
}

val dog : Animal = Animal() // invalid
```

### example #1 ~ override

```scala
abstract class A {
	def x: String = y + y + "!"
	def y: String = "_"
}

class B extends A {
	override def x: String = y + super.x
	override def y: String = "b"
}

class C extends A {
	override def y: String = "c"
}

object Test {
	def printA(a: A) = 
		print(a.x)
		print(a.y)

	def test : Unit = {
		printA(new B)
		printA(new C)
	}	
}
Test.test
```

- Two main calls from `test`
    
    ```scala
    printA(new B)
    	a.x = y + super.x = "b" + ( "b" + "b" + "!" )
    	// y = "b" not "_" as it was overriden, even though we are using super.x  
    	a.y = "b"
    	out -> "bbb!b"...
    
    printA(new C)
    	a.x = "c" + "c" + "!"
    	// y = "c" not "_" for the same reason as previous
    	a.y = "c"
    	out -> "cc!c"
    
    full out -> "bbb!bcc!c"
    ```
    

⚠️ **************Note ~************** If a method is overridden it will be used, even in the case where you are calling a parent’s class method.   ****************************

### example #2 ~ override

```scala
class A {
	def x: String = y + "_" + y
	def y: String = "y"
}

class B extends A {
	override def x: String = y + super.x
	override def y: String = ">"
}

class C extends B {
	override def x: String = y + super.x
	override def y: String = "Y"
}

object Test {
	def test : Unit = {
		println(new C().x)
	}
}
Test.test
```

- A bit easier, just a single print call to evaluate
    
    ```scala
    new C().x
    	x = C.y + (C.y + (C.y + "_" + C.y))
    		= "Y" + "Y" + "Y" + "_" + "Y"
    
    full out -> "YYY_Y" 
    ```
    
    Note ~ its basically just testing the same concept as above, that the most local `override` method is the one that takes precedence, even across multiple inheritances 
    

### example #3 ~ override

```scala
class A {
	def f(x: Int): Int = x - 1
}

class B extends A {
	override def f(x: Int): Int = x * 2
}

class C extends B {
	override def f(x: Int): Int = x + 3
}

class D extends C {
	override def f(x: Int): Int = x - 4
}

object Test {
	val x: List[Int] = {
		val l: List[A] = List(new B, new C, new D)
		for(e <- l) yield e.f(5)
	}
}
```

- A bit weird looking but easy enough
    
    ```scala
    x[0] = B.f(5) => 5 * 2 = 10
    
    x[1] = C.f(5) => 5 + 3 = 8
    
    x[2] = D.f(5) => 5 - 4 = 1 
    
    output -> List(10, 8, 1)
    ```
    

---

## functions and classes

### example #1 ~ function overloading

```scala
class Vehicle
class Car extends Vehicle
class Porsche extends Car

object Test {
	def g(v: Any) = "Any"
	def g(v: Vehicle) = "Vehicle"
	def g(v: Car) = "Car"
	def g(v: Porsche) = "Porsche"

	def x : Unit = {
		val v1 : Any = new Porsche
		val v2 : Porsche = new Porsche
		println(g(v1) + ", " + g(v2))
	}
}
Test.x

output -> "Any, Porsche"
```

********************Concept ~******************** In scala when it comes to type matching for overloaded functions we **match with the most specific type** which is why 

- v1 : Any == Any
- v2 : Porsche == Porsche

If we were to for example omit `g(v: Porsche)` the most specific type would be `g(v: Car)` which in turn would make the output 

`Any, Car` (`v1: Any, v2: Porsche` ) 

As the Porsche type would match with the most specific (closest) type out of the given functions, this being `Car`

### example #2 ~ just fizzbuzz for some reason

```scala
def isMultipleof(n: Int, m: Int): Boolean = n % m == 0

def fizzString(n: Int): String = {
	if (isMultipleof(n, 3) && isMultipleof(n, 5)) "fb"
	else if (isMultipleof(n, 3)) "f"
	else if (isMultipleof(n, 5)) "b"
	else n.toString
}

def fizzies : Seq[String] =
	(3 until 15).map(_ - 3).map(fizzString)

fizzies.foreach(print)
```

```scala
(3 until 15) == [3, 14]
	.map(_ - 3) == "subtract 3 from each element in the array"
[3, 14] == [0, 11] 
	.map(fizzString) 
0 -> fb
1 -> 1
2 -> 2
3 -> f
4 -> 4
5 -> b
6 -> f
7 -> 7
8 -> 8
9 -> f
10 -> b
11 -> 11

output -> fb 1 2 f 4 b f 7 8 f b 11
```

### example #3 ~ higher order functions and types

```scala
val f = l => l.flatMap(_.map(_ + 2).sum).filter(_ != '5')
```

1. What do all the higher order functions take in and what do they spit out 

| function | purpose | takes | returns |
| --- | --- | --- | --- |
| flatMap | applies some function to all elements in a collection, then flattens this collection in other words, it “de-nests” it. | Function : A | new collection of some primitive type e.g. Seq[Int] |
| map | applies some function to all elements in a collection | Function : A | Some new collection of type A |
| sum | returns sum of all elements in specific types of collections, e.g. Lists | N.A. | Int representing sum of values |
| filter | used to select all elements which match some predicate (Boolean condition) | boolean predicate e.g. _ > 5  | Collection of all elements matching the predicate |
1. Now that we understand that, lets break the statement apart

```scala
l.flatMap(
	.map(_ + 2)            // adding 2 to each element or element of a subcollection
	).sum                  // summing subcollections 
	).filter( _ != 5)      // returning a list of elements not equal to 5
```

****************************************************assumptions from the code**************************************************** 

1. `flatMap` returns some collection of whatever type the inner function yields in the end
2. `.map` is adding to the elements so the inner collection must have a numeric type
3. `.sum` *returns a numeric type*, AKA `Int` and can *only operate on collections*
4. from this we can determine that we are calling filter on some collection of type `Int` 
5. `filter` has no effect on the type of the collection

******************************Conclusion ⇒****************************** From this we can see that f is a function of type 

```scala
List[List[Int]] => List[Int]
```

*From (b.) we know that the inner collection has a type `Int` so we determined the input is of type `List[List[Int]]` and since `.sum` returns `Int` and we are running `.flatMap` it should be clear that the resulting return type of the function is `List[Int]`* 

Note ~ I don’t actually know how to determine the collection, but the questions only have `List` anyways so I think in this case we are just going in assuming the collections are Lists

---

## `final` keyword

> Used in many different contexts generally to **prevent overriding or reassigning**, a bit like `const` in other languages.
> 

### classes

```scala
final class A 

class B extends A // ! not allowed
// not allowed in any context
```

### methods / functions

```scala
class Base {
	final def foo() : Unit = { }
}

class Dervied extends Base {
	// error: method foo cannot be overridden
	override def foo() : Unit = { } 
}
```

### variables

```scala
final val x = 10
// x = 20  // Error: cannot reassign a value to a final variable
```

### code blocks

```scala
val x = 10

final {
  val y = 20
	// Can access x because it is defined outside the final block
  println(x)  
  // x = 30   // Error: cannot reassign a value to x because outside final block
}
```

---

## bringing it all together

### example #1

```scala
trait Iterator[+A] {
	def hasNext: Boolean
	def next(): A
}

trait Iterable[+A] { def iterator: Iterator[A] }

class RangeIterator(var cur : Int, val step : Int, val end : Int) extends Iterator[Int] {
	def hasNext = cur < end
	def next() = {
		val res = cur
		cur += step
		res
	}
}

case class MyRange(start : Int, step : Int, end : Int) extends Iterable[Int] {
	override def iterator : Iterator[Int] = new RangeIterator(start, step, end)
}

object Test {
	def test : Unit = {
		val r = MyRange(-2, 2, 10)
		val it1 = r.iterator
		val it2 = r.iterator
		print(it1.next()); print(it2.next()); print(it1.next())
		print(it2.next()); print(it2.next()); print(it1.next())
	}
}
Test.test
```

1. For now we gonna ignore the type related shit and just focus on the `myRange` declaration
    
    ```scala
    it1, it2
    	curr = -2
    	step = 2
    	end = 10
    
    it1.next() = -2 
    	res = -2, curr = 0 -> return -2
    it2.next() = -2
    	res = -2, curr = 0 -> return -2 
    it1.next() = 0
    	res = 0, curr = -2 -> return 0
    it2.next() = 0
    	res = 0, curr = -2 -> return 0
    it2.next() = 2
    	...
    it1.next() = 2 
    ```
    
    ************************explanation************************
    
    > Each new assignment to `r.iterator` is a ********new******** class instance of `RangeIterator` from there it becomes a pretty trivial act of just calling the functions for either class instance and modifying the values accordingly making sure to not misread the very painful to look at code.
    > 

---

## Additional

### Common higher order function properties

| function | works on  | applies | returns |
| --- | --- | --- | --- |
| exists | List[A] | A => Boolean | Boolean  |
| map | List[A] | A => B | List[B] |
| flatMap | List[A] | Int => IterableOnce[B] | List[B] |
| foldLeft / foldRight | List[A] | z: B , (B, A) => B | B |
| filter | List[A] | A => Boolean  | List[A] |
| forall | List[A] | A => Boolean | Boolean |