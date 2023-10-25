# Classes, Abstract Classes and Traits

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
    
