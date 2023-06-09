# Folding

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
