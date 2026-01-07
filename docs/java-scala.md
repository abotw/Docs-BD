# Java & Scala

*A gentle introduction for newcomers*

------

## 1. What are Java and Scala?

### Java

**Java** is one of the most popular programming languages in the world.

-   Created in **1995**
-   Widely used in:
    -   Backend servers
    -   Android development
    -   Enterprise systems
-   Famous slogan: **“Write Once, Run Anywhere”**

Java is known for:

-   Stability
-   Huge ecosystem
-   Strong tooling
-   Clear, explicit syntax (good for beginners)

------

### Scala

**Scala** is a modern language that runs on the **Java Virtual Machine (JVM)**.

-   Created in **2003**
-   Combines:
    -   **Object-Oriented Programming (OOP)** (like Java)
    -   **Functional Programming (FP)**
-   Fully interoperable with Java

Scala is known for:

-   Concise syntax
-   Powerful type system
-   Functional programming support

------

### Java vs Scala (Quick Comparison)

| Feature             | Java              | Scala               |
| ------------------- | ----------------- | ------------------- |
| Paradigm            | Object-Oriented   | OOP + Functional    |
| Syntax              | Verbose, explicit | Concise, expressive |
| Learning curve      | Easier            | Steeper             |
| JVM                 | Yes               | Yes                 |
| Uses Java libraries | Native            | Yes                 |

👉 **Scala runs on the JVM, just like Java**, and can directly use Java libraries.

------

## 2. The JVM (Very Important Concept)

Both Java and Scala run on the **JVM (Java Virtual Machine)**.

### How it works:

```
Java / Scala code
        ↓
    Bytecode (.class)
        ↓
        JVM
        ↓
  Windows / macOS / Linux
```

This means:

-   Same runtime
-   Same performance characteristics
-   Same deployment model

If you understand Java, learning Scala becomes much easier.

------

## 3. Installing the Environment (Basic Idea)

### Java

-   Install **JDK (Java Development Kit)**
-   Common versions: Java 8, 11, 17 (LTS)

Check installation:

```bash
java -version
javac -version
```

------

### Scala

Scala usually uses:

-   **Scala compiler**
-   **sbt** (Scala Build Tool)

Check installation:

```bash
scala -version
sbt sbtVersion
```

------

## 4. Your First Java Program

### Hello World in Java

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, Java!");
    }
}
```

### Key points:

-   `class` defines a class
-   `main` is the program entry point
-   Every statement ends with `;`

Compile and run:

```bash
javac Hello.java
java Hello
```

------

## 5. Your First Scala Program

### Hello World in Scala

```scala
object Hello {
  def main(args: Array[String]): Unit = {
    println("Hello, Scala!")
  }
}
```

### Or even shorter (Scala 3 / script style):

```scala
@main def hello() =
  println("Hello, Scala!")
```

### Key differences from Java:

-   Less boilerplate
-   `object` instead of `class` for singletons
-   Semicolons usually **not needed**

------

## 6. Variables and Types

### Java

```java
int x = 10;
String name = "Alice";
```

### Scala

```scala
val x: Int = 10     // immutable
var y: Int = 20     // mutable
val name = "Alice" // type inference
```

**Important concept in Scala**:

-   `val` → immutable (preferred)
-   `var` → mutable (use carefully)

------

## 7. Functions / Methods

### Java

```java
public static int add(int a, int b) {
    return a + b;
}
```

### Scala

```scala
def add(a: Int, b: Int): Int = a + b
```

Scala:

-   `return` is often omitted
-   Last expression is the return value

------

## 8. Classes and Objects

### Java Class

```java
class Person {
    String name;

    Person(String name) {
        this.name = name;
    }

    void greet() {
        System.out.println("Hello, " + name);
    }
}
```

------

### Scala Class

```scala
class Person(val name: String) {
  def greet(): Unit =
    println(s"Hello, $name")
}
```

Scala:

-   Constructor parameters are part of the class
-   String interpolation: `s"Hello, $name"`

------

## 9. Functional Programming (Scala’s Superpower)

Scala supports **functional programming**.

### Function as a value

```scala
val add = (a: Int, b: Int) => a + b
```

### Using `map`

```scala
val nums = List(1, 2, 3)
val doubled = nums.map(x => x * 2)
```

Result:

```scala
List(2, 4, 6)
```

Java can do this too (since Java 8), but Scala is designed for it.

------

## 10. When Should You Use Which?

### Choose Java if:

-   You are a complete beginner
-   You want maximum job opportunities
-   You work with Android or enterprise systems

### Choose Scala if:

-   You already know Java
-   You like functional programming
-   You work with big data (Spark, Akka)

👉 **Recommended path for beginners**:

>   **Java first → Scala second**

------

## 11. Common Beginner Mistakes

### Java

-   Forgetting `main`
-   Confusing `==` with `.equals()`
-   Too much boilerplate frustration

### Scala

-   Overusing advanced features too early
-   Writing “clever” but unreadable code
-   Ignoring immutability rules

------

## 12. Learning Roadmap

### Step 1: JVM Basics

-   Variables
-   Loops
-   Functions
-   Classes

### Step 2: Java Fundamentals

-   OOP
-   Collections
-   Exceptions

### Step 3: Scala Fundamentals

-   `val` vs `var`
-   Functions
-   Collections (`map`, `filter`)
-   Case classes

### Step 4: Advanced Topics

-   Concurrency
-   Functional patterns
-   Frameworks (Spring / Akka / Spark)

------

## 13. Summary

-   **Java**: stable, beginner-friendly, industry standard
-   **Scala**: powerful, concise, functional
-   Both run on the **JVM**
-   Learning Java first makes Scala much easier