## Introduction

This document is here as a soundboard for ideas and learnings around the `sparklyr` package.

## Data Types

sparklyr has a function named [`sdf_schema()`](https://www.rdocumentation.org/packages/sparklyr/versions/0.7.0/topics/sdf_schema) for exploring the columns of a tibble on the R side. The return value is a list, and each element is a list with two elements, containing the name and data type of each column.

Here is a comparison of how R data types map to Spark data types. Other data types are not currently supported by `sparklyr`.

| R type | Spark type |
---------|-------------
| logical | BooleanType |
| numeric | DoubleType |
| integer | IntegerType |
| character | StringType |
| list | ArrayType |

`sparklyr` [doesn't currently have the ability](https://github.com/rstudio/sparklyr/issues/1324) to pass over more complex data types such as a `List[String]`. 

### Using other data types

When passing an R `list` over to Scala, we get a Scala `ArrayType` and there is no current way to send a Scala `List` from R using `sparklyr`. However, some of our Scala functions require `List` inputs. Potential solutions to this issue are:

1. Use `Seq` instead of `List` as the input type since `Array` has also the `Seq` trait in Scala, so everything works out-of-the-box.
2. Use overloading, which allows us to define methods of same name but having different parameters or data types, though this [has issues](https://stackoverflow.com/questions/2510108/why-avoid-method-overloading). For an example of how this works, see [this link](https://www.javatpoint.com/scala-method-overloading).
3. Define a new Scala method for the same class that is called from R, which effectively invokes the `toList` function on the `ArrayType` and then calls the existing Scala method.


## What is a `static` method?

A `static` method is one type of method which doesn't need any object to be initialized for it to be called. For instance, here’s an example of a method named `increment` in a Scala object named `StringUtils`:

```scala
object StringUtils {
  def increment(s: String) = s.map(c => (c + 1).toChar)
}
```

Because it’s defined inside an object (not a class), the `increment` method can be called directly on the `StringUtils` object, without requiring an instance of `StringUtils` to be created:

```scala
scala> StringUtils.increment("HAL") 
res0: String = IBM
```

In fact, when an object is defined like this without a corresponding class, you can’t create an instance of it. This line of code won’t compile:

```scala
val utils = new StringUtils
```