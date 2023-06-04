# Placeholders and Partially applied functions

## placeholders and partially applied functions in practice

### example #1 ~ given

```scala
// type of function that takes arbitrary type A and returns A
type Multiset[A] = A => A

// (function : A, function : A) and returns function : A
def sub[A](a: Multiset[A], b: Multiset[A]): Multiset[A]
```

**Option A ~** ❌

```scala
// for all elements x in the combined set (a, b)
for (x <- a.toSeq ++ b.toSeq)
	yield x -> (a(x) + b(x)) 

// (x -> (a(x) + b(x))) is of type (Int -> Int) / (Int, Int) 
// which does not conform to return type of A => A
```

**Option B ~** ✅

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

**Option C ~** ❌

```scala
a -- b
// -- operator does not exist in scala 
```

**Option D ~** ❌

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

**Option E~** ✅

```scala
// similar to corrected version above, type A is just inferred in this case
x => if(a(x) - b(x) >= 0) a(x) - b(x) else 0
```

**example of a basic version of this idea implemented**

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

**Option F ~** ✅

```scala
{
	def s(x: A): Int = {
		val ax: Int = a(x) // evaluates a(x)
		if(ax == 0) 0 else ax - b(x) // valid syntax
	} 
	s // returns partially applied function of type eval(A) => Int == Int => Int 
} 
```