# Application examples

## bringing it all together

### example #1

```scala
trait Iterator[+A] {
	def hasNext: Boolean
	def next(): A
}

trait Iterable[+A] { def iterator: Iterator[A] }

class RangeIterator(var cur : Int, val step : Int, val end : Int) extends Iterator[Int] {
	def hasNext = cur < end
	def next() = {
		val res = cur
		cur += step
		res
	}
}

case class MyRange(start : Int, step : Int, end : Int) extends Iterable[Int] {
	override def iterator : Iterator[Int] = new RangeIterator(start, step, end)
}

object Test {
	def test : Unit = {
		val r = MyRange(-2, 2, 10)
		val it1 = r.iterator
		val it2 = r.iterator
		print(it1.next()); print(it2.next()); print(it1.next())
		print(it2.next()); print(it2.next()); print(it1.next())
	}
}
Test.test
```

1. For now we gonna ignore the type related shit and just focus on the `myRange` declaration
    
    ```scala
    it1, it2
    	curr = -2
    	step = 2
    	end = 10
    
    it1.next() = -2 
    	res = -2, curr = 0 -> return -2
    it2.next() = -2
    	res = -2, curr = 0 -> return -2 
    it1.next() = 0
    	res = 0, curr = -2 -> return 0
    it2.next() = 0
    	res = 0, curr = -2 -> return 0
    it2.next() = 2
    	...
    it1.next() = 2 
    ```
    
    ************************explanation************************
    
    > Each new assignment to `r.iterator` is a ********new******** class instance of `RangeIterator` from there it becomes a pretty trivial act of just calling the functions for either class instance and modifying the values accordingly making sure to not misread the very painful to look at code.
    > 

---

## Additional

### Common higher order function properties

| function | works on  | applies | returns |
| --- | --- | --- | --- |
| exists | List[A] | A => Boolean | Boolean  |
| map | List[A] | A => B | List[B] |
| flatMap | List[A] | Int => IterableOnce[B] | List[B] |
| foldLeft / foldRight | List[A] | z: B , (B, A) => B | B |
| filter | List[A] | A => Boolean  | List[A] |
| forall | List[A] | A => Boolean | Boolean |