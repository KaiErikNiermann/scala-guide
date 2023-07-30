# Lists and Arrays

## Copies Example - Lists

```scala
def v: String = {
  def f1(l: List[String]): Unit =
    l.updated(0, "zero") // modifies copy and returns nothing

  def f2(l: List[String]): List[String] =
    l.updated(1, "one") // modifies copy and returns copy

  def f3(l: List[String]): List[String] =
    l.updated(2, "two") // modifies copy and returns copy

  // Note ~ on lists .update(index, newValueAtIndex) 
  val l1 = List("one", "two", "three")
  
  f1(l1) // modifies and returns copy 
  f2(l1) // modifies and returns copy
  
  val l2 = f3(l1) // resulting List = List("one", "two", "two")
  
  l2(0) + " " + l2(1) + " " + l2(2) // "one two two"
}
```

### key takeaway 1

The important thing to be aware of here is that the `.update` method returns a *new* list, so that is, it uses the elements from the old list, from this creates a new list, and then updates the elements. So there are two key things that happen.

- The original list is **not modified**
- The new instance is the original list with any modifications

**Note**: `.update` works on both maps and lists, equating the key with the index.

- For maps: `(key, valueToReplace)`
- For lists: `(index, valueToReplace)`

---

## Modifications Example - Arrays

```scala
case class Point(x: Int, y: Int) {
  def +(p: Point) = Point(x + p.x, y + p.y)
}

def test: Unit = {
  // initialize array of size 5
  val a: Array[Point] = new Array(5)
  // initialize point with x = 6 and y = -4
  val p: Point = Point(6, -4)

  // set all elements of array to p
  for (i <- a.indices) a(i) = p

  // add 2 to y of each element
  for (i <- a.indices) a(i) += Point(2, i)

  var sum = 0
  for (p <- a) sum += p.y
  print(sum)
}

test()
```

### key takeaway 2

The key takeaway here is that with Lists you use `.updated` which creates a new list, but with arrays you can use the more traditional index notation `array(index)` to modify the original variable.

```scala
val a: Array[Int] = Array(1, 2, 3, 4, 5)

a(0) = 5 

print(a.toList)


val b: List[Int] = List(1, 2, 3, 4, 5)

// throws an error
b(0) = 5

print(b)

```

### misc additions

|                       | array                | most other data structures    |
| --------------------- | -------------------- | ---------------------------- |
| updating an index     | myArray(index) = value | myStruct = myStruct.updated(index, element) |

**Note:** Other mutable data structures include:

- `ArrayBuffer`: Not fixed size (like Arrays) but similar to arrays in all other respects.
- `ListBuffer`: Mutable List.
- `Queue`: LIFO queue.
- `Stack`: FIFO stack.
- `Vector`: Tree.
