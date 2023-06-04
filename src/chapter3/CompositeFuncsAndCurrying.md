# Composite functions and currying

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

In scala a **closure** is a type of function that refers to variables defined in its enclosing scope. 
In the example here the closure is the function that we are returning from `f` which is mutating the variabl `x` enclosed in the scope of `f` , the mutate state of `x` is then **saved** which means on successive calls `x` **can be changed** which is how we go from 5 → 8 instead of say 5 → 5 

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

**another example** 

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

**Option A ~** ✅

```scala
cp == acp
```

- **Remember** - A curried function that takes for example 1 parameter and returns a function which takes another parameter which then returns some result
    - `cp` takes Int, returns function that takes Int, which returns result
    - `acp` takes Int, returns function that takes Int, which returns result

**Option B ~** ✅

```scala
uncurry((x: Int) => ((y: Int) => x + y)) == ncp
```

- Since we called uncurry only with the first argument, it gives us a partially the partially applied function

```scala
(x: Int, y: Int) => f(x)(y) // expects two params and returns an Int result
```

⇒ This makes the two functions equivalent 

**Option C ~** ❌

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

**Option D ~** ✅

```scala
curry(uncurry(acp)) == cp
```

- Assuming (based on the previous cases) `curry` and `uncurry` work properly, we can clearly see that both functions are equivalent in how they function

**Option E ~** ✅

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