# Advanced functions

## Syntax Notes

### `type` aliases

********************main idea ~******************** feature that allows us to declare our own types 

Aliases ⇒ ********************Parameterized Types******************** 

```scala
type Integer = Int        // alias Integer for Int type
var foo : Integer = 10

type IntList = List[Int] // alias IntList for List[Int] type
var bar : IntList = List(1, 2, 3)
```

Aliases ⇒ ***************Function types***************

```scala
type IntToString = Int => String // function that takes an int and returns a string
def IntThingToString(items : IntList, intToString: IntToString) : List[String] {
	items.map(intToString) // intToString(item: Int) : String 
}
```

Aliases ⇒ ******************************************illegal types****************************************** 

```scala
type A = List[A] // cyclical reference involving A
type T = List    // type needs parameters but we dont define them 

// unable to select part of another type that has more than one element
type Y = Tuple2[Int, String]
type Z = List[Y.key] 
```

### `type` members

*********************abstract type member********************* 

```scala
trait Repeat {
	// abstract type member
	type RepeatType
	def apply(item: RepeatType, reps: Int): RepeatType
}
```

*************************************implementing an abstract type member*************************************

```scala
object IntegerRepeat extends Repeat {
	type RepeatType = Int
	def apply(item: RepeatType, reps: Int): RepeatType = {
		(item.toString * reps).toInt
	}
} 
// IntegerRepeat(3, 5) -> 33333 "5 3s"
// IntegerRepeat(10, 3) -> 101010 "3 10s"
```

### confusing syntax

********************************Aliased operators********************************

|  | List, Sets | Lists |
| --- | --- | --- |
| concatenate | ++  | ::: |
| append element | :+ | - |
| prepend element | +: | :: |
| append list  | :++ | - |
| prepend list | ++: | - |

```scala
(1, 2) ++ (3, 4) -> (1, 2, 3, 4)
(1, 2) ::: (3, 4) -> (1, 2, 3, 4)
(1, 2) :+ 3 -> (1, 2, 3)
1 +: (2, 3) -> (1, 2, 3)
1 :: (2, 3) -> (1, 2, 3)
(1, 2) :++ (3, 4) -> (1, 2, 3, 4)
(3, 4) ++: (1, 2) -> (1, 2, 3, 4)
```

⚠️ ******************Reminder ~****************** none of these operators mutate the original List / Set  ************************************

`_` ******************multipurpose operator ~ *********************application examples*********************** 

***Wildcard*** for pattern matching 

```scala
var l = List(1, 2, 3, 4)
l match {
	case head :: _ :: tail => 
		// prints "head: 1, tail (3, 4)" with _ = 2
		println(f"head: $head, tail: $tail") 
	case _ => println("no match")
}
```

***************************************************************Argument placeholder***************************************************************

```scala
def add(x: Int, y: Int): Int = x + y

// partially applied function of type Int => Int 
val result : Int => Int = add(2, _: Int) // function which takes 1 parameter and adds 2
// Note ~ type annotation of placeholder not necessary but probably better ? 

// calling the partially applied function
println(result(3)) // prints "5"
```

Note - You *don’t need placeholders* for partially applied functions

```scala
def add(x: Int, y: Int): (Int => Int) = {
	def add2(z: Int): Int = {
		x + y + z
	}
	add2 
} 

val partial = add(2, 3)
// result(5) equivalent to result(5)(_)
println(result(5)) // prints 10
		
```

***************************************************Type placeholder***************************************************

```scala
def foo[A](a: A): A = a // when called with 5, type inferred to be Int

val result: _ = foo(5) // type infered to be Int 
// via inference ~ val result : Int = foo(5) = 5

println(result) // prints "5"
```

---

## placeholders and partially applied functions in practice

### example #1 ~ given

```scala
// type of function that takes arbitrary type A and returns A
type Multiset[A] = A => A

// (function : A, function : A) and returns function : A
def sub[A](a: Multiset[A], b: Multiset[A]): Multiset[A]
```

