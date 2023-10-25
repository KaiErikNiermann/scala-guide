# Syntax

## `type` aliases

**main idea ~** feature that allows us to declare our own types 

Aliases ⇒ **Parameterized Types** 

```scala
type Integer = Int        // alias Integer for Int type
var foo : Integer = 10

type IntList = List[Int] // alias IntList for List[Int] type
var bar : IntList = List(1, 2, 3)
```

Aliases ⇒ **Function types**

```scala
type IntToString = Int => String // function that takes an int and returns a string
def IntThingToString(items : IntList, intToString: IntToString) : List[String] {
	items.map(intToString) // intToString(item: Int) : String 
}
```

Aliases ⇒ **illegal types** 

```scala
type A = List[A] // cyclical reference involving A
type T = List    // type needs parameters but we dont define them 

// unable to select part of another type that has more than one element
type Y = Tuple2[Int, String]
type Z = List[Y.key] 
```

## `type` members

**abstract type member** 

```scala
trait Repeat {
	// abstract type member
	type RepeatType
	def apply(item: RepeatType, reps: Int): RepeatType
}
```

**implementing an abstract type member**

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

## confusing syntax

### Aliased operators

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

⚠️ **Reminder ~** none of these operators mutate the original List / Set  

<br>

### Multipurpose operator '`_`'  

**Wildcard** for pattern matching 

```scala
var l = List(1, 2, 3, 4)
l match {
	case head :: _ :: tail => 
		// prints "head: 1, tail (3, 4)" with _ = 2
		println(f"head: $head, tail: $tail") 
	case _ => println("no match")
}
```

**Argument placeholder**

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

**Type placeholder**

```scala
def foo[A](a: A): A = a // when called with 5, type inferred to be Int

val result: _ = foo(5) // type infered to be Int 
// via inference ~ val result : Int = foo(5) = 5

println(result) // prints "5"
```