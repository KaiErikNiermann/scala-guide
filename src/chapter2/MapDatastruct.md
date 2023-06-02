# Map datastructure

### **map basics**

```scala
def test() : Unit = {
    var myMap : Map[String, Int] = Map("first" -> 1, "second" -> 2, "third" -> 3)
    // accessing first pair
    println(myMap.head) // (first,1)
		// accessing a value
		println(myMap("second")) // 2
    // printing all elements
    myMap.foreach(item => {print(f"${item}, ")}) // (first,1) (second,2) (third,3)
    println()
    // modifying an element 
    myMap += ("first" -> 4) // returns new map, does not modify original
    // deleting an element
    myMap -= "second"       // returns new map, does not modify original
    // printing final output 
    myMap.foreach(item => {print(f"$item, ")}) // (first,4) (third,3)
    println()
}
test()
```

- **Creation** → `var mapName : Map[T1, T2] = Map(a -> b)` / `Map((a, b), (c, d))`

- **Deletion** → `mapName -= "keyToDelete"`

- **Modification** → `mapName += ("keyToModify" -> newValue)`

- **Value** → `println(mapName("keyToAccess"))`

### **Updating and Finding Higher Order Functions**

```scala
def test() : Unit = {
    var myMap : Map[String, Int] = Map("first" -> 1, "second" -> 2, "third" -> 3)
    myMap = myMap.updated("first", 4)       // creates a new map with the updated value
    // using `exists` and `contains` methods
    println(myMap.exists(_._1 == "first"))  // returns bool if key exists
    println(myMap.contains( ("first", 4) )) // returns bool if key and value exists
    
    // using `get` method 
    println(myMap.get("first"))             // returns Option[Int] if key exists
    var value : Option[Int] = myMap.get("fourth")
    println(value.getOrElse(0))             // returns Int if key exists, else default (0)
    // accessing option value
    value match {
        case Some(v) => println(v)
        case None => println("None")
    }

    // using `getOrElse` method 
    println(myMap.getOrElse("first", 0))    // returns Int if key exists, else default (0)
}
test() 
```

> Higher order functions that modify objects usually generate new objects and rarely modify the original object.
> 

### **example #1**

```scala
// Note ~ returns new map, not updated map
def f(m : Map[String, String]) : Map[String, String] = m.updated("Italy", "Rome") 

def test() : Unit = {
    var m1 = Map(("Netherlands", "Amsterdam"), ("France", "Paris"), ("Italy", "Milan"))
    var m2 = m1          // m2 is a reference to m1
    m2 = f(m1)           // m2 is a reference to a new map
    print(m1("Italy"))   // prints "Milan" because m1 is unchanged
    print(" ")  
    print(m2("Italy"))   // prints "Rome"
}
```

### **example #2**

```scala
def f(m: Map[Int, String]) : Unit = println(m) 

def mapVal : Option[String] = {
    val m : Map[Int, String] = Map(1 -> "one", 2 -> "two")
    f(m)                                        // does not affect `m` state
    val n : Map[Int, String] = m + (2 -> "TWO") // does not mutate `m`
    f(n)                                        // does not affect `n` state                                            
    m.get(2)                                    // returns `Some("two")`
}
println(mapVal)
```