******************Option A ~****************** ❌

```scala
// for all elements x in the combined set (a, b)
for (x <- a.toSeq ++ b.toSeq)
	yield x -> (a(x) + b(x)) 

// (x -> (a(x) + b(x))) is of type (Int -> Int) / (Int, Int) 
// which does not conform to return type of A => A
```

**********************Option B ~********************** ✅

```scala
{
	// has the correct type of A => Int
	def s(x: A): Int = {   // input parameter for the actual computation
		val r = a(x) - b(x)  // result of type Int
		if (r >= 0) r else 0 // returns 0 to avoid negatives
	}
	s // returns partially applied function x
}
```

*example of this implemented*

```scala
type Multiset[A] = A => Int

def sub[A](a: Multiset[A], b: Multiset[A]) : Multiset[A] = {
	def s(x: A): Int = {
		val r : Int = a(x) - b(x) // e.g a(1) - b(1) = (1) - (1 + 1) = 1 - 2 = -1
		if(r >= 0) r  
		else 0
	}
	s // return partially applied function s
}

// define two multisets
val a: Multiset[Int] = (x: Int) => x
val b: Multiset[Int] = (x: Int) => x + 1

// subtract a from b AKA a(1) - b(1) == 1 - (1 + 1)
val c : Int => Int = sub(a, b)

// print the result
println(c(-10))
```

********Option C ~******** ❌

```scala
a -- b
// -- operator does not exist in scala 
```

**********************Option D ~********************** ❌

```scala
// not specifying the argument you want to pass the function
if (a(_) - b(_) >= 0) // does not know what to put in the placeholders
	a(_) - b(_) 
else 
	0
```

modification that would make this valid

```scala
(x: A) => a(x) - b(x) // specifying `x` as the argument we intend to pass
```

**********************Option E~********************** ✅

```scala
// similar to corrected version above, type A is just inferred in this case
x => if(a(x) - b(x) >= 0) a(x) - b(x) else 0
```

****************************example of a basic version of this idea implemented****************************

```scala
... 
def sub[A](a: Multiset[A], b: Multiset[A]) : Multiset[A] = {
	x => a(x) - b(x) // ignroing the conditional just for simplicity sake
}

val a: Multiset[Int] = (x: Int) => x
val b: Multiset[Int] = (x: Int) => x + 1

val c: Int => Int = sub(a, b) // c is a function that takes and int and returns one
println(c(-10)) // 10 - (10 + 1) = -1
```

**********************Option F ~********************** ✅

```scala
{
	def s(x: A): Int = {
		val ax: Int = a(x) // evaluates a(x)
		if(ax == 0) 0 else ax - b(x) // valid syntax
	} 
	s // returns partially applied function of type eval(A) => Int == Int => Int 
} 
```

---

## composite functions and currying

### composite functions

```scala
def andThen[A, B, C](f: A => B, g: B => C): A => C = {
	a => f(g(a))
} 

def f(x: Int): Int = x + 1 // Int => Int thus A = Int, B = Int
def g(x: Int): Int = x * 2 // Int => Int thus B = Int, C = Int

val composed: Int => Int = andThen(f, g)
println(composed(2)) // f(g(2)) == (2 * 2) + 1 == 5
```

### currying

basics

```scala
// regular functions
val sum : (Int, Int) => Int = (x, y) => x + y // variable syntax
def sum(x: Int, y: Int): Int = x + y          // function syntax

// curried variants
val curriedSum : Int => Int => Int = x => y => x + y
def curriedSum(x: Int)(y: Int) = x + y
def curriedSum(x: Int)(y: Int) = (sum _).curried

// calling conventions
curriedSum(1)(2) // equal to 3

// partially applied function
val increment : Int => Int = curriedSum(1) // same thing as curriedSum(1)(_)
val incr2 : Int = increment(2) // equal to 3
```

application in type inference

