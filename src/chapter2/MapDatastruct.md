# Map Data Structure

## Map Basics

```scala
def test(): Unit = {
    var myMap: Map[String, Int] = Map("first" -> 1, "second" -> 2, "third" -> 3)

    // Accessing first pair
    println(myMap.head) // (first,1)

    // Accessing a value
    println(myMap("second")) // 2

    // Printing all elements
    myMap.foreach(item => { print(f"${item}, ") }) // (first,1) (second,2) (third,3)

    println()

    // Modifying an element
    myMap += ("first" -> 4) // Returns new map, does not modify the original

    // Deleting an element
    myMap -= "second" // Returns new map, does not modify the original

    // Printing the final output
    myMap.foreach(item => { print(f"$item, ") }) // (first,4) (third,3)

    println()
}
test()
```

### Summary

Here is a short summary of some map operations. For a comprehensive rundown, check out [_the docs_](https://docs.scala-lang.org/overviews/collections/maps.html) page for Maps.

#### Creation

To create a new map, you define types for the key (`T1`) and the associated value (`T2`). Then, you create the map instance using the `Map()` constructor. For the key-value pairs, you can use either arrow notation `a -> b` or bracket notation `(a, b)`.

```scala
var myMap: Map[T1, T2] = Map(a -> b) // or Map((a, b), (c, d))
```

#### Deletion

The operation removes the mapping with `keyToDelete` and returns a new map, which is why we have to use `-=` and not just `-`.

```scala
myMap -= "keyToDelete"
```

#### Modification

The operation adds a mapping to the map and then returns the updated map.

```scala
myMap += ("keyToModify" -> newValue)
```

#### Value Access

```scala
println(myMap("keyToAccess"))
```

---

## Updating and Finding Higher Order Functions

```scala
def test(): Unit = {
    var myMap: Map[String, Int] = Map("first" -> 1, "second" -> 2, "third" -> 3)

    myMap = myMap.updated("first", 4) // Creates a new map with the updated value

    // Using `exists` and `contains` methods
    println(myMap.exists(_._1 == "first"))  // Returns a bool if the key exists
    println(myMap.contains(("first", 4)))   // Returns a bool if the key and value exist

    // Using `get` method
    println(myMap.get("first"))              // Returns an Option[Int] if the key exists
    var value: Option[Int] = myMap.get("fourth")
    println(value.getOrElse(0))              // Returns an Int if the key exists, else default (0)
    
    // Accessing option value
    value match {
        case Some(v) => println(v)
        case None => println("None")
    }

    // Using `getOrElse` method
    println(myMap.getOrElse("first", 0))     // Returns an Int if the key exists, else default (0)
}
test() 
```

I recommend again for this to maybe just run the code yourself and if you are still confused again checkout [_the docs_](https://docs.scala-lang.org/overviews/collections/maps.html) page for Maps, it covers all the operations quite nicely.

### key takeaway

> Higher-order functions that modify objects usually generate new objects and rarely modify the original object.

## Example 1

```scala
// Note ~ returns a new map, not an updated map
def f(m: Map[String, String]): Map[String, String] = m.updated("Italy", "Rome")

def test(): Unit = {
    var m1 = Map(("Netherlands", "Amsterdam"), ("France", "Paris"), ("Italy", "Milan"))
    var m2 = m1           // m2 is a reference to m1
    m2 = f(m1)            // m2 is a reference to a new map
    print(m1("Italy"))    // Prints "Milan" because m1 is unchanged
    print(" ")  
    print(m2("Italy"))    // Prints "Rome"
}
```

## Example 2

```scala
def f(m: Map[Int, String]): Unit = println(m)

def mapVal: Option[String] = {
    val m: Map[Int, String] = Map(1 -> "one", 2 -> "two")
    f(m)                                         // Does not affect `m` state
    val n: Map[Int, String] = m + (2 -> "TWO")    // Does not mutate `m`
    f(n)                                         // Does not affect `n` state
    m.get(2)                                     // Returns `Some("two")`
}
println(mapVal)
```
