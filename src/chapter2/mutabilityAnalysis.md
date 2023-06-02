# Analysing State

## Behavior/Mutability analysis

### notes on classes

```scala
class A(var a: Int) {
		// class constructor
    def this() = this(5) 
}

def test() : Unit = {
		// using the default constructor
    val a = new A()   // a.a = 5
		// using constructor with parameter
    val b = new A(10) // b.a = 10
}
test()
```

In Scala, classes can have **class constructors** which can be used to provide default parameters if none are provided. If no parameters are provided, the `this()` **auxiliary constructor** calls the **primary constructor** (the one with the parameter) with the number 5.

### **Example #1**

```scala
class GameLogic {
    def height = 10          // The height of the game board
    var dir : Int = -1       // The snake is moving up
    var y : Int = height - 1 // The snake is at the bottom ~ start = 9 

    def step() : Unit = {
        // Move the snake
        y = y + dir // 8, 7, 6, 5, 4, 3, 2, 1, 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
        // Check if the snake hit the top or bottom
        if(y == 0 || y == height - 1) 
            // Change the direction 
            dir = -dir // dir = -1 * dir
    }

    final def getCellType(x: Int, y: Int) : CellType =
        // Check if the snake is at this position
        if (y == this.y) // (0, 9), (1, 9), ... ,(9, 9) in other words a row
            // Return the snake body
            SnakeBody(1.0f)
        else 
            // Return an empty cell
            Empty()
}
```

**Approach**

1. Understand the **initial state**:
    
    - `y = 9` thus for all coordinates `(0 to 9, 9)` we render the snake body as a row.
    
2. Understand the **continued state**:
    
    - At first, we decrease `y` each time `step` is called. As the top is `y = 0` and bottom is `y = 9`, this means we at first **move up** then once `dir` is inverted we move **down**. 
    
3. **Conclusion:** The behavior of this code segment is a row of cells that start at row `y = 9` then move up to `y = 0` before moving back down due to the direction being inverted.

### **Example #2**

```scala
class SnakeLogic(val random: Random, val nrColumns: Int, val nrRows: Int) {
    // initalization code
    def this() = this(new ScalaRandom(), SnakeLogic.defaultRows, SnakeLogic.defaultColumns)
    var flag: Boolean = false 

    // each call flag is toggled
    def step(): Unit = flag = !flag

    def getGridType(x: Int, y: Int): GridType = 
        if(x % 2 == 0 && flag)  // if x is even and flag is true
            // (0, y) , (2, y) , ... , (2n, y) are rendered as snake
            SnakeBody(1.0f)     
        else 
            // (1, y) , (3, y) , ... , (2n+1, y) are rendered as empty
            Empty()
}
```

**Approach**

1. **Initial state**:
    
    - `flag` is false thus the board is rendered as completely blank.
    
2. **Continued state**:
    
    - `flag` is true, thus for all even `x` we render the snake body and for all uneven we render empty.
    
3. **Conclusion**: As the `flag` variable is toggled on and off with each `step` call, we alternate between a blank board and a board of cells with even `x` being rendered. So, the behavior would be flickering columns.

### example #3

```scala
object testing_stuff extends App {
    case class Point(x: Int, y: Int) {
        def +(p: Point) = Point(x + p.x, y + p.y)
    }

    class GameLogic(val random : RandomGenerator, val gridDims : Dimensions) {
        var cursor : Point = Point(gridDims.width - 1, 0) // Start at top right e.g. (9, 0)
        var dir : Direction = South()                     // Start facing south

        def toPoint(dir: Direction): Point = {
            dir match {
                case North() => Point(0, -1)
                case South() => Point(0, 1)
                case East() => Point(1, 0)
                case West() => Point(-1, 0)
            }
        }

        def step(): Unit = {
            // if we're moving south and we're at the bottom, turn left
            if (dir == South() && cursor.y == gridDims.height - 1) dir = West()
            // if we're moving west and at the left go up
            else if (dir == West() && cursor.x == 0) dir = North()
            // if we're moving north and at the top go right
            else if (dir == North() && cursor.y == 0) dir = East()
            // if we're moving east and at the right go down
            else if (dir == East() && cursor.x == gridDims.width - 1) dir = South()
            cursor = cursor + toPoint(dir)
        }
        // direction summary 
        // south -> west -> north -> east -> south AKA clockwise motion

        def getCellType(p: Point): CellType = {
            if (p == cursor) 
                SnakeHead(dir)
            else 
                Empty()
        }
    }
}
```

**Approach**

1. **Initial state**:
    
    - Cell starting off at for example `(9, 0)` assuming `gridDims.width = 10`.
    
2. **Continued state**:
    
    - Directions go in the order south → west → north → east → south, which is clockwise.
    
3. **Conclusion**: Snake starts off on the right edge of the board, once it hits the bottom edge it moves in a clockwise motion repeating this on a loop.