```scala
def curried_find[A](xs: List[A])(predicate: A => Boolean): Option[A] = {
	xs match {
		case Nil => None 
		case head :: tail => 
			if (predicate(head)) Some(head) 
			else curried_find(tail)(predicate)
	}
}
// no need to specify the type in the predicate as the compiler can infer it 
// from the first argument : List[Int] thus A = Int 
val result : Option[Int] = curried_find(List(1, 2, 3, 4))(x => x == 5) 
```

application for improved flexibility

```scala
// generic curried sum function
def sumF(f: Int => Int)(x: Int, y: Int): Int = f(x) + f(y)
val sum : (Int, Int) => Int = sumF(identity)
val increment = sum.curried(1)

// cleaner way to use map as increment only takes a single parameter
val numbers = List(1, 2, 3, 4)
numbers.map(increment) // List(2, 3, 4, 5)

// instead of having to do 
numbers.map(n => sum(1, n))
```

### closures

```scala
def f : Int => Int = {
	var x : Int = 2
	(y: Int) => x += y; x
}

def test : Unit = {
	var foo : Int => Int = f
	println(foo(3)) // prints "5"
	println(foo(3)) // prints "8"
} 
test
```

In scala a *******closure******* is a type of function that refers to variables defined in its enclosing scope. 
In the example here the closure is the function that we are returning from `f` which is mutating the variabl `x` enclosed in the scope of `f` , the mutate state of `x` is then ******************saved****************** which means on successive calls `x` *********************************************can be changed********************************************* which is how we go from 5 → 8 instead of say 5 → 5 

### example #1

```scala
def iterate[A](f: A => A, i : Int)(a: A): A =
	if(i == 0) 
		a
	else 
		// the reason you can do f(a) is because a in the call was marked as a `placeholder`
		iterate(f, i - 1)(f(a))

def andThen[A, B, C](f: A => B, g: B => C): A => C = {
	x => g(f(x))
} 

def times3 : Int => Int = _ * 3
def plus2 : Int => Int = _ + 2
def min5 : Int => Int = _ - 5

val x : Int = 
	andThen(iterate(andThen(plus2, times3), 2), min5)(1)
```

1. breaking down the main function call

```scala
val inner : Int => Int = andThen(plus2, times3)
val twice_composed : Int => Int = iterate(inner, 2)(_) // you can omit the placeholder
val final_function : Int => Int = andThen(twice_composed, min5) // min5(twice_comp(_))
val result : Int = final_function(1) // gives 28
```

********************************another example******************************** 

```scala
val x : Int =
	andThen(min1, iterate(andThen(times2, min1), 3))(3)

// iterate(andThen(times2, min1), 3) == ((((((a * 2) - 1) * 2) - 1) * 2) - 1)
// andThen(min1, ...) == (((((([a - 1] * 2) - 1) * 2) - 1) * 2) - 1) 
// a = 3 => (((((([3 - 1] * 2) - 1) * 2) - 1) * 2) - 1) = 9 
```

### example #2

```scala
// basic sum function
	def ncp(x: Int, y: Int): Int = x + y

	// takes int x and returns a function that takes int y and returns int			
	def cp(x: Int): Int => Int = (y: Int) => x + y

	// curried sum function
	def acp(x: Int)(y: Int): Int = x + y

	// call a function with the curried syntax
	def curry[A, B, C](f: (A, B) => C)(x: A)(y: B): C = f(x, y)
	val curriedSum = curry(ncp)
	println(curriedSum(1)(2))

	// call function with uncurried syntax
	def uncurry[A, B, C](f: A => B => C)(x: A, y: B): C = f(x)(y)
	val uncurriedSum = uncurry(cp)
	println(uncurriedSum(1, 2))
```

**************************Option A ~************************** ✅

```scala
cp == acp
```

- ******************Remember****************** - A curried function that takes for example 1 parameter and returns a function which takes another parameter which then returns some result
    - `cp` takes Int, returns function that takes Int, which returns result
    - `acp` takes Int, returns function that takes Int, which returns result

**********Option B ~********** ✅

```scala
uncurry((x: Int) => ((y: Int) => x + y)) == ncp
```

- Since we called uncurry only with the first argument, it gives us a partially the partially applied function

```scala
(x: Int, y: Int) => f(x)(y) // expects two params and returns an Int result
```

⇒ This makes the two functions equivalent 

**************Option C ~************** ❌

```scala
uncurry(acp) == cp
```

- `uncurry(acp)` ~ uncurried
    - type : (Int, Int) ⇒ Int
    - calling method : e.g. (5, 3)

- `cp` ~ curried
    - type : Int ⇒ Int ⇒ Int
    - calling method e.g. (5)(3)

⇒ clearly the functions are not equivalent 

**************************Option D ~************************** ✅

```scala
curry(uncurry(acp)) == cp
```

- Assuming (based on the previous cases) `curry` and `uncurry` work properly, we can clearly see that both functions are equivalent in how they function

**********************Option E ~********************** ✅

```scala
curry(ncp) == ((x: Int) => ((y: Int) => x + y))
```

- Equivalent because of the same idea mentioned in Option B, the anonymous function

```scala
(x: Int) => ((y: Int) => x + y)
// type : Int => Int => Int
// called : (5)(3)
// conclusion -> equivalent to curry(ncp)
```

### example #3

```scala
def f(): Int => Int = {
	var x = 2 
	(y: Int) => { x+= 2; y * x } // ! Note ~ function `modifies` x 
}

def main() : Unit = {
	var g = f() // x = 2
	g(1)        // x = 4
	g(1)        // x = 6
	g = f()     // reassigned thus x = 2
	g(1)        // x = 4
	g(1)        // x = 6
}
```

- f contains a closure which captures the state of `x` , as we are mutating the state of `x` the return value will be different even if the function call is the same.

---

## Miscellaneous

Not really sure why this question is in this section but lets go through it anyways 

### example #1

```scala
class A(var x: Int) {
	class B(var y: Int) {
		def decrease() = 
			x -= y
	}
}

object Test {
	def test : Unit = {
		val a = new A(10)  // a.x = 10
		var b = new a.B(1) // b.y = 1
		val a2 : A = a     // a2.x = 10 Note ~ reference to A
		b.decrease()       // 10 = 10 - 1 = 9 -> a.x = 9, a2.x = 9
		val b2 : a2.B = new a2.B(3) // b2.y = 3
		b2.decrease()      // 9 = 9 - 3 = 6 -> a.x = 6, a2.x = 6
		b = new a.B(6)     // b.y = 6
		b.decrease()	   // 6 = 6 - 6 = 0 -> a.x = 0, a2.x = 0
	}
}
```

> When assigning objects you are always **assigning references**, if two variables point to the same object, changing properties of the object changes it for both variables. In this case that `a` `b` and `a2`. 

We change `a` using `b.decrease()` and as `a2` points to the same object as `a` its also changed here.
> 

---

## Additional

### 10 different ways of writing `add()`

```scala
//--------- regular functions ---------- // 

// regular
def add(x: Int, y: Int): Int = { x + y }

// regular without brackets
def add2(x: Int, y: Int): Int = x + y

// partially applied
def add3(x: Int): Int => Int = {
	def add4(y: Int): Int = x + y
	add4
}

//--------- variable functions ---------- // 

// regular curried syntax
def add5(x: Int)(y: Int): Int = x + y

// regular variable function
val add6 : (Int, Int) => Int = (x: Int, y: Int) => x + y

// named anonymous function
val add7 = (x: Int, y: Int) => x + y

// more compact named anon function 
val add8 = (Int, Int) => Int = _ + _

// variable partially applied function
val add9 : Int => Int => Int = (x: Int) => (y: Int) => x + y

// variable curried fuction
val add10 = (x: Int) => (y: Int) => x + y

// lazy val regular function
lazy val add11 = (x: Int, y: Int) => x + y
```