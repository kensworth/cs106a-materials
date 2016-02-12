Brad's Guide to Good Style
--------------------------

## Introduction

Often, when students and teachers talk about style, they focus on concepts talked about in [conventional style guides](http://web.stanford.edu/class/cs106a/handouts/17-CodingStyle.pdf), such as consistent whitespace and indentation or descriptive comments, variable, and method names. While these ideas are important--albeit somewhat superficial--elements of good style, often, discussions of what makes well-written code avoid some of the more abstract factors that contribute to a well-styled programs. Examples of these murkier concepts include where to store information, how to best move that information around your program, how to decompose programs effectively, and how these ideas contribute to what makes a "nice" algorithm. Often, conventional good style ideas like effective variable and method names can help, but thinking about those ideas alone is not enough to produce well-written code. While having good style is more of an art than a science (hence why getting practice through programming assignments is crucial to any computer science course), this document is an attempt to spell out as clearly as possible what "good style" really means at a deeper level. The following sections cover some of the more important abstract concepts of a well-constructed program.

### Ways of Storing Information

A critical part of any program is its variables, which allow it to store information for reference later. In Java, there are two main ways to store information in your program: local variables and instance variables.

#### Local Variables

Local variables are variables declared anywhere inside *methods*. These variables are used to keep track of information while the method is running, and then disappear after the method is finished. A somewhat contrived example of this is a program that prints the numbers from 1 to 10 using a `while` loop:

```java
public void countToTen() {
    //This is the declaration of a local variable
    int currentNum = 1;
    while (currentNum <= 10) {
        println(currentNum);
        currentNum++;
    }
}
```

After `countToTen` ends, the variable `currentNum` is no longer accessible anywhere else in the program. Even if `countToTen` ran again, a *new* variable called `currentNum` would be created and set to `1`, and the *old* `currentNum` from the last run wouldn't exist.

Local variables created inside `if` statements only exist inside that `if` block. For example, the following code prints all multiples of 3 that are less than or equal to 10 (using a for loop that is equivalent to the `while` loop above):

```java
public void printMultiplesOfThree() {
    for (int currentNum = 1; currentNum <= 10; currentNum++) {
        if (currentNum % 3 == 0) {
            String lineToPrint = currentNum + " is a multiple of 3!";
        }

        println(lineToPrint); //THIS WOULD BE AN ERROR: lineToPrint DOESN'T EXIST HERE!
    }
}
```

In the example above, `lineToPrint` disappears after the `if` block ends, which causes a problem when the program tries to `println(lineToPrint)`.

Local variables created inside `while` and `for` loops disappear and are created again each time the loop runs. For example, the following code prints every multiple of 10 until 100, inclusive:

```java
public void printPowersOfTen() {
    for (int currentNum = 1; currentNum <= 10; currentNum++) {
        int powerOfTen = powerOfTen * 10; //<--THIS WOULD BE AN ERROR: NO VARIABLE CALLED powerOfTen EXISTS YET!
        println(powerOfTen);
    }
}
```

The code above throws away `powerOfTen` each time the `for` loop runs, so when the loop tries to get the value of `powerOfTen` from the previous iteration of the loop at the beginning of the next iteration so it can multiply `powerOfTen` by 10, it sees nothing, causing an error.

#### Instance Variables

Instance variables are variables declared inside of a class as a whole, meaning they are accessible to any of the methods inside of that class. Instance variables exist as long as the *instance* of that class exists. Here is an example of a simple `Person` class that uses the same instance variable in multiple methods:

```java
public class Person {
    //This is the declaration of an instance variable
    private int money = 10000;

    public void getPaycheck() {
        money += 5000;
    }

    public void payTaxes() {
        money -= 2000;
    }

    public int getMoney() {
        return money;
    }

    public void run() {
        Person bob = new Person();
        println(bob.getMoney()); //Output: 10000

        bob.getPaycheck();
        println(bob.getMoney()); //Output: 15000

        bob.payTaxes();
        println(bob.getMoney()); //Output: 13000
    }
}
```

In the example above, the same `money` instance variable of the *instance* of `Person` called `bob`, is modified by both the `getPaycheck` and `payTaxes`. As a result, the value of `money` for `bob` reflects both of these modifications by the end of the run method.

#### Constants

Another type of variable is the constant, which exists inside a class but never changes. The previous `Person` class has been modified to use constants for the person's wage and taxes:

```java
public class Person {
    private int money = 10000;

    //These are declarations of constants
    private static final int WAGE = 5000;
    private static final int TAXES = 2000;

    public void getPaycheck() {
        money += WAGE;
    }

    public void payTaxes() {
        money -= TAXES;
    }

    ...
}
```

In the above example, rather than using arbitrary values to specify how much the `Person` gets paid, constants called `WAGE` and `TAXES` are used. By using constants instead of arbitrary values, more meaning is given to those values when they're used in context. For example, it makes more sense in context to see `WAGE` added to `money` than some arbitrary number like `5000`.

##### A Helpful Tip:

_A good rule of thumb is that any type of variable (local, instance, or constant) only exists inside the curly braces in which it has been declared. The places in which a variable exists are called its **scope**. This is why indenting properly is really helpful: it's easy to see which variables have been declared where. In other words, it's much easier to see what the **scope** of any variable is._

#### Which Variables Should I Use When?

The hardest part of using these different ways of storing information effectively is knowing which storage technique to use when, so here are some general guidelines:

**Local Variables**: Use when you only need information in a single place in your class, such as within a method, if block, or loop. Remember that that information is only accessible within the place you create the variable containing that information. Using parameter-passing, you can also pass the values of local variables to other methods (see below for more information).

**Instance Variables**: Use when you need to keep track of and modify the information across many methods of your class. These values are easily accessible and modifyable from any method in your class, so be careful about using them too much--it's better to use local variables unless you're keeping track of some kind of state (like how much `money` a `Person` has). If it helps, you can think of an instance variable as a local variable whose **scope** is the entire class.

**Constants**: Use when you need some unchanging value to be accessible in your class, e.g. the `WAGE` that a `Person` earns.

_In general, never declare a variable of any type in a **scope** higher than you need to both calculate the value of the variable and use that value in your code._ To count to ten like the `countToTen` example, it wouldn't make sense to write the following code:

```java
public class Counter() {
    private int currentNum = 1;

    public void countToTen() {
        currentNum = 1; //resets currentNum so that the count starts from 1 again
        while (currentNum <= 10) {
            println(currentNum);
            currentNum++;
        }
    }
}
```

Here, you only ever use `currentNum` inside your `countToTen` method, so why make it an instance variable? You'd be better off just putting `currentNum` directly inside your `countToTen` method since you only ever use it there.

Here's another example of a variable being put in a higher **scope** than it needs to be:

```java
public void drawGridOfCircles(double xPos, double yPos) {
    double x = xPos;
    double y = yPos;

    for (int currRow = 0; currRow < NUM_ROWS; currRow++) {
        for (int currCol = 0; currCol < NUM_COLS; currCol++) {
            add(new GOval(x, y, CIRCLE_RADIUS * 2, CIRCLE_RADIUS * 2));
            x += CIRCLE_RADIUS * 2 + SPACE_BETWEEN_CIRCLES;
        }

        y += CIRCLE_RADIUS * 2 + SPACE_BETWEEN_CIRCLES;
        x = xPos;
    }
}
```

In the program above, `x` and `y` are intialized outside the two `for` loops, and each time one of the `for` loops runs, the program adds to `x` and `y` accordingly. However, `x` and `y` could also just be recalculated inside each `for` loop using the values of `currRow` and `currCol`. That way, the program wouldn't need to have `x` and `y` variables at a higher scope than needed:

```java
public void drawGridOfCircles(double xPos, double yPos) {
    for (int currRow = 0; currRow < NUM_ROWS; currRow++) {
        double y = yPos + currRow * (CIRCLE_RADIUS * 2 + SPACE_BETWEEN_CIRCLES);

        for (int currCol = 0; currCol < NUM_COLS; currCol++) {
            double x = xPos + currCol * (CIRCLE_RADIUS * 2 + SPACE_BETWEEN_CIRCLES);
            add(new GOval(x, y, CIRCLE_RADIUS * 2, CIRCLE_RADIUS * 2));
        }
    }
}
```

While the program above is functionally equivalent to the previous example, `y` is calculated based on `currRow` each time the other `while` loop runs, and `x` is calculated based on `currCol` each time the inner `for` loop runs. That way, `x` and `y` are never stored in a higher **scope** than where they are calculated.

### Ways of Manipulating Information

Moving information around programs elegantly is another important characteristic of well-styled code. Any substantial program should be decomposed into methods, each of which produces results based on the information it takes in. There are two main ways to input information into methods: parameter passing and instance variables.

#### Parameter Passing

The primary way that any program should provide information to a method is through parameters. Think about parameters as the variables passed into a function in math; the function `f(x) = 2x^2 + x + 3` needs to be given a value to use for `x` so it can provide an output. For example, if we decide `x = 3`, then we can pass in `f(3)`, which then evaluates to `24`. Similarly, a method called `drawSquare` needs 3 pieces of information to be able to draw a square: the x coordinate of the square, the y coordinate of the square, and the side length of the square. As a result, the method declaration would look something like this:

```java
public void drawSquare(double x, double y, double sideLength) {
    add(new Grect(x, y, sideLength, sideLength));
}
```

Here, `drawSquare` has 3 parameters it takes in, a double called `x`, a double `y`, and a double called `sideLength`. Each of these parameters can be thought of as a local variable that is available anywhere inside your method. `drawSquare` can then use the values of these local variables to draw a square in the appropriate position on the screen.

By using parameters to take in information, methods can be as generic as possible, meaning they can be used in as many different situations as possible. The `drawSquare` method above is able to draw squares of any size anywhere on the screen because it uses parameters. This flexibility allows `drawSquare` to be reused throughout a larger program.

#### Instance Variables

Instance variables can be used to share the same information between many different methods in a class since they are accessble anywhere in the class. A good example of where using an instance variable would be appropriate is in the `Person` class demonstrated above. There, the same `money` instance variable needs to be modified by both the `getPaycheck` and the `payTaxes` methods, which is why that information is made accessible through an instance variable. Think of instance variables as the "nuclear" option of information passing--they are extremely powerful and allow information to be made accessible in any method of a class, but that level of access is not usually needed. Most of the time, programs can use method parameters to provide the necessary information to a method.

#### Returning Values from Methods

Often, methods won't just *do* something like print a line to the console--they will use their inputs to compute some kind of result, which they then *return* to the method that called them. Just like the math function `f(x) = 2x^2 + x + 3` takes in any value of `x` and returns `2x^2 + x + 3`, a method called `createCircle` would create a circle and then return it to the method that called it so it could be used elsewhere. Here is the `createCircle` method along with an example way it could be used:

```java
public GOval createCircle(double x, double y, double radius) {
    return new GOval(x, y, radius * 2, radius * 2);
}

public void run() {
    GOval circleOne = createCircle(0, 0, 100);
    add(circleOne);
}
```

In the code above, the `createCircle` method takes in an x coordinate, a y coordinate, and a radius, and returns a `GOval` it created using these parameters back to the `run` method, which called it. That `GOval` is then added to the screen in the `run` method. Here, the purpose of having the `createCircle` method is to abstract away how the circle actually gets created so that the `run` method can focus on how to use the circles the `createCircle` method creates.

Notice that no instance variables are being used here--instead of having `createCircle` set some instance variable to a new `GOval` so that `GOval` can be accessible in the `run` method, it just returns that circle directly. In general, unless a method needs to modify some piece of information that is needed in many other methods, it's much better to just return a value so that the "parent" method can use it for whatever it wants.

### Good Decomposition

Well-styled programs apply these information handling principles to decompose abstract, hard-to-solve problems they are responsible for solving into smaller, more manageable pieces, the results of which can be composed again to resolve the larger problem. Each chunk of the larger problem can be handled by a method, which in turn could break the problem it's responsible for into even smaller pieces, each of which is handled by another method. The idea is that at each level of a well-decomposed program, a person reading that program should be able to see the high level strategy used to attack the problem without having to care about how each step in that strategy is implemented. Unfortunately, there is no easy way to describe what makes any decomposition "good." However, there are a few common ideas that can help when trying to break down problems into methods.

#### Every Method Should Do Only One Thing

When writing code, try to write every method such that they only do one thing. For example, take a look at the `calculateHypotenuse` method below:

```java
public double calculateHypotenuse(double sideOne, double sideTwo) {
    double hyp = Math.sqrt(sideOne * sideOne + sideTwo + sideTwo);
    println("The hypotenuse of the right triangle with sides " + sideOne + " and " + sideTwo + " is: " + hyp);
    return hyp;
}

public void run() {
    double hyp = calculateHypotenuse(3, 4);
}
```

Here, `calculateHypotenuse` is both *calculating* the hypontenuse of the right triangle with side lengths `sideOne` and `sideTwo` and printing the result. A better decomposition would look like this:

```java
public double calculateHypotenuse(double sideOne, double sideTwo) {
    return Math.sqrt(sideOne * sideOne + sideTwo + sideTwo);
}

public void reportTriangle(double sideOne, double sideTwo, double hyp) {
    println("The hypotenuse of the right triangle with sides " + sideOne + " and " + sideTwo + " is: " + hyp);
}

public void run() {
    double sideOne = 3;
    double sideTwo = 4;
    double hyp = calculateHypotenuse(sideOne, sideTwo);
    reportTriangle(sideOne, sideTwo, hyp);
}
```

While the example above is more lines of code than the previous version, what is going on is much more explicit because each method only does one thing: `calculateHypotenuse` takes in the side lengths of the triangle legs and returns the length of the hypotenuse, and `reportTriangle` takes in the side lengths of the triangle and prints the result to the console. A person reading the `run` method doesn't have to care how the hypotenuse length is calculated or how the result is reported to the console--they can see that these higher level steps happen one after the other.

One way to ensure that a method never does more than one thing is to make sure to only write that method to do what the method's name says it should do. In the first example, `calculateHypotenuse` did more than just calculate the hypotenuse, which is what its name suggested it did. In the second version howver, both `calculateHypotenuse` and `reportTriangle` did only what their names promised they would do, namely calculate and return the length of the hypotenuse and report the side lengths of the triangle.

#### Methods Should be as Reusable as Possible

In general, it's best to write methods to handle as many different inputs as possible. That way, if the function that method performs is needed somewhere else in the program, the same logic can be reused. In the example above, the `calculateHypotenuse` method can calculate the length of the hypotenuse of any triangle, so if a hypotenuse needed to be calculated at another point in the program, the same method could be used and no code would need to be copied.

#### Over-Decomposition is Always Better

It is always better to decompose more than could be needed. In the example above, the code to calculate the hypotenuse of a triangle with legs 3 and 4 and print the result could be written in 1 line in the `run` method. However, by decomposing that process into 2 methods, the process became much clearer: first, the program calculated the length of the hypotenuse, and then it took that result and reported it through the console. A side effect of decomposition in this way was that if another hypotenuse length needed to be calculated, the same method could Every "unit" of functionality (like calculating the hypotenuse or reporting the side lengths of a triangle) is a candidate for decomposition. One sometimes helpful rule is that if a method doesn't read as much like English as possible, then it can probably be decomposed even more. Another good heuristic is that any method over 20 lines of code long should definitely be decomposed.

#### Keep Decomposition Flowing from General to Specific

In general, methods that handle smaller parts of a problem should never call methods that handle larger chunks of that problem. By preserving this flow from general to specific, it is much easier to understand the strategy the program employs to break the large problem down into smaller problems.