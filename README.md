# Enterprise Lang

Currently I'm collecting ideas for the worlds most enterprise language. Suggestions are welcome

- Rigorously OOP, but in the bad Java way
  - It forces you to use design patterns, even when it is completely unnecessary, or its a compile error.
  - There are no functions and methods are also objects.
  - You have to inherit the main class to make a program.
- Methods can be at most 5 lines long, if this is exceeded it is a hard error
- Every file must start with a package name and a copyright statement, or it is a hard error
  - The package name must be a real domain, this is looked up at compile time and it is a hard error if the request fails
- Everything is private, including methods and classes.
  - To call a method you need to use the special `Get-Method` keyword, which returns a method object.
  - Closures are just objects, you need to use the `Call-Method` keyword to acutally evaluate them.
  - Even though everything is private, you must specify `Private` for every single thing.
  - To construct an object, you need to use the keyword `Get-Default-Factory` to obtain its constructor, which you then need to call with `Call-Method`.
- To get a property you need to first get the method, then get the property
- Every attribute has a mutex that needs to be locked and unlocked explicitly
- Every method call returns a future that must be awaited. To await a future, you need to of course first get the await method.
- Operators exist, but they work just like any other method :)
- The standard library is purposefully extremely deep nested and verbose.
- Everything uses `Kebab-Camel-Case` because why not.
- Names must always be fully qualified
- Packages must be imported one layer at a time, yet, they must still be fully qualified

## Examples

Hello world
```java
// These are mandatory!
Package com.example
Source-Code-File-Encoding-Format "UTF-8"
Package-Author-Full-Name "Jonh"

Use-Package org;
Use-Package org::enterprise-lang;
Use-Package org::enterprise-lang::std;


Private Class com::example::Main 


```
