# Class methods
## functions and classes

### example #1 ~ function overloading

```scala
class Vehicle
class Car extends Vehicle
class Porsche extends Car

object Test {
	def g(v: Any) = "Any"
	def g(v: Vehicle) = "Vehicle"
	def g(v: Car) = "Car"
	def g(v: Porsche) = "Porsche"

	def x : Unit = {
		val v1 : Any = new Porsche
		val v2 : Porsche = new Porsche
		println(g(v1) + ", " + g(v2))
	}
}
Test.x

output -> "Any, Porsche"
```

********************Concept ~******************** In scala when it comes to type matching for overloaded functions we **match with the most specific type** which is why 

- v1 : Any == Any
- v2 : Porsche == Porsche

If we were to for example omit `g(v: Porsche)` the most specific type would be `g(v: Car)` which in turn would make the output 

`Any, Car` (`v1: Any, v2: Porsche` ) 

As the Porsche type would match with the most specific (closest) type out of the given functions, this being `Car`

### example #2 ~ just fizzbuzz for some reason

```scala
def isMultipleof(n: Int, m: Int): Boolean = n % m == 0

def fizzString(n: Int): String = {
	if (isMultipleof(n, 3) && isMultipleof(n, 5)) "fb"
	else if (isMultipleof(n, 3)) "f"
	else if (isMultipleof(n, 5)) "b"
	else n.toString
}

def fizzies : Seq[String] =
	(3 until 15).map(_ - 3).map(fizzString)

fizzies.foreach(print)
```

```scala
(3 until 15) == [3, 14]
	.map(_ - 3) == "subtract 3 from each element in the array"
[3, 14] == [0, 11] 
	.map(fizzString) 
0 -> fb
1 -> 1
2 -> 2
3 -> f
4 -> 4
5 -> b
6 -> f
7 -> 7
8 -> 8
9 -> f
10 -> b
11 -> 11

output -> fb 1 2 f 4 b f 7 8 f b 11
```

### example #3 ~ higher order functions and types

```scala
val f = l => l.flatMap(_.map(_ + 2).sum).filter(_ != '5')
```

1. What do all the higher order functions take in and what do they spit out 

| function | purpose | takes | returns |
| --- | --- | --- | --- |
| flatMap | applies some function to all elements in a collection, then flattens this collection in other words, it “de-nests” it. | Function : A | new collection of some primitive type e.g. Seq[Int] |
| map | applies some function to all elements in a collection | Function : A | Some new collection of type A |
| sum | returns sum of all elements in specific types of collections, e.g. Lists | N.A. | Int representing sum of values |
| filter | used to select all elements which match some predicate (Boolean condition) | boolean predicate e.g. _ > 5  | Collection of all elements matching the predicate |
1. Now that we understand that, lets break the statement apart

```scala
l.flatMap(
	.map(_ + 2)            // adding 2 to each element or element of a subcollection
	).sum                  // summing subcollections 
	).filter( _ != 5)      // returning a list of elements not equal to 5
```

****************************************************assumptions from the code**************************************************** 

1. `flatMap` returns some collection of whatever type the inner function yields in the end
2. `.map` is adding to the elements so the inner collection must have a numeric type
3. `.sum` *returns a numeric type*, AKA `Int` and can *only operate on collections*
4. from this we can determine that we are calling filter on some collection of type `Int` 
5. `filter` has no effect on the type of the collection

******************************Conclusion ⇒****************************** From this we can see that f is a function of type 

```scala
List[List[Int]] => List[Int]
```

*From (b.) we know that the inner collection has a type `Int` so we determined the input is of type `List[List[Int]]` and since `.sum` returns `Int` and we are running `.flatMap` it should be clear that the resulting return type of the function is `List[Int]`* 

Note ~ I don’t actually know how to determine the collection, but the questions only have `List` anyways so I think in this case we are just going in assuming the collections are Lists
