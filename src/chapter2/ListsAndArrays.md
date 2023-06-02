# Lists and Arrays

### example #1 ~ Lists

```scala
def v : String = {
        def f1(l : List[String]) : Unit = 
            l.updated(0, "zero") // modifies copy and returns nothing

        def f2(l : List[String]) : List[String] =
            l.updated(1, "one") // modifies copy and returns copy

        def f3(l : List[String]) : List[String] =
            l.updated(2, "two") // modifies copy and returns copy

        // Note ~ on lists .update(index, newValueAtIndex) 
        val l1 = List("one", "two", "three")
        f1(l1) // modifies and returns copy 
        f2(l1) // modifies and returns copy
        val l2 = f3(l1) // resulting List = List("one", "two", "two")
        l2(0) + " " + l2(1) + " " + l2(2) // "one two two"
    }
```

Note ~ `.update` works on boths maps and lists equating the key == index 

→ maps `(key, valueToReplace)` 

→ lists `(index, valueToReplace)`

### example #2 ~ Arrays

```scala
case class Point(x: Int, y: Int) {
        def +(p: Point) = Point(x + p.x, y + p.y)
    }

    object ArrayTest {
        def test : Unit = {
            // initialize array of size 5 
            val a : Array[Point] = new Array(5)
            // initialize point with x = 6 and y = -4
            val p : Point = Point(6, -4)

            // set all elements of array to p
            for(i <- a.indices) a(i) = p

            // add 2 to y of each element
            for(i <- a.indices) a(i) += Point(2, i)
            
            var sum = 0
            for(p <- a) sum += p.y
            print(sum)
        }
    }
    ArrayTest.test
```

|  | array | most other data structures |
| --- | --- | --- |
| updating an index | myArray(index) = value | myStruct = myStruct.updated(index, element) |

**Note:** Other mutable data structures include:
- `ArrayBuffer`: not fixed size (like Arrays) but similar to arrays in all other respects
- `ListBuffer`: mutable List
- `Queue`: LIFO queue
- `Stack`: FIFO stack
- `Vector`: tree
