# Enterprise Lang

Currently I'm collecting ideas for the worlds most enterprise language. Suggestions are welcome

- Rigorously OOP, but in the bad Java way
  - It forces you to use design patterns, even when it is completely unnecessary, or its a compile error.
  - There are no functions and methods are also objects.
  - You have to inherit the main class to make a program.
- Methods can be at most 20 lines long, if this is exceeded it is a hard error. It may seem like this is a generous number, but look at the hello-world example below, which is 16 lines.
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
- Everything must be in a namespace
- Everything attribute and method static by default, unless you specify with `Non-Static`
- Comments that aren't doc comments must start with `[No-Doc]`

## Examples

Hello world
```C#
//[No-Doc] These are mandatory!
Package com.example
Source-Code-File-Encoding-Format "UTF-8"
Package-Author-Full-Name "Jonh"

Use-Package org.enterprise-lang;
Use-Package org.enterprise-lang::String;
Use-Package org.enterprise-lang::String::UTF8-String;
Use-Package org.enterprise-lang::IO;
Use-Package org.enterprise-lang::IO::Printer;
Use-Package org.enterprise-lang::Runtime;
Use-Package org.enterprise-lang::Runtime::Main;
Use-Package org.enterprise-lang::Async;
Use-Package org.enterprise-lang::Async::Future;
Use-Package org.enterprise-lang::Language-Primitives;
Use-Package org.enterprise-lang::Language-Primitives::Void;

Private Namespace com::example::Main {
  Private Class com.example::Main::Main Inherits org.enterprise-lang::Runtime::Main {
    //[No-Doc] the initializer used by the default factory 
    Private Override Non-Static Default-Factory-Initializer () {}
    
    //[No-Doc] this method is very close to the maximal allowed length
    Private Override Non-Static Method org.enterprise-lang::Language-Primitives::Void com.example::Main::Main::Main () {
      //[No-Doc] construct the printer
      org.enterprise-lang::Async::Future<org.enterprise-lang::IO::Printer> printer-future = Call-Method (
        Get-Method "Default-Factory-Initializer" (
          Get-Default-Factory org-enterprise-lang::IO::Printer
        )
      ) ();
      Lock-Mutex printer-future;
      org.enterprise-lang::IO::Printer printer = Await-Future printer-future;
      Unlock-Mutex printer-future;
      
      //[No-Doc] construct the string from a string literal (which isn't printable on its own)
      org.enterprise-lang::Async::Future<org.enterpriese-lang::String::UTF8-String> hello-future = Call-Method (
        Get-Method "Default-Factory-Initializer" (
          Get-Default-Factory org.enterprise-lang::String::UTF8-String
        )
      ) ("Hello, World!");
      Lock-Mutex hello-future;
      org.enterpriese-lang::String::UTF8-String hello = Await-Future hello-future;
      Unlock-Mutex;
      
      //[No-Doc] print the string
      Lock-Mutex printer;
      Lock-Mutex hello;
      org.enterprise-lang::Async::Future<org.enterprise-lang::Language-Primitives::Void> print-result = Call-Method (
        Get-Method "Print-Line" printer
      ) (hello);
      Unlock-Mutex printer;
      Unlock-Mutex hello;
      
      //[No-Doc] void futures need to be awaited because they are lazily executed, but we don't need
      //[No-Doc] the return value of the future, so we must explicitly discard it.
      Lock-Mutex print-result;
      Discard(Await-Future print-result);
      Unlock-Mutex print-result;
    }
  }
}
```
