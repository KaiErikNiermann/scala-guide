# Miscallaneous

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