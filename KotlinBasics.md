# Kotlin Basics
Note that these are inferred from Kotlin Koans.

## Introduction

### Nullable types
Types can be made nullable by adding a ? after the type. When checking attributes of a nullable type, we can use the following syntax:
```kotlin
client?.personalInfo?.email
```

### Nothing return type
Functions that always throw an exception (not that they'd be particularly common) have the `Nothing` return type.

### Lambdas
In Kotlin, lambdas can be supplied as function parameters, and when calling the function, rather than including the lambda between two brackets, we have a space after the function name and use braces. The following syntax is used: 
```kotlin
collection.any { it % 2 == 0 }
``` 
This function returns whether any elements of the collection are even. Note that if a lambda only has one parameter, the Kotlin convention is to refer to the object as `it`.

## Classes

### Smart casts
Kotlin has more streamlined `instanceof`-like checks using the `when` operator. See below for an example:
```kotlin
when (expr) {
    is Num -> expr.value
    is Sum -> eval(expr.left) + eval(expr.right)
    else -> throw IllegalArgumentException("Unknown expression")
}
```
Also note that Kotlin is smart enough to automatically cast the when parameter to its appropriate type in the `is` branch.

### Sealed classes
Sealed classes are like abstract classes or interfaces, but they specify that there can be no more subclasses or implementations added after compilation. As such, if we are doing a when check like above, we do not need the else check if we are covering all possibilities.

### Named imports
In Kotlin, we can name imports. This is particularly useful in cases where we are importing classes and functions with identical names.

### Extension functions
Extension functions are function declarations on particular types. For example, we can declare a new function that can apply to all Ints. An example can be seen below:
```kotlin
fun Int.extension(Int i1, Int i2): String ...
```
