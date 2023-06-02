# Pattern Matching & More

[](Pattern%20Matching%20&%20More%20a57e3a2c52ec4ef5994dafaca8f5aca5.md) **←INDEX** 

---

## basic pattern matching

```scala
def matchTest(x: Int): String = x match {
  case 1 => "one"
  case 2 => "two"
  case _ => "other"
}
matchTest(3)  // returns other
matchTest(1)  // returns one
```

```scala
// basic format 
thingToMatch match {
	case A => 
	case B => 
	case _ => 
} 
```

### matching on case classes

```scala
sealed trait Notification
case class Email(sender: String, title: String, body: String) extends Notification
case class SMS(caller: String, message: String) extends Notification
case class VoiceRecording(contactName: String, link: String) extends Notification

def showNotification(notification: Notification): String = {
	notification match {
		case Email(sender, title, _) =>
			s"You got an email from $sender with title: $title"
		case SMS(number, message) =>
			s"You got an SMS from $number! Message: $message"
		case VoiceRecording(name, link) =>
			s"You received a Voice Recording from $name! Click the link to hear it: $link"
	}
}

val someSms = SMS("12345", "Are you there?")
val someVoiceRecording = VoiceRecording("Tom", "voicerecording.org/id/123")

println(showNotification(someSms)) 
println(showNotification(someVoiceRecording))
```

****************Concept**************** 

> Here we defined 3 case classes, and are matching by these classes and using the parameters in the return values. 
Note ~ we can use the wildcard `_` to ignore a particular field
> 

****************`sealed` keyword**

> Used to control the extension of classes and traits. Declaring a class or trait as sealed restricts where we can define its subclasses - ********************************they have to be defined in the same source file********************************.
> 

### pattern guard

```scala
...	
	case SMS(number, message) if importantPeopleInfo.contains(number) =>
		"SMS from someone important!"
...

val importantPeopleInfo = Seq("123-1234, "some message")
```

> Used to make cases more specific. Meaning it won’t match if the Boolean expression after the `if` evaluates to false.
> 

### matching on type only

```scala
... 
	case SMS => ...
	case Email => ... 
...
```

> We can match only based off the type of the case class
> 

⚠️ ~ ****************Warning**************** : If you explicitly define some user defined type by its supertype, then overloaded functions will match with explicitly defined types not the actual type.

```scala
class A
class A1 extends A

def g(x: A): Unit = println("A")
def g(x: A1): Unit = println("A1")

var foo : A = new A1
g(foo)
```

### example #1 ~ basic pattern matching

```scala
def test : String = {
	Some(Some(None)) match {
		case Some(Some(_)) => "test" // matches with this case
		case Some(_) => "fizz"
		case Some(Some(None)) => "buzz"
		case _ => "nope"
	}
}
println(test) // "test"
```

**********Idea**********

> `Some(Some(_))` is a valid case and since pattern matching just matches with the first valid case the function returns “test”
> 

### example #2

```scala
object testing_stuff extends App {
	abstract class Expression 
	case class Const(value: Int) extends Expression
	case class Var(name: String) extends Expression
	case class Op(lhs: Expression, op: String, rhs: Expression) extends Expression

	object Q {
		def simplify(exp: Expression): Expression = 
			exp match {
				case Op(Const(0), "+", r) => simplify(r)
				case Op(l, "*", Const(0)) => Const(0)
				case Op(l, op, r) => Op(simplify(l), op, simplify(r))
				case x => x
			}
			
		val v1: Expression = simplify(
			Op(
				Const(0),
				"+",
				Op(Var("x"), "+", Op(Const(5), "*", Const(0)))
				)
			)
	}
}
```

We can break this down by the different cases that are recursively matched

```scala
Op(Const(0), "+", r)
	simplify r = exp = Op(Var("x"), "+", Op(Const(5), "*", Const(0)))
	Op(l, op, r)
		l = exp = Var("x") => Var("x")
		r = exp = Op(Const(5), "*", Const(0))
			OP(l, "*", Const(0)) => Const(0)

simplify r =return> Op(Var("x"), "+", Const(0))
```

**************************************another expression**************************************

```scala
...
val v1: Expression = simplify(
	Op(
		Op(Var("y"), "*", Const(0)),
		"+",
		Var("x")
	)
)
```

> Basically similar idea here, you can see Since lhs is of type `Op` its gonna match with the last one. Lhs simplifies down to `Const(0)` and as `Var("x")` just matches to `x` as its not an operation it yields itself so we end up with `Op(Const(0), "+", Var("x"))`
> 

---

## folding

### `fold`

> Method which takes an accumulator value and some function, then applies this function in an unspecified order in a cumulative manner.
> 

```scala
val l1 = List(1, 2, 3)
val f1 = (x: Int, y: Int) => x + y
println(l1.fold(0)(f1)) // 6
```

********************************************************************************features of `fold`**

*return value is supertype of input *****************************************************************************************************************************************************************

```scala
val l1 = List(1, 2, 3)
val f1 = (x: Int, y: Int) => x + y
// we can specify ANY type 
val result : Any = l1.fold(0)(f1) 
```

*apply function in unspecified order*

> This is not really clear form basic examples, but the implementation of `fold` can apply the function in some unspecified tree like order, this just should be remembered.
> 

### `foldLeft` and `foldRight`

> Both are methods used on collections (e.g. Lists) and they recursively combine items into another item called an accumulator.
> 

```scala
val l2 = List(1, 2, 3)
val f2 = (x: Int, y: Int) => x + y
// ((0 + 1) + 2) + 3
println(l2.foldLeft(0)(f2)) // 6
```

```scala
val l3 = List(1, 2, 3)
val f3 = (x: Int, y: Int) => x + y
// 1 + (2 + (3 + 0))
println(l3.foldRight(0)(f3)) // 6
```

⚠️ ~ basically just critical that you understand the order of operations, the applications are always just variations of this fundamental left vs right order given some function `f` 

### example #1

- For which `A` , `z` and `f` will the function `foldEquiv` always return `true` for any list `l`

```scala
def foldEquiv[A](z: A, f: (A, A) => A, l: List[A]): Boolean =
	l.foldLeft(z)(f) == l.foldRight(z)(f) && l.foldRight(z)(f) == l.fold(z)(f)
```

> Its basically asking for which accumulator and binary functions the folding functions return the same thing.
> 

*********Option 1 ~********* ❌

```scala
A = String, z = "bla", _ ++ _ 
```

> As we saw in the example of `foldLeft` and `foldRight` they begin from different directions, thus if we concatenate an array from different directions we are bound to end up with a different result
> 

example

```scala
val l = List("a", "b", "c")
val z = "bla"
val f = (x: String, y: String) => x ++ y // expanded form of _ ++ _
println(l.foldRight(z)(f)) // a ++ (b ++ (c ++ bla)) = abcbla
println(l.foldLeft(z)(f))  // ((bla ++ a) ++ b) ++ c = blaabc
println(l.fold(z)(f))      // ((bla ++ a) ++ b) ++ c = blaabc
```

***********Option 2 ~*********** ✅

```scala
A = Boolean, z = false, f = _ && _
```

> This should be clear from intuition, order is irrelevant because if our starting value is `false` and we are doing successive `and` ’s then obviously the result will always be false regardless of the order anything is being applied.
> 

***********Option 3 ~*********** ✅

```scala
A = String, z = "", f = _ ++ _
```

> You can basically just replace `"bla"` with `""` (from Option 1) and understand why this works. Since we are just prepending / appending an empty string even though the order of operations is different we yield the same result.
> 

***********Option 3 ~*********** ❌

```scala
A = Int, z = 0, f = _ - _
```

> Since subtraction isn’t associative its clear the order of operations matters, and since they are different by design for the 3 functions `foldEquiv` would return false.
> 

***********Option 4 ~*********** ✅

```scala
A = Int, z = 9, f = _ * _
```

> We can apply a similar thought process as above. Multiplication is associative ⇒ order is irrelevant ⇒ all functions yield the same output.
> 

****************summary****************

| operator | output | reasoning |
| --- | --- | --- |
| ++ | Different if the starting value is non-empty | The starting value is prepended or appended for foldLeft and foldRight besides that output is the same |
| + | Always the same  | Associative so order doesn't matter |
| -  | Always different | Not associative so order does matter |
| / | Always different  | Not associative so order does matter |
| * | Always the same | Associative so order doesn't matter |

### example #2

```scala
abstract class RoShamBo
case object Rock extends RoShamBo
case object Paper extends RoShamBo
case object Scissors extends RoShamBo

object Test {
	def beats(l: RoShamBo, r: RoShamBo): Boolean = 
		(l, r) match {
			case (Paper, Rock) 
				| (Rock, Scissors)
				| (Scissors, Paper) => true
			case _ => false
		}

	def winner(l: RoShamBo, r: RoShamBo): RoShamBo = 
		if (beats(l, r)) l else r

	val l : List[RoShamBo] = List(Scissors, Paper, Rock, Scissors)
	val res: (RoShamBo, RoShamBo) = 
		(l.foldRight(Paper: RoShamBo)(winner(_, _)), 
			l.foldLeft(Paper: RoShamBo)(winner(_, _)))
}
```

> Code looks a bit weird but lets just break down `res`
> 

```scala
(l.foldRight(Paper: RoShamBo)(winner(_, _)), l.foldLeft(Paper: RoShamBo)(winner(_, _)))

l.foldRight(Paper: RoShamBo)(winner(_, _))
	z = Paper
	f = winner(_, _) = W(_, _)
	W(Scissors, W(Paper, W(Rock, W(Scissors, Paper)))) =final> Scissors 
															 W(Scissors, Paper) => Scissors
				               W(Rock, Scissors) => Rock
			         W(Paper, Rock) => Paper
	W(Scissors, Paper) =final> Scissors

l.foldLeft(Paper: RoShamBo)(winner(_, _)) =final> Rock
	z = Paper
	f = winner(_, _) = W(_, _)
	W(W(W(W(Paper, Scissors), Paper), Rock), Scissors)
				W(Paper, Scissors) => Scissors
				        W(Scissors, Paper) => Scissors
									      W(Scissors, Rock) => Rock
																		W(Rock, Scissors) =final> Rock

(l.foldRight(Paper: RoShamBo)(winner(_, _)), l.foldLeft(Paper: RoShamBo)(winner(_, _)))
=final> (Scissors, Rock)
```

> For anyone still confused, look at the decomposition of the function, its exactly the same thing as the basic example for `foldLeft` and `foldRight` but in this case we are just applying the `W(_, _)` function, if it helps you can thing of `W(_, _)` as `_ vs _` just a function that takes to things; just like `_ + _` or `_ ++ _` ; and yields some result.
> 

---

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

---