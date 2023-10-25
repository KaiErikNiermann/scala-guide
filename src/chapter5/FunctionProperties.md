# Function properties

## Function Properties

### tail recursive

> A tail recursive function is just defined by a recursive call being the last part of the function. A basic example using the factorial function.
> 

****************************tail recursive**************************** 

```scala
def factorial(n, result = 1):
    if n == 0:
        return result
    return factorial(n - 1, result * n)
```

************************************NOT tail recursive************************************

```scala
def factorial(n):
    if n == 0:
        return 1
    return n * factorial(n - 1)
```

### sugar and desugar

> ******************Sugar :****************** syntactic features of a programming language that make it more convenient for the programmer to use but don’t actually add any functionality. 
**********example ~********** `x++` == `x = x + 1`
> 

> ******************Desugar****************** : remove these features; makes it harder for programmer to read but easier to execute
**********example ~********** `x++` ⇒ `x = x + 1`
> 

### example #1

Find the desugared form

```scala
def b(l1: List[Int]): List[Int] = {
	val l2 = l1.map(_ - 1)
	for(x <- l1; y <- l2; if (x * y) > 1) 
		yield x * y
}
```

********************************************plain English******************************************** 

1. Filter out all `x` and `y` where `(x * y) > 1`
2. Return the value of `x * y` 
3. function will return list with `[x1 * y1, ..., xn * yn]` where all `xn` and `yn` meet that initial condition 

***********Option 1 ~*********** ❌

```scala
l1.flatMap(x => l2.map(x => x + y).filter(y => x * 1 > 1))
```

> This is just incorrect code, `x` is used but to denote the elements in `flatMap(x => ...` and in `map(x => ...` leaving `y` in `map` undefined.
> 

****Option 2 ~**** ✅

```scala
l1.flatMap(x => l2.filter(y => x * y > 1).map(x + y))
```

> Does exactly what the original loop does. It filters out all elements that meet the condition, then it adds all of these. And finally; since `filter` returns lists, we use `flatMap` to flatten the result into a single array of type `List[Int]` same as the original function returns.
> 

***********Option 3 ~*********** ❌

```scala
l.map(x => l2.map(x => x + y).filter(y => x * y > 1))
```

> Same situation as Option 1, just incorrect code. Also since we aren’t using `flatMap` we would end up with a 2D list due to the use of `filter`
> 

***********Option 4 ~*********** ❌

```scala
l1.map(x => l2.filter(y => x * y > 1).flatMap(y => x + y))
```

> Filter would return a sublist inside of `l1` so applying `flatMap` has no effect since the sublist of values that match the predicate `x * y > 1` is already flat, additionally you cant have your function in `flatMap` return something of type `Int` .
> 

### example #2

> Which of the following statements about the code are true ?
> 

```scala
def f(x: Int, y: Int): Int = {
	var z: Int = y

	def g(acc: Int, y: Int): Int = {
		if (y == 0) return acc
		z += y 
		g(acc, y - 1)
	}

	if (x == 0) {
		g(0, y)
		z
	} else f(x - 1, y)
}
```

`def g(acc: Int, y: Int): Int`

> This function is clearly *tail recursive* since the last thing we do is a recursive call.
> 

`def f(x: Int, y: Int): Int` 

> This function is pure, as it doesn't R/W any state from outside, it doesn’t mutate the input parameters, additionally it doesn’t
> 

---

## cursed ass looking question

### example #1

```scala
sealed class Either[A, B] {
	def combine(f: (A, A) => A)(g: (B, B) => B)
				(rhs: Either[A, B]): Option[Either[A, B]] = 
		(this, rhs) match {
			case (Left(x), Left(y)) => Some(Left(f(x, y)))
			case (Right(x), Right(y)) => Some(Right(g(x, y)))
			case (Left(x), Right(y)) => Some(Right(g(y, y)))
			case _ => None
	}
}

case class Left[A, B](x: A) extends Either[A, B]
case class Right[A, B](x: B) extends Either[A, B]

object Test {
	val m = Left[Double, Double](8.0).combine(_ + _)(_ * _)(Right(2.0))
}

println(Test.m) // Some(Right(4.0))
```

> Looks pretty weird but isn’t too complex once you break it down
> 

```scala
val m = Left[Double, Double](8.0).combine(_ + _)(_ * _)(Right(2.0))
// breaking the parts down
val lhs = Left[Double, Double](8.0)
val rhs = Right[Double, Double](2.0)
val res = lhs.combine(_ + _)(_ * _)(rhs)
	f = _ + _ 
	g = _ * _
	this = Left(8.0)
	rhs = Right(2.0)
	=> (Left(8.0), Right(2.0))

	// match with 
	(Left(x), Right(y)) => Some(Right(g(y, y)))
		g(y, y) = y * y = 2.0 * 2.0 = 4
	
=return> Some(Right(4.0)) 
```

---

## misc question ~ spotting errors

```scala
sealed abstract class Animal {
	def speakTwice : String = speak + speak
	def speak : String
	def name : String
}

case class Parrot(line: String) extends Animal {
	override def speakTwice: String = speak + speak + speak
	def speak : String = "Parrot: " + line
}

case class Dog(line: String) extends Animal {
	def speakTwice: String = super.speakTwice + super.speakTwice
	def speak : String = "Dog: " + line
	final def changeLine(newLine: String) : Unit = {
		line = newLine
	}
}

object Parrot {
	def makeParrt(line: String): Parrot = Parrot(line)
}

object Dog {
	def makeDog(line: String): Dog = Dog(line)
}
```

- Class parameters are `val` by default

```scala
val line = newLine // triggers error
```

- You must always implement all abstract members if you are extending a class who has some

```scala
Parrot => does not implement `name` method
Dog => does not implement `name` method 
```

- Only abstract things can be implicitly overridden by concrete implementations.

```
`speakTwice` is not abstract so all new implementations 
need the override modifier
```