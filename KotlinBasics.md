# Kotlin Basics
Note that these are inferred from Kotlin Koans, with some examples directly from exercises.

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
Note that we can use callable references instead of lambdas in some cases. If we are referring to a field name, we could use Class::field in regular brackets instead of a lambda. See below for an example, ignoring the specific methods called, as they will be explained later:
```kotlin
customer.orders.flatMap(Order::products).maxBy(Product::price)
```

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

## Conventions

### Ranges
Ranges are implemented using `..`. An example are included below:
```kotlin
return date in firstDate..lastDate
```
The above returns whether or not a date is between the first and last date, noting that a class must implement `Iterable` to use ranges.

### Operator overloading
To overload an operator for a type, we add the `operator` prefix and create an extension function with the appropriate name. A couple of examples are below:
```kotlin
operator fun TimeInterval.times(number: Int) =
    RepeatedTimeInterval(this, number)

operator fun MyDate.plus(timeIntervals: RepeatedTimeInterval) =
    addTimeIntervals(timeIntervals.timeInterval, timeIntervals.number)
```
See [here](https://kotlinlang.org/docs/operator-overloading.html) for a list of the extension function names for all operators.

## Collections

### Introduction
Note that the Kotlin standard library has a lot of convenience functions for transforming collections. Most notably are `toSet()` and `toList()`.

### Sorting
There are four main methods for sorting in Kotlin, `sorted()`, `sortedBy()` (takes a lambda for the sorting parameter), `sortedDescending()`, and `sortedByDescending()` (also takes a lambda. An example is below, sorting strings.
```kotlin
val strings = listOf("bbb", "a", "cc")
strings.sorted() == listOf("a", "bbb", "cc")
strings.sortedBy { it.length } == listOf("a", "cc", "bbb")
strings.sortedDescending() == listOf("cc", "bbb", "a")
strings.sortedByDescending { it.length } == listOf("bbb", "cc", "a")
```

### Filtering and mapping
Filtering in Kotlin works exactly the same as in Java, providing a lambda, and retaining the elements that meet the filter.
Mapping involves obtaining a particular field for every element in the collection, similar to what is possible in Python. An example may be seen below, which retrieves all of the unique cities for all customers:
```kotlin
customers.map { it.city }.toSet()
```

### Predicates
Kotlin also has four *extremely* useful predicates, `all()`, `any()`, `count()`, and `find()`. All four take a lambda parameter. `all()` and `any()` return booleans, `count()` returns an Int, and `find()` returns a nullable object. Examples of each are included below:
```kotlin
// Return true if all customers are from a given city
fun Shop.checkAllCustomersAreFrom(city: City): Boolean =
        customers.all { it.city == city }

// Return true if there is at least one customer from a given city
fun Shop.hasCustomerFrom(city: City): Boolean =
        customers.any { it.city == city }

// Return the number of customers from a given city
fun Shop.countCustomersFrom(city: City): Int =
        customers.count { it.city == city }

// Return a customer who lives in a given city, or null if there is none
fun Shop.findCustomerFrom(city: City): Customer? =
        customers.find { it.city == city }
```

### Minimums and maximums
There are four main methods for this, `min()` and `max()`, which are fairly self-explanatory, and `minBy()` and `maxBy()`, which both take lambda parameters, allowing for specific sorting on fields of objects.

### Sums
Similarly, we have four main methods for summing: `sum()` and `sumDouble()`, as well as `sumBy()` and `sumByDouble()`, which both take lambda parameters, allowing for specific fields to be used in the sum operation.

### Grouping
Grouping is a very useful piece of functionality, allowing objects in a collection to be grouped by a particular field or evaluation on a field, resulting in a map of group keys to list values including all elements of the collection that match the key. An example is included below:
```kotlin
val result = listOf("a", "b", "ba", "ccc", "ad").groupBy { it.length }
result == mapOf(
    1 to listOf("a", "b"),
    2 to listOf("ba", "ad"),
    3 to listOf("ccc"))
```

### Association
Like grouping, association is used to build maps, and there are three varieties of association included in Kotlin, which all take lambda parameters:
- `associateBy()`, which maps the provided parameter to the elements that match it.
- `associateWith()`, which maps the elements to the provided parameter.
- `associate()`, which maps the first provided parameter to the second one. This variety makes use of the `to` keyword, denoting which parameter maps to which.
More fullsome examples are included below:
```kotlin
// Build a map from the customer name to the customer
fun Shop.nameToCustomerMap(): Map<String, Customer> = customers.associateBy { it.name }
// Build a map from the customer to their city
fun Shop.customerToCityMap(): Map<Customer, City> = customers.associateWith { it.city }
// Build a map from the customer name to their city
fun Shop.customerNameToCityMap(): Map<String, City> = customers.associate { it.name to it.city }
```

### Partitioning
In Kotlin, collections may be partitions into two sub-collections based on a lambda parameter boolean condition. An example is included below:
```kotlin
val numbers = listOf(1, 3, -4, 2, -11)
val (positive, negative) = numbers.partition { it > 0 }
positive == listOf(1, 3, 2)
negative == listOf(-4, -11)
```

### Flattening
For collections of elements which in themselves also have a collection as a field, we may use flattening, which in essence, returns a list of all of the elements in the collections of the objects' fields.
For example, if we have a List of Customers, and each Customer has a List of Orders, and each Order has a list of Products, we can use flattening to retrieve a list of all of the products ordered by all Customers, as seen in the below snippet:
```kotlin
customers.flatMap(Customer::orders).flatMap(Order::products).toSet()
```

### Folding and reducing
Generally speaking, folding and reducing both execute similar operations, applying a particular operation to each element in a collection.
Folding takes an initial value and uses it as the accumulated value on the first/next element, while reducing will use the first element as the initial value on the first two elements.
These sort of operations are most suited to specific aggregations, and more detail about them may be seen [here](https://kotlinlang.org/docs/collection-aggregate.html#fold-and-reduce).

## Properties

### Custom getters and setters
Properties in Kotlin may have custom getters and setters set. An example of the syntax may be seen below:
```kotlin
var stringRepresentation: String
    get() = this.toString()
    set(value) {
        setDataFromString(value) // parses the string and assigns values to other properties
    }
```
A more useful example for a custom setter is the following:
```kotlin
var propertyWithCounter: Int? = null
    set(value) {
        field = value
        ++counter
    }
```
Note that if we are modifying the field in question in a setter, we must use the `field` keyword.

### Lazy properties
We can make a property lazy-loaded by appending the `by lazy` keyword to the end of its declaration, along with a lambda specifying what occurs when the variable is loaded.
A full class example may be seen below:
```kotlin
class LazyProperty(val initializer: () -> Int) {
    val lazyValue: Int by lazy {
        initializer()
    }
}
```
Of course, we don't have to use a function in the lazy initialisation process; we could instead have just set the value to a constant.
