# Practice Exam 2

---

Value of `test` 

```scala
def test: Int = {
	val str = "one"
	val digit = 
		str match {
			case "one" => 1
			case "two" => 2
			case "three" => 3
			case _ => -1
		}
	digit
}
```

Value is one as we are returning `digit` not `str`

---

Output if function

```scala
def foo() : Unit = 
	for(i <- 1 to 5;
		if i % 2 == 0; 
		j <- 1 to i;
		if j > 2)
		println(j)
```

```scala
...
	for {
		i <- 1 to 5    i     = 1, 2, 3, 4, 5
		if i % 2 == 0  i % 2 = x, 0, x, 0, x 
			j <- 1 to i  j     = x, (1, 2), x, (1, 2, 3, 4), x
					if j > 2 j > 2 = x, x, x, (3, 4), x
 		} println(j)         
// 3
// 4
```

---

Most specific type of `p`

```scala
case class Pair[A, B](x: A, y: B)
	
val p = Pair[Char, Int]('a', 9).y
```

Sneaky question, its ********`.y` , so the most specific type would obviously be `Int`** 

---

What is the output 

```scala
def mapEm[A, B](m: Option[A], f: A => B) = 
	m match {
		case Some(x) => Some(f(x))
		case None => None
	}

def add(lhs: Double)(rhs: Int): Double = lhs + rhs

def out() = {
	val n : Option[Int] = Some(3)
	val m : Option[Int] = None
	print(mapEm(n, add(1.3)))
	print(mapEm(m, add(1.3)))
}

out()
```

`add(1.3)` is equivalent to the function `(rhs: Int) ⇒ 1.3 + rhs` . The first call to `mapEm` has `m = Some(3)` which matches with `Some(x)` where `x = 3` , we also know `f` = `1.3 + x` in this instance, which means `Some(f(x)) == Some(4.3)`

The second call has `m = None` which matches with `None` and thus just prints `None` 

⇒ The final output would thus be `Some(4.3)None` 

---

What is the output 

```scala
import scala.collection.mutable.ListBuffer
object testing_stuff extends App {
	def foo() : Unit = {
		var x = ListBuffer(1, 2)
		var y = x
		print(y)
		x = ListBuffer(0) ++ x
		print(y)
		x = x.updated(2, 5)
		print(y)
	}
	foo()
}
```

→ The first print call would output just `List(1,2)`

→ The second and third call would print the same as the reassignment does not assign a reference but a new instance that is copied from the original.

---

Value of `l` 

```scala
class A {
	def x = "fiz"
}
class B extends A {
	override def x = "buz"
}
class C extends B {
	override def x = "boo"
}
val l = {
	val p: B = new C()
	p.x
}
```

Same concept as alot of previous questions, they are trying to throw you off buy declaring an instance of a class with its supertype, but this has no impact on the variables. The override still means that the class instance will chose its overridden variable, so the value is `“boo”`

---

Is `gdc` a pure function 

```scala
def gdc(ia: Int, ib: Int): Int = {
	var a = ia
	var b = ib
	var t = 0
	while(b != 0) {
		t = b
		b = a % b 
		a = t
	}
	a
}
```

→ Output depends only on inputs : ✅

→ Does not R/W data from the outside world : ✅

→ Does not modify any hidden state : ✅

⇒ thus the function is pure

---

Desugared version of loop

```scala
def fors(l: List[List[Int]]) = {
	var s = 0
	for(row <- l; e <- row; if e > 10) s += e
	s
}
```

Desugared version is 

```scala
l.foreach(row => row.filter(_ > 10).foreach(e => s += e))
```

---

Possible type of `T`

```scala
trait Springy { def jump : String }
class A extends Springy { def jump = "weee" }
class B extends Springy { def jump = "wooo" }
case class CompBox[A <: Springy](c : A)

def t = {
	val x : CompBox[A] = CompBox(new A)
	val y : CompBox[B] = CompBox(new B)
	var z : T = x.c
	print(z.jump)
	z = y.c
	print(z.jump)
}
```

As the upper bound is `Springy` and this is also the least upper bound between `A` and `B` the only possible type would be `Springy` . 

---

Type of `f` 

```scala
def isEven : Int => Boolean = (n: Int) => n % 2 == 0

def f = l => l.exists(_.forall(isEven))
```

`l.exists(p)` returns a Boolean, and takes a `List[A]` in this case `_.forall(p)` can only be applied to `List[A]` and as our predicate `p` can only take the type `Int` our input must be `List[List[Int]]`

⇒ which makes the type of `f` : `List[List[Int]] => Boolean`

---

Find which is equivalent to `1:`

```scala
1: l.map(g).map(f)

A: l.map(x => f(g(x)))
B: l.map(x => g(f(x)))
C: l.map(f).map(g)
```

1 is equivalent to `A:` because in 1 we are first applying `g` to all elements then we are applying `f` to the return value of `g` , this is the same idea as the composite function in `A:`. 

---

Most generic type 

```scala
abstract class Animal {
	def sound : String 
}
class Dog extends Animal {
	def sound = "woof"
}
class Cat extends Animal {
	def sound = "meow"
	def pur = "purrr"
}

val x = {
	val l: T = List(new Cat, new Dog)
	l(0).pur 
}
```

This would always yield a type error since there is no type that could include `Cat` but also still have the member `pur` . 

---

Output 

```scala
def lval() = {
	val x = { print("v"); 3 }
	def z = { print("d"); 7 }
	lazy val y = { print("l"); 5 }
	var s = y
	s += y 
	s += z
	s += z 
	s += x
}
lval()
```

→ `val x` is evaluated immediately so we print “v”

→ `def z` is only evaluated upon access 

→ `lazy val y` is only evaluated upon access 

→ `var s = y` leads to us assigning `y` to `s` and then the compiler evaluates `s` printing “l”

→ `s += y` again leads to the compiler evaluating `s` which prints “l”

---

Value of `folds`

```scala
val folds : Int = {
	val z = List(1, 2, 3)
	z.foldLeft(0)(_ - _) + z.foldRight(0)(_ - _)
}

=> ((0 - 1) - 2) - 3 + 1 - (2 - (3 - 0)) = -4
```

output is `-4`

---

Value of `l`

```scala
def comp[A, B, C](f: A => B, g: B => C)(x: A): C = 
	g(f(x))

def h[A, B](f: A => A): A => (A, A) = 
	x => (f(x), comp(f, f)(x))

val l = h(comp[Int, Int, Int](_ + 1, _ * 3))(6)
```

Lets go from the inside out

1. `comp[Int, Int, Int](_ + 1, _ * 3)` gives us the function `(x: Int) => (x + 1) * 3` 
2. so the call to `h` would then give us the function 
    1. `(x: Int) => ((x + 1) * 3, (((x + 1) * 3) + 1) * 3)`
3. Calling the function in 2a with `6` would therefore give us the Tuple `(21, 66)`