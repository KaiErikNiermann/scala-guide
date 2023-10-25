# Advanced Types

---

### type basics

**type parameter `[A]`**

> We can pass types as parameters to make methods or classes more flexible
> 

```jsx
// here we declare the type parameter A
//          v
class Stack[A]:
  private var elements: List[A] = Nil
  //                         ^
  //  Here we refer to the type parameter
  //          v
  def push(x: A): Unit =
    elements = elements.prepended(x)
  def peek: A = elements.head
  def pop(): A =
    val currentTop = peek
    elements = elements.tail
    currentTop

// creating an Int stack
val stack = Stack[Int]
stack.push(1)
stack.pop() // yields 1  
```

**union types** 

> You can use the `|` ( union ) operator to denote that something accepts more than one type
> 

```scala
case class Username(name: String)
case class Password(hash: Hash)

def help(id: Username | Password) = // id can be of type `Username` or `Password`
  val user = id match
    case Username(name) => lookupName(name)
    case Password(hash) => lookupPassword(hash)
  // more code here ...
```

---

### type variance `[A]`, `[+A]` , `[-A]`

**Invariance**

> Subtyping relationships between type parameters **not reflected in parameterized type**.
> 

```scala
class Box[A](var content: A)

abstract class Animal { def name: String }
	
case class Cat(name: String) extends Animal // Cat subtype of Animal
case class Dog(name: String) extends Animal // Dog subtype of Animal

val myAnimal: Animal = Cat("Felix") // valid as Cat is a subtype of A

val myCatBox: Box[Cat] = new Box[Cat](Cat("Felix"))
val myAnimalBox: Box[Animal] = myCatBox // does not compile, why ? 
```

> `Animal` in this case is the *parameterized type* of `Box` , and by the definition of invariance we know that subtype relationships; in this case Cat being a subtype of Animal; are not reflected in parameterized types. Which in turn means while …
> 

```scala
Animal = Cat // is valid 
```

```scala
Box[Animal] = Box[Cat] // is not valid
```

**Covariance**

> Subtyping relationship between type parameters **is reflected in parameterized type.**
> 

```scala
class Box[+A](val content: A)
...
val myCatBox: Box[Cat] = new Box[Cat](Cat("Felix"))
val myAnimalBox: Box[Animal] = myCatBox 
println(myAnimalBox.content.name) // prints "Felix"
```

> Because we now denoted the type parameter `+A` as being **covariant** we can now do
> 

```scala
Box[Animal] = Box[Cat] // now valid !
```

This allows us to do what invariance couldn’t, with the subtyping relationship now also being reflected in the **parameterized type**

⚠️ ~ **Note** : Lists are implemented as `List[+A]` NOT `List[A]` , to allow for things like 

```scala
val animalList : List[Animal] = List( Cat("Felix") )
```

**Contravariance**

> The reverse of covariance if `A` is a subtype of `B` , then `Contra[B]` is a subtype of `Contra[A]` .
> 

```scala
...
abstract class Serializer[-A] {
  def serialize(a: A): String
}

val animalSerializer: Serializer[Animal] = new Serializer[Animal] {
  def serialize(animal: Animal): String = s"""{ "name": "${animal.name}" }"""
}
val catSerializer: Serializer[Cat] = animalSerializer
catSerializer.serialize(Cat("Felix"))
```

> In this case, even though `Cat` is a subtype of `Animal` we can assign `Serializer[Cat]` to `Serializer[Animal]` . Or expression more generally we can assign something of a subtype to something of a more general type.
> 

```scala
Box[Cat] = Box[Animal] // valid 
```

Using the example above this would now be a valid operation 

---

### Functions and Types

> Functions can take an argument of some type and return an argument of another type. But there are some rules in scala. Assuming a generic function `def func(x: A): B` with `A` and `B` ; `func` can only be of type
> 

```scala
class superAB
class A extends superAB
class subA extends A

class B extends superAB
class subB extends B
```

⚠️ ~ The general rule here is that the **input type can never be a supertype** and the **output type can never be a subtype** (… of some original type `A` and `B` )

```scala
// same => same 
(x: A): B == A => B
// same => sub
(x: A): subB == A => subB 
// super => same
(x: superAB): B == superAB => B
// super => sub
(x: superAB): subB == superAB => subB
// Any => sub/same is also valid
```

### variance with function types

**Invariant**

```scala
Box[A => B] == Box[A => B] // valid anything else invalid
```

**Covariant**

```scala
Box[A => B] == Box[superAB => subAB]
```

**Contravariant**

```scala
Box[A => B] == Box[subA => superAB]
```

---

### Type Bounds

setup code

```scala
class Alphabet
class A extends Alphabet 
class B extends A 
class C extends B 
class D extends C
```

**Upper Type Bounds**

> Upper type bound `L <: B` declares that type variable `L` refers to a subtype of type `B` (inclusive of `B`)
> 

![Untitled](Advanced%20Types%20e0c661dbf707408eb45505f1ada44905/Untitled.png)

```scala
class DtoB[L <: B](l: L) { def letter: L = l }

val bar : DtoB[A] = new DtoB(new A) // error ! 
val foo : DtoB[B] = new DtoB(new B) // valid 
val baz : DtoB[C] = new DtoB(new C) // valid
val qux : DtoB[D] = new DtoB(new D) // valid
```

**Lower Type Bounds** 

> Lower type bound `L >: C` expresses that the type `L` refers to a supertype of `C` (inclusive of `C`)
> 

```scala
class AtoC[L >: C](l: L) { def letter: L = l }

val bar2 : AtoC[A] = new AtoC(new A) // valid
val foo2 : AtoC[B] = new AtoC(new B) // valid
val baz2 : AtoC[C] = new AtoC(new C) // valid
val qux2 : AtoC[D] = new AtoC(new D) // error !
```

---

### The wonderfully confusing world of `with`

> The basic idea of the `with` keyword is that you in a way *******absorb******* traits into a class as a form of like horizontal inheritance.
> 

```scala
trait LayEggs
trait CanFly

class Animal
class Bird extends Animal with LayEggs with CanFly
class Pigeon extends Animal with LayEggs with CanFly

```

In this case now `Bird` will have access to all the members of the two traits and will be a subtype of of the `Animal` class. Same concept with Pigeon. 

The general format for extending classes with traits is … 

```scala
class A
class subA extends A with A1 with A2 with A3 // A1 - A3 are traits
```

⚠️ ~ ***Traits act as supertypes*** 

```scala
class A
class subA extends A with A1 with A2 
class subB extends A with A1 
class subC extends A with A2 

val l : List[A1] = List(new subA, new subB)

val l2 : List[A1 with A2] = List(new subA)

val l3 : List[A2] = List(new subA, new sub C)
```

---

### example #1 ~ type hierarchies

- Which functions can be used as arguments for `f`

```scala
class Animal 
class Marsupial extends Animal
class Wombat extends Marsupial
class CatLike extends Animal 
class Cat extends CatLike
class Lion extends CatLike 

object Test {
	def transformEm(f: CatLike => Marsupial,
					animals: Seq[CatLike]): Seq[Marsupial] =
		animals.map(f)
}
```

![Untitled](Advanced%20Types%20e0c661dbf707408eb45505f1ada44905/Untitled%201.png)

Possible types for `f` 

→ `CatLike => Wombat/Marsupial`  

→ `Animal => Wombat/Marsupial`

→ `Any => Wombat/Marsupial`

❗~ Any is a supertype of everything so it can also be used as an input for a function.

### example #2 ~ most specific type

- Most specific type of `x`

```scala
abstract class Animal 
abstract class Mammal extends Animal 
class Mouse extends Mammal
class Dog extends Mammal
class Cat extends Mammal

object Test {
	def aId[A](x: A): A = x
	def bId[A <: Animal](x: A): A = x
	def cId[A <: Mammal](x: A): A = x

	val x = List(aId(new Mouse), bId[Animal](new Dog), cId(new Cat))
}
```

> for `aId` and `cId` the types are not specified so they would be inferred. The three types in the list are …
→ `List : Mouse , Animal , Cat`

The earliest common supertype in the tree for all three types is `Animal` thus the most specific type would be `List[Animal]`
> 

### example #3 ~ subtypes

- Subtypes of `Seq[Animal]`

```scala
class Animal
class Mammal extends Animal
class Dog extends Mammal
```

**Option 1 ~** ❌

```scala
Seq[Any]
```

Any is a supertype of class types, not a subtype, so this would be false.

**Option 2 ~** ✅

```scala
Seq[Dog]
```

`Dog` is a subtype of `Animal` and since Sequences are implemented via Lists in Scala, in other words their type parameters are covariant`Seq[Dog]` would be a subtype of `Seq[Animal]`

**Option 3 and 4**

```scala
Seq[Nothing] / Nothing
```

`Nothing` is a subtype of all other types, and since Sequences are covariant both of these are valid subtypes of `Seq[Animal]`

### example #4 ~ subtypes of a function type

> Subtype of function type `Cat => Animal`
> 
> 
> ⚠️ ~ **Remember** : The first part can’t be a subtype and the second part can’t be a super type
> 

Note ~ By *subtype of a function type* they seem to just mean, all other variants of the type excluding ones with the same types as the original. 

```scala
class Animal 
class Dinosaur extends Animal
class Brontosaurus extends Dinosaur
class Mammal extends Animal
class Cat extends Mammal
class Dog extends Mammal
```

All subtypes of the function type are 
→ `Animal => Dinosaur` 

→ `Any => Nothing`

→ `Mammal => Brontosaurus`

→ `Animal => Dog`

→ etc. *see full table of `super ⇒ sub` below

**list of all subtypes and super-types in the context of functions**

|  | Cat  | Animal |
| --- | --- | --- |
| subtypes | N.A. | Dinosaur, Mammal, Cat, Dog, Nothing, Brontosaurus |
| super-types | Mammal, Animal, Any | N.A. |

> To generalize here, all **subtypes of a function type** `A => B` referres to the set of 
`{super types of A} => {subtypes of B}`
> 

### example #5 ~ using the `with` keyword

> What can the type of `A` be ?
> 

```scala
trait LaysEggs 
trait CanFly

class Animal
class Bird extends Animal with LaysEggs with CanFly
class Mammal extends Animal
class Platypus extends Mammal with LaysEggs
class Bat extends Mammal with CanFly
class Pigeon extends Animal with LaysEggs with CanFly

object Test {
	val l : List[A] = List(new Bird, new Bat, new Pigeon)
	
	// other examples
	val l2 : List[Mammal with LaysEggs] = List(new Platypus)
	val l3 : List[CanFly with LaysEggs] = List(new Bird, new Pigeon)
	val l4 : List[Mammal with CanFly] = List(new Bat)
}
```

We have various different options. Beyond the obvious of like `Animal` it should be clear that they all have the `CanFly` trait in common 

⇒ we can also use `CanFly` , this would be the most specific type we could apply to that group

⇒ We can also use `List[Any] / Any` 

![Untitled](Advanced%20Types%20e0c661dbf707408eb45505f1ada44905/Untitled%202.png)

### example #6 ~ most specific type

> Most specific type of `t`
> 

```scala
class Fruit 
class Apple extends Fruit
class Pear extends Fruit

object Test {
	def id(x: Any): Any = x
	def gid[A](x: A): A = x
	def gffid[A <: Fruit](x: A): Fruit = x
	def gfid[A <: Fruit](x: A): A = x
	def fid(x: Fruit): Fruit = x

	val t = 
		(gffid(new Apple), fid(new Pear),
			gfid(new Apple), id(new Pear),
			gid(new Apple)) 
}
```

Ok wow this looks cursed but lets break it down one by one. First of all `t` is a Tuple5 so each variable can have different types, so we are finding the most specific type for the **individual variables**. 

```scala
gffid(new Apple) -> Fruit // A <: Fruit => Fruit 
fid(new Pear) -> Fruit    // Fruit => Fruit
gfid(new Apple) -> Apple  // A <: Fruit => A
id(new Pear) -> Any       // Any => Any
gid(new Apple) -> Apple   // A => A
```

Ok I’m pretty sure this is just a trick question 

### example #7 ~ most specific type

> Most specific return type of `b`
> 

```scala
trait Feathered
trait LongNeck

abstract class Animal
abstract class Mammal extends Animal
abstract class Dinosaur extends Animal 
class Velociraptor extends Dinosaur with Feathered 
class ApatoSaurus extends Dinosaur with LongNeck
class Giraffe extends Mammal with LongNeck
class Raccoon extends Mammal
class Ostrich extends Animal with Feathered

object Test {
	def b(c: Boolean) = 
		if(c) (new ApatoSaurus, List(new Racoon, new Giraffe))
		else (new Giraffe, List(new Ostrich))
}
```

As we have two tuples that could be returned me must consider both for finding the most specific type

```scala
(new ApatoSaurus, List(new Racoon, new Giraffe)) and (new Giraffe, List(new Ostrich))
```

Easiest is to always draw iz

![Untitled](Advanced%20Types%20e0c661dbf707408eb45505f1ada44905/Untitled%203.png)

1. From this its clear that for the first index of the tuple the common type between `ApatoSaurus` and `Giraffe` is `Animal with LongNeck` 
2. For the second part we need to decompose it further 
    1. `List(new Racoon, new Giraffe)` has a common type of `Mammal` 
    2. `List(new Ostrich)` is just `Ostrich` 
    
    ⇒ Finally between `Mammal` and `Ostrich` the common is clearly `Animal` 
    

⇒ From this we can conclude the most specific return type is `(Animal with LongNeck, List[Animal])`

### example #8 ~ find subtypes

> Find the subtypes of
> 

```scala
(List[Mammal], Printer[Mammal], MutableContainer[Mammal])
```

```scala
class List[+A]
class Printer[-A]
class MutableContainer[A]

class Animal
class Mammal extends Animal
class Hamster extends Mammal
class Bat extends Mammal
```

`List[Mammal]`

> List is covariant, meaning all subtypes of `A` are also subtypes of `List[A]` . In our case `A` is `Mammal` which has the subtypes  `Hamster` and `Bat` , which in turn means the valid subtypes here are 
→ `List[Hamster]`
→ `List[Bat]`
> 

`Printer[Mammal]`

> Printer is contravariant, so subtypes act in the reverse nature, in other words, if `A` is a supertype of `B` then `Printer[A]` is a subtype of `Printer[B]` . In our case the only supertypes of `Mammal` are `Animal` . So the only subtypes of `Printer[Mammal]` will be 
→ `Printer[Animal]`
→ `Printer[Any]`
> 

`MutableContainer[Mammal]`

> Remember that for the case of invariance any subtyping relationship of the parameterized variable is not reflected, the only valid subtype would be 
→ `Nothing`
> 

⚠️ ~ Don’t forget about `Nothing` and `Any` in these types of questions, `Nothing` is a subtype to everything and `Any` is a supertype to anything.