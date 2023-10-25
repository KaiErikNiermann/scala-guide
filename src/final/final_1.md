# Practice Exam 1

---

Value of `l` 

```scala
abstract class Dog {
	def barkies : String = woof + woof
	def woof : String = "woof"
}
class GreatDane() extends Dog {
	override def woof : String = "WOOF"
}

val l : String = {
	val d : Dog = new GreatDane()
	d.barkies
}
```

Saying `D` is of type dog has no impact on the fact that you are still instantiating it as the `GreatDane()` class, which means the call to `d.barkies` is still going to use the overridden value of `"WOOF"` instead of `“woof”`

---

 Value of `l` 

```scala
val l = List(1, 2, 3).foldRight(2)(_ - _)
```

We start with 2 and then cumulatively go from right to left so 

```scala
1 - (2 - (3 - 2)) = 1 - (2 - 1) = 0
```

---

Value of `y` 

```scala
def tupleFun[A, B, C](f: A => B, g: A => C): A => (B, C) = {
	(a: A) => (f(a), g(a))
}

val y = {
	val f = tupleFun[Int, Int, Int](_ + 1, _ * 3)
	f(3)
}
```

`tupleFun` is a function which returns a function which returns a tuple of type `(B, C)` . We know 

→ `f(a) = a + 1 => a = 3 = 4`

→ `g(a) = a * 3 => a = 3 = 9`

From this clearly the tuple of type `(B, C)` (the value of `y`) is `(4, 9)`

---

Value of `x`

```scala
def times(x: Int)(y: Int): Int = x * y

val x : Int = {
	val t3 : Int => Int = times(3)
	t3(2) + t3(1)
}
```

`times(3)(_)` is equivalent to the function `(y: Int) = 3 * y` so `t3(2) + t3(1)` are just 
→ `(3 * 2) + (3 * 1) = 9`

⇒ `x = 9`

---

Most specific type for `T`

```scala
class A 
class B extends A
class C extends A
class D extends C

def bla: Unit = {
	var x : T = new C
	x = new D()
	x = new C()
}
```

Best thing is to always make a tree 

- A
    - B
    - C
        - D

Since C is the least upper bound type and upper bounds are inclusive its clear that `C` is the most specific type.

---

Value of `x`

```scala
val x = Int {
	var calc : Int => Int = x => x * 3

	def upcalc(x: Int): Int = {
		val res = calc(x)
		calc = _ + 3
		res
	}
	upcalc(2) + upcalc(1)
}
```

We never create a definition that would shadow `calc` thus the reassignment modifies it. Which in turn means the value of `x` is 

→ `(2 * 3) + (1 + 3) = 10`

---

Function type

```scala
l => l.map(_.sum).sum
```

`.sum` can only operate on numeric lists, same applies to `map` , it also only returns `Int`. Should be pretty trivial that the type here is `List[List[Int]] => Int`

---

Value of `Test`

```scala
class A() {
	def x : Any = "bla"
}
class B() extends A {
	override def x = 3 
}
class C() {
	def z : A = new A()
}
class D() extends C {
	override def z = new B()
}
val Test : String = {
	val v : C = new D()
	val w : C = new C()
	v.z.x.toString + w.z.x.toString
}
```

`v` is an instance of class `D` which extends `C` this means it has the members 

- z = B
    - B in turn extends A and has the members
    - x = 3
    
    ⇒ `v.z.x.toString = "3"`
    

`w` is an instance of class `C` it has the member 

- z = A
    - A has the member
    - x = bla
    
    ⇒ `w.z.x.toString = "bla"`
    

⇒ value of `Test` ⇒ `“3” + "bla" = "3bla"`

---

Most specific type `T` 

```scala
abstract class Dog {
	def woof : String
}
class GreatDane() extends Dog {
	def woof = "WOOF"
}
class Poodle() extends Dog {
	def woof = "woof"
}

val l : T = List((new GreatDane(), "bla"), (new Poodle(), 3))
```

We can already say that `T` is clearly a `List[(T1, T2)]` , the most specific type for `T1` is the least upper bound type for `GreatDane` and `Poodle` which is clearly `Dog` , and the most specific type for `T2` is the least upper bound of `Int` and `String` which would be `Any` , thus making the most specific type of `T` = `List[(Dog, Any)]`

---

Final value 

```scala
def isEven(x: Int): Boolean = x % 2 == 0
def id[A](x: A): A = x 

List(1, 2, 3, 4, 5, 6).map(isEven).filter(id)
```

Bit of a sneaky question…

⚠️ ~ **********************Remember :********************** Filter only includes the elements for which the predicate function returned true.

In our case after `.map(isEven)` we end up with `List(false, true, false, true, false, true)` , our function is just an identity function (in this case) of type `Boolean => Boolean` , *the function returns `true` when its passed `true` .* From this its hopefully that it only includes all `true` elements, making the final value `List(true, true, true)`

---

> Correct equivalence
> 

```scala
// given 
l.filter(f).filter(g)

// two options 
// A :
l.filter(x => f(x) && g(x))
// B: 
l.filter(g).filter(x)
```

All filter operations are associative, meaning no matter in which order you apply the filter operations you end up with the same final values. 

---

Most specific type for `T`

```scala
abstract class Dog {
	def woof : String
}
class GreatDane() extends Dog {
	def woof = "WOOF"
}
class Poodle extends Dog {
	def woof = "woof"
}
def woofies : String = {
	var t: T = (new GreatDane(), new Poodle())
	t = (t._1, t._2)
	t._1.woof + t._2.woof
}
```

Basically just a repeat of an earlier question, its `(Dog, Dog)`