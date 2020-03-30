# Java 8 new features



## Default methods

**Important Points:**

1. Interfaces can have default methods with implementation in Java 8 on later.
2. Interfaces can have static methods as well, similar to static methods in classes.
3. Default methods were introduced to provide backward compatibility for old interfaces so that they can have new methods without affecting existing code.



## Lambda expressions

lambda expressions are added in Java 8 and provide below functionalities.

- Enable to treat functionality as a method argument, or code as data.
- A function that can be created without belonging to any class.
- A lambda expression can be passed around as if it was an object and executed on demand.

![lambda expression](https://media.geeksforgeeks.org/wp-content/uploads/lambda-expression.jpg)

**Important points:**

- The body of a lambda expression can contain zero, one or more statements.
- When there is a single statement curly brackets are not mandatory and the return type of the anonymous function is the same as that of the body expression.
- When there are more than one statements, then these must be enclosed in curly brackets (a code block) and the return type of the anonymous function is the same as the type of the value returned within the code block, or void if nothing is returned.



## Java method references

| Type                                                         | Syntax                            | Lambda                                                       |
| ------------------------------------------------------------ | --------------------------------- | ------------------------------------------------------------ |
| Reference to a *static method*                               | `ClassName`::`staticMethodName`   | (`args`) -> `ClassName`.`staticMethodName`(`args`)           |
| Reference to *an instance method of an existing object*      | `object`::`instanceMethodName`    | (`args`) -> `object`.`instanceMethodName`(`args`)            |
| Reference to an instance method of an arbitrary object of a particular type | `ClassName`::`instanceMethodName` | (`arg0`,`rest`)  -> `arg0`.`instanceMethodName`(`rest`) Note: `argo` is of type `ClassName` |
| Reference to a constructor                                   | `ClassName`::`new`                | (`args`) -> new `ClassName`(`args`)                          |