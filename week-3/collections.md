# Introduction

In this chapter you’ll learn about Java’s **Collections Framework** — a set of flexible, resizable data structures that go far beyond the fixed-size arrays you’ve used so far. Instead of being limited to a single fixed-length array, you’ll work with lists, sets, and maps — each designed for different use cases.

Almost every backend endpoint you build will involve collections: returning a list of users, looking up a product by ID in a map, ensuring a set of unique tags. Mastering these structures now prepares you for the REST APIs you’ll build in Week 4.

**Pre-requisites:**

- You’re familiar with Javascript arrays and sets from the Core Program and you’ve learned the Java equivalent in [Week 1 - Arrays](https://www.notion.so/Arrays-32150f64ffc980708541eb0953f07247?pvs=21) (fixed-size arrays).
- Polymorphism from [Week 2 - Polymorphism](https://www.notion.so/Polymorphism-32150f64ffc980c79df8ea52ff497bea?pvs=21) and [Week 2 - Interfaces](https://www.notion.so/Interfaces-2af50f64ffc981d69a9aca0964eb464e?pvs=21). Revisit these concepts if needed—they're essential for understanding the hierarchical structure of Java's Collections Framework.

# Collections Hierarchy

The Collections Framework provides these data structures as classes bundled in packages in your standard JDK installation.

<aside>
💡

JDK is the SDK for Java (Java Development Kit) and it is really a development kit for your application needs.

</aside>

The most frequently used collections are `ArrayList`, `HashSet`, and `HashMap`. Each implements an interface above it in the hierarchy. To understand the Collections Framework, we need to briefly cover these interfaces. Take a look at the image below:

![Diagram of ArrayList,HashSet and HashMap classes’ ancestor interfaces](attachment:2f850049-27e8-438b-b33f-bea03022ea52:ch2.png)

Diagram of ArrayList,HashSet and HashMap classes’ ancestor interfaces

In the diagram above, `Iterable` sits at the top with the most abstract functionality. As you move down the hierarchy, classes become more specific and provide more functionality.

This hierarchical structure offers many advantages. Here are a couple:

- **Predictability:** When ancestors provide default functionality, you can assume all descendants have it too.
- **Write less code:** By coding to an ancestor interface, you can assume your code works for all of its descendants.

We'll start this chapter at the top with [Iterable.java](https://www.notion.so/Collections-2af50f64ffc9818497aaf84d77d01075?pvs=21), then move to [Collection.java](https://www.notion.so/Collections-2af50f64ffc9818497aaf84d77d01075?pvs=21) → [List.java](https://www.notion.so/Collections-2af50f64ffc9818497aaf84d77d01075?pvs=21) and [Set.java](https://www.notion.so/Collections-2af50f64ffc9818497aaf84d77d01075?pvs=21) → implementations of Lists and Sets (`ArrayList` and `HashSet`). We'll follow the same path for [Map.java](https://www.notion.so/Collections-2af50f64ffc9818497aaf84d77d01075?pvs=21) and its main implementation `HashMap`, which have their own ancestry but remain similar to Sets and use Set APIs for comparable functionality.

## Iterable.java

`Iterable` is an interface that enforces a contract for a few basic methods. Classes implementing this interface allow their instances to be used in for-each loops, which you learned about in [Week 1 - Control Flow](https://www.notion.so/Control-Flow-32150f64ffc980b092a8eeadee84f661?pvs=21).

To implement this interface, classes have to implement `iterator()`. The interface's other methods are already implemented with a workable default:

![Iterable has 2 other methods that are left out as they’re implemented by default](attachment:27fb1ab9-fcf1-490c-8dcc-72481d622c75:fb2ae3fd-f109-495b-803f-cfc7bb2b741e.png)

Iterable has 2 other methods that are left out as they’re implemented by default

As you can see `iterator()` must return an `Iterator` instance. That in itself is an interface that requires `next()`, `hasNext()` :

![Iterator’s `forEachRemaining()` and `remove()` methods are left out as they’re not required](attachment:ae4706b3-c71d-4b8d-87a1-99f85d26ac19:cac48fb2-92ee-49f7-9bd6-68d771381400.png)

Iterator’s `forEachRemaining()` and `remove()` methods are left out as they’re not required

Zooming out, we can summarize that making a class work with for-each loops requires implementing two things:

- The `iterator()` method on the `Iterable` interface, which returns an `Iterator`.
- The `next()` and `hasNext()` methods on that `Iterator` — `next()` to retrieve the current element and advance, and `hasNext()` to know whether there are more elements (preventing infinite loops or out-of-bounds errors).

Can you notice how this is the bare minimum for looping? That's not a coincidence—Iterable sits at the top of the hierarchy; therefore, it should be the most general.

## Collection.java

Iterable is very raw and rarely implemented directly. It's usually extended by other interfaces like `Collection`.

`Collection` extends Iterable with additional methods such as `contains()`, `equals()`, `add()`, and `remove()`. By implementing these methods, classes gain access to useful APIs that operate on Collections—like the Stream API, which you'll learn about in [Stream APIs](https://www.notion.so/Stream-APIs-32150f64ffc9801c92f3d11a71e0aa98?pvs=21) chapter.

<aside>
⌨️

**Hands-on:** Find the Collection interface in your Java installation. First, locate your Java/JDK installation directory.

On my machine (macOS), the Java installation directory is: `/Users/Ali/Library/Java/azul-25.0.2`. Below are instructions for different platforms:

- macOS
  Java installation directory (if installed through IDE): `/Users/[YourUserName]/Library/Java/azul-25.x.x`
  The Collection interface is located at: `{java-install-directory}/Contents/Home/lib/src.zip/java.base/java/util/Collection.java`
- Windows
  Java installation directory (if installed through IDE): `C:\Users\[YourUserName]\.jdks\azul-25.x.x`
  The Collection interface is located at: `{java-install-directory}\lib\src.zip\java.base\java\util\Collection.java`

Take a look at the interface. Don't worry about understanding everything—just explore and find questions to ask 🙂

There's a much easier way to read this file directly in your IDE. You rarely need to navigate to it manually, but it helps to understand the structure first.

</aside>

Just like the Iterable interface, the Collection interface is still quite general and is rarely implemented directly; it is again extended by other interfaces as seen in the [Diagram of Collection Hierarchy](https://www.notion.so/Collections-2af50f64ffc9818497aaf84d77d01075?pvs=21) above.

Let’s cover the two main extensions of Collection, `List` and `Set` interfaces.

## List.java

Lists are ordered collections that allow duplicate elements. The main implementations are `ArrayList` and `LinkedList`.

We'll focus only on `ArrayList` here. `LinkedList` offers the same functionality but works differently under the hood. Think of `ArrayList` as a numbered row of seats — finding seat #50 is instant (O(1)), but adding a seat in the middle means shifting everyone down. `LinkedList` is more like a chain where each link points to the next — inserting a new link is easy, but finding the 50th link means walking the chain from the start (O(n)). In most backend work, `ArrayList`'s fast random access makes it the better default choice.

<aside>
❗

"Ordered" should not be confused with "Sorted". Ordered means the collection tracks where each element sits within the collection. In plain English: there's an index for each element, so you can retrieve, add, update, or remove elements precisely using an index rather than looping through the entire array.

</aside>

### ArrayList.java

ArrayList is a direct implementation of the List interface. Since it's a fully implemented class, we can create an instance of (AKA, instantiate) and directly use it in our code.

**Initializing an ArrayList and adding elements:**

<aside>
💡

`IO.println` is a utility shorthand that works like `System.out.println()`. We use it throughout these examples for brevity. If your project doesn’t have an `IO` class, simply use `System.out.println()` instead.

</aside>

```java
void main() {
    List<String> groceries = new ArrayList<String>();

    groceries.add("Croissant");
    groceries.add("Espresso");

    IO.println(groceries);
}
```

Notice how we assign the new ArrayList instance to a variable of type `List`? It's good practice—now we can work with our array through the List interface, and if we later need a different List implementation, we simply change the initialization statement. All code expecting a List, like `groceries.add("Croissant")` continues to work.

**Read:** We can retrieve elements from the ArrayList using an index: `IO.println(groceries.get(0))` outputs `Croissant`.

**Update:**

```java
    groceries.set(0, "Choco-Croissant");
    IO.println(groceries);
```

**Delete:**

```java
    groceries.remove(1);
    IO.println(groceries);
```

<aside>
⌨️

**Hands on:** Modify the grocery list code above to add 3 more items, then print the size of the list using `groceries.size()`.

</aside>

**Breakdown of ArrayList:**

ArrayList signature in ArrayList.java is:

```java
public class ArrayList<E> extends AbstractList<E> implements List<E>, RandomAccess, Cloneable, java.io.Serializable
```

- `ArrayList<E>`: means it is a class that takes a type `E` which is just a placeholder name for a type. Most likely the placeholder was named `E` for element-type. When we instantiated the array above, we decided that its element-type would be `String` in the statement `new ArrayList<String>` . You’ll learn much more about this in the [Week 3 - Generics](https://www.notion.so/Generics-2af50f64ffc9818fb3b3d1c7deb2e2df?pvs=21) chapter.
- `extends AbstractList<E>`: AbstractList is an abstract class that implements some convenient methods for List. It is not complete (Abstract) and can’t be instantiated on its own.
- `implements List<E>`: Direct implementation of List to allow list functionality
- Other `implements`: Will not be covered here but they allow other functionalities

Since `<E>` is already known to be a `String` at the declaration part of our statement, before the equal sign in `List<String> groceries = new ArrayList<String>()` , we no longer need to add it at the second part and that’ll be inferred by the compiler so `List<String> groceries = new ArrayList<>()` is equally valid.

## Set.java

Sets are collections that contain no duplicate elements.

### HashSet.java

`HashSet` is the main implementation of `Set`. It’s called `HashSet` because it uses a **hash table**—an internal structure that converts each value into a numeric code (a "hash") for near-instant lookup. Think of it like a library filing system: instead of scanning every shelf, the librarian calculates exactly which shelf a book belongs on from its title. That calculation is the "hash," and the shelving system is the "hash table." This gives `HashSet` O(1) performance for `add()`, `contains()`, and `remove()`. `HashSet` doesn’t guarantee an order, which means there’s no index for retrieving elements.

`HashSet` is a fully implemented class that you can instantiate and directly use in your code:

**Initializing a set and adding elements:**

```java
void main() {
    Set<Integer> userIds = new HashSet<>();

    userIds.add(40012);
    userIds.add(30001);
    userIds.add(40011);

    IO.println(userIds);
}
```

**Read:** Since sets have no index (like `ArrayList`) and no key (like `HashMap`), there’s no `get()` method for retrieving a specific element.

However, reading sets should be thought of differently than for lists. When reading sets, you shouldn’t need to retrieve by index, if you do, then you’re using the wrong collection. To give one use case as an example:

```java
void main() {
    Set<Integer> allowedUserIds = new HashSet<>();

    allowedUserIds.add(40012);
    allowedUserIds.add(30001);
    allowedUserIds.add(40011);

    String response = allowedUserIds.contains(40011) ? "User is allowed" : "User is not allowed";
    IO.println(response);
}
```

**Update/Delete:**

Sets also don’t provide `set()` method like lists. So updates should be thought of as removing and adding an element.

```java
userIds.remove(40011);
userIds.add(20011);
IO.println(userIds);
```

**Breakdown of HashSet:**

Signature is almost the same as `ArrayList`

```java
public class HashSet<E> extends AbstractSet<E> implements Set<E>, Cloneable, java.io.Serializable
```

No breakdown needed—just notice that it implements the `Set` interface. Also, `AbstractSet` is like `AbstractList`: an abstract class with convenience methods for sets.

💬 When would you choose a `Set` over a `List`? Think of a real-world example where duplicates would be a problem.

- Answer
  - **Notifications’ system:** You never want to notify a user twice so you’d store their emails, userIds and so on in a `Set`.
  - **Content tags:** Duplicate tags can become a problem and be missed if your content has too many tags.
  - **Deduplicating data:** Let’s say you have identical data types coming from multiple external APIs. Then you’d need to deduplicate your data to ensure you have a complete list without repetition.

## Map.java

`Map` is an interface of its own. It doesn't extend the `Collection` interface, but it can be transformed into a `Set` when iteration is needed. Maps and sets share similar characteristics.

Maps are key-value dictionaries. Think of them as two-column tables: the first column holds keys, the second holds values, and each row represents an element. You choose the types for both keys and values during instantiation.

### HashMap.java

`HashMap` is the main implementation of `Map`. It is a complete class that you can use directly in your code.

**Initializing a map and adding elements:**

```java
void main() {
    Map<Integer, String> userEmails = new HashMap<>();

    userEmails.put(40010, "user1@mail.com");
    userEmails.put(40012, "user2@mail.com");
    userEmails.put(30001, "user3@mail.com");

    IO.println(userEmails);
}
```

**Read:** Reading from a map is straightforward. Use the `get()` method with your key: `userEmails.get(40012)`.

- For membership checks, use `containsKey()` and `containsValue()`. Example: `userEmails.containsValue("user1@gmail.com")`
- You can retrieve all keys or values using `userEmails.keySet()` (returns a `Set`) and `userEmails.values()` (returns a `Collection`).
  - To iterate over both keys and values together, use `userEmails.entrySet()`. This wraps your map entries in a `Set`. See below for how to iterate over entry sets.

**Update:** Maps don't provide a `set()` method like lists do. Instead, calling `put()` on an existing key updates that entry:

```java
void main() {
    Map<Integer, String> userEmails = new HashMap<>();

    userEmails.put(40010, "user1@mail.com");
    userEmails.put(40012, "user2@mail.com");
    userEmails.put(30001, "user3@mail.com");

    // Update user 40012's email
    userEmails.put(40012, "VIPUser001@mail.com");

    IO.println(userEmails);
}
```

**Delete:** Use the `remove()` method with a key. For example: `userEmails.remove(40012)`. If an element is found and removed, the method returns its value. Otherwise, it returns `null`.

**Iterating over a map:**

Use the `entrySet()` method to iterate over a map's elements. This returns a set of `Map.Entry<K,V>` elements, where `K` and `V` match your map's key and value types. You'll learn more about this typing in the [Generics](https://www.notion.so/Generics-2af50f64ffc9818fb3b3d1c7deb2e2df?pvs=21) chapter. For now, note how Java handles maps as a set of complex `Map.Entry` types. Take a look at the code below:

```java
void main() {
    Map<Integer, String> userEmails = new HashMap<>();

    userEmails.put(40010, "user1@mail.com");
    userEmails.put(40012, "user2@mail.com");
    userEmails.put(30001, "user3@mail.com");

    for (Map.Entry<Integer, String> entry : userEmails.entrySet()) {
        IO.println("User " + entry.getKey() + " has email: " + entry.getValue());
    }
}
```

Maps can be thought of as sets with a richer API that provides dictionary-like functionality. What you're seeing is abstraction in action—a more complex type, `Map`, built by customizing what's already available under the hood.

**Breakdown of HashMap:**

Signature in HashMap.java is:

```java
public class HashMap<K,V> extends AbstractMap<K,V> implements Map<K,V>, Cloneable, Serializable
```

- `HashMap<K,V>`: means it is a class that takes 2 types; K for keys and V for values. That’s why we initialized it with `new HashMap<Integer,String>`. Could’ve been other types as well
- `extends AbstractMap<K,V>`: Just like `AbstractList` in `ArrayList`, an abstract class with convenience methods that isn't complete enough to be used on its own
- `implements Map<K,V>`: Direct implementor of the `Map` interface which guarantees at least `Map` functionality
- Other `implements`: Will not be covered here but they allow other functionalities

💬 What real-world data naturally fits a key-value structure? Think of examples from everyday life or from applications you use.

- Answer
  - **Phonebook**. K: Person’s name + Postcode | V: Phone number
  - **Employee catalog:** K: Employee ID number | V: Class holding employee information such as name, department, etc…
  - `.json` data can also be nicely represented as a `Map<String,String>`. In fact some JSON parsing libraries do that under the hood.
  ```json
  {
    "orderId": "1001",
    "customerName": "Alice Brown",
    "status": "active"
  }
  ```

# Choosing the right collection

When choosing the right collection, the answer is usually clear. However, if you want scalable code that doesn’t break under heavy load, you need to think more carefully about your choices. You’ve seen Big O notation before — let’s use it to compare the performance of the collections you’ve just learned.

Start by sorting the collections by their complexity. Go through them one by one until you find one that solves the problem. The table below compares their features and Big O performance for common operations:

|               | **Duplicates**          | **Ordered** | **add()**  | **get / contains** | **remove()** |
| ------------- | ----------------------- | ----------- | ---------- | ------------------- | ------------ |
| **HashSet**   | ❌                      | ❌          | O(1)       | O(1) `contains`     | O(1)         |
| **ArrayList** | ✅                      | ✅          | O(1)*      | O(1) by index, O(n) `contains` | O(n) |
| **HashMap**   | Keys: ❌ / Values: ✅   | ❌          | O(1)       | O(1) by key         | O(1) by key  |

\* *O(1) amortized — occasionally the internal array needs to be resized, which costs O(n), but this is rare enough that the average cost per operation stays O(1).*

For each capability a collection provides, there’s a trade-off. By choosing the simplest collection that solves the problem, you balance advantages and disadvantages. Sometimes it gets deeper than that but the idea is the same.

💬 What are some drawbacks you can think of for a collection that allows duplicates such as `ArrayList`?

- Answer
  - Slower membership checks: `ArrayList.contains()` is O(n) — it must scan every element. `HashSet.contains()` is O(1). For a collection of 10 items this barely matters, but for 100,000 items it’s the difference between instant and sluggish.
  - Similarly, `ArrayList.remove(Object)` is O(n) because it has to find the element and then shift all subsequent elements. `HashSet.remove()` is O(1).

# Iterating over collections

You’ve seen how to iterate over maps so far. Here’s a less verbose way to do that using the Java 10+ keyword `var`. Use `var` when the type is obvious from the right-hand side of the assignment; prefer explicit types when clarity matters.

```java
void main() {
    Map<Integer, String> userEmails = new HashMap<>();

    userEmails.put(40010, "user1@mail.com");
    userEmails.put(40012, "user2@mail.com");

    for (var entry : userEmails.entrySet()) {
        IO.println("User " + entry.getKey() + " has email: " + entry.getValue());
    }
}
```

Even less verbose:

```java
void main() {
    var userEmails = new HashMap<Integer, String>();

    userEmails.put(40010, "user1@mail.com");
    userEmails.put(40012, "user2@mail.com");
    userEmails.put(30001, "user3@mail.com");

    userEmails.forEach((k,v) -> IO.println("User " + k + " has email: " + v));
}
```

Quite a few ways to loop over elements. Better to opt for the enhanced for-loop, even IntelliJ recommends that when you write **traditional loops** such as

```java
for (int i = 0; i < groceries.size(); i++) {
    IO.println("Grocery item: " + groceries.get(i));
}
```

**So better would be either of the two below:**

```java
// forEach method in collections
groceries.forEach(item -> IO.println("Grocery item: " + item));

// Enhanced for loop (for-each loop)
for (String item : groceries) {
    if (item.equals("Croissant")) {
        IO.println(item);
    }
}
```

<aside>
⚠️

When comparing collection elements, always use `.equals()` instead of `==`. In Java, `==` compares object references (memory addresses), not values. For example, `"hello" == new String("hello")` is `false`, but `"hello".equals(new String("hello"))` is `true`. This is one of the most common beginner mistakes in Java.

</aside>

# Exercise

Solve a problem using each collection type.

## `HashSet` Exercise

**Problem:** Create a program that removes duplicate words from a list of words entered by a user.

**Requirements:**

- Create a `HashSet` to store unique words
- Read a sentence or multiple words from user input
- Split the input into individual words
- Add each word to the `HashSet` (this automatically removes duplicates)
- Print out all unique words

**Example:**

```java
// Input: "hello world hello java world programming"
// Output: [hello, world, java, programming]
// Note: HashSet does not guarantee order, so your output may appear in a different order.
```

## `ArrayList` Exercise

**Problem:** Create a simple todo list application where users can add tasks, mark them as complete, and view all tasks.

**Requirements:**

- Use an `ArrayList` to store todo items as `String`
- Implement methods to:
  - Add a new task
  - Remove a task by index
  - Display all tasks with their index numbers
  - Get the total number of tasks
- Create a simple menu system that allows users to choose operations

**Bonus:** Extend the program to store task objects with properties like description, priority, and completion status.

## `HashMap` Exercise

**Problem:** Build a simple student grade management system that stores student IDs and their corresponding grades.

**Requirements:**

- Create a `HashMap` with `String` keys (student IDs) and `String` values (grades)
- Add at least 5 students with their grades
- Implement functionality to:
  - Look up a student's grade by their ID
  - Update a student's grade
  - Display all students and their grades
  - Find and display all students with a specific grade (e.g., all students with grade "A")
- Handle the case when a student ID is not found

**Bonus:**

- Generate a random unique ID for each student added.

**Example output:**

```java
Student 10001: A
Student 10002: B
Student 10003: A
Student 10004: C
Student 10005: B

Students with grade A: [10001, 10003]
```
