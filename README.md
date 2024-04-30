# Enterprise Lang

This is a joke programming language that pokes fun at "enterprise software", especially when it is written in Java, C# or PowerShell.

## Enterprise-Lang™ by Example

Let's take some pseudo-code (Python) and slowly transform it into Enterprise-Lang™ code, this will guide us through the basic differences between a popular language like Python and Enterprise-Lang™:

```python
# displays "Hello, World!" on the terminal
print("Hello, World!")
```

The first issue here is that Enterprise-Lang™ has an entirely different syntax for calling methods. Let's re-write the above using the correct syntax:
```powershell
# displays "Hello, World!" on the terminal
Call-Method Print "Hello, World!";
```
Here, we have a method called `Print`, and in order to invoke it, we need to pass it to the `Call-Method` built-in command.

Unfortunately, this is not the only problem here. Enterprise-Lang™ does not provide a simple `Print` function, because there are several ways in which printing can occur, for instance you may want to print to STDOUT or you may want to log to a file. It is bad practice™ to introduce this kind of tight-coupling, so the language prohibits it. We have to instantiate a `Printer` class first, but `Printer` is an abstract class, as such it cannot be instantiated directly, instead we need to hide the implementation of `Printer` by casting a concrete class to the bastract class `Printer`. There are several such printers, in our case we have to use the `STDIOPrinterFactory` class to instantiate our printer. These are all conveniently provided in the standard package `org.enterprise-lang::io`. Our factories have a method called `Create`, which returns the instance of the abstract super-class `Printer`. This is called the _factory pattern_, and you should get familiar with it in order to program in Enterprise-Lang™.

```powershell
Use-Package org.enterprise-lang::io::STDIOPrinterFactory;
Use-Package org.enterprise-lang::io::Printer;

STDIOPrinterFactory printer-factory = # TODO: you will see :)
Printer printer = Call-Method printer-factory Create;
Call-Method printer Print "Hello, World!";
```

Much more maintainable™, don't you think? Wrong! It's terrible! What if we wanted to provide multiple versions of the `Create` and `Print` methods in the future? If we changed them now, it would break our code! The solution is simple, there is an additional layer of de-coupling when calling a method that lets the method implementer define new versions of the same method, and let the user pick whichever version they want to use, this lets us make breaking changes to the method safely! In order to do so, we use another built-in command called `Get-Method`, which returns the method given its name and version. Method versions have the SemVer number corresponding to the version of the package we refer to.

```powershell
# ...
Printer printer = Call-Method (Get-Method printer-factory "Create" "1.0.0");
Call-Method (Get-Method "Print" "1.0.0") "Hello, World!";
```

That's great! But we left out a little detail: we need to instantiate the `STDIOPrinterFactory`. Remember that we want to decouple the implementation of a class from its interface, so again, the language forces us to use the factory pattern. In this case, the class is already as concrete as it gets, so it doesn't really have multiple implementations, but we must remain open to the possibility that such different implementations may arise as the requirements change™, and so to construct a concrete class, we must use its _default factory_, which every concrete class provides, of course. We can get the default factory with the `Get-Default-Factory` built-in command.

```powershell
# ...
STDIOPrinterFactory::DefaultFactory printer-factory-factory = Get-Default-Factory STDIOPrinterFactory;
STDIOPrinterFactory printer-factory = Call-Method (Get-Method printer-factory-factory "Create" "1.0.0");
```

Let's write what we have so far in full:
```powershell
Use-Package org.enterprise-lang::io::STDIOPrinterFactory;
Use-Package org.enterprise-lang::io::Printer;

# create the printer factory
STDIOPrinterFactory::DefaultFactory printer-factory-factory = Get-Default-Factory STDIOPrinterFactory;
STDIOPrinterFactory printer-factory = Call-Method (Get-Method printer-factory-factory "Create" "1.0.0");

# create the printer
Printer printer = Call-Method (Get-Method printer-factory "Create" "1.0.0");

# print "Hello, World!"
Call-Method (Get-Method printer "Print" "1.0.0") "Hello, World!";
```

This may look a bit daunting, but it is much more maintainable™, compared to what we started out with! You will see the benefits of this architecture as projects inevitably scale and become more complex as the requirements change™.

This example, however, contains a glaring bug, do you see it? That's right! What it we are running _hello world_ on a device of a non-English-speaking person? The text would appear to be in English, despite their system language not being set to English! The `Printer` function is actually a little bit more complex than this, as it needs to be able to adapt to situations like this, by allowing us to change the _locale_. We can do so by passing a `LocalizedString` object, instead of our disgusting and tightly-coupled string literal. The `LocalizedString` lets us add different versions based on the locale of the user. In our case we will use the "ENG" locale for English and the "ITA" locale for Italian as an example. Again we will need to use the factory method to construct abstract `Locale` objects, for our "ENG" and "ITA" locales.

```powershell
# ...
Use-Package org.enterprise-lang::locale::Locale;
Use-Package org.enterprise-lang::locale::factories::ENG;
Use-Package org.enterprise-lang::locale::factories::ITA;
Use-Package org.enterprise-lang::string::LocalizedString;
# ...

# create the locales
ENG::DefaultFactory eng-locale-factory-factory = Get-Defaut-Factory ENG;
ENG eng-locale-factory = Call-Method (Get-Method eng-locale-factory-factory "Create" "1.0.0");
Locale eng-locale = Call-Method (Get-Method eng-locale-factory "Create" "1.0.0");
ITA::DefaultFactory ita-locale-factory-factory = Get-Defaut-Factory ITA;
ITA ita-locale-factory = Call-Method (Get-Method ita-locale-factory-factory "Create" "1.0.0");
Locale ita-locale = Call-Method (Get-Method eng-locale-factory "Create" "1.0.0");

# create the localized strings
LocalizedString::Default-Factory hello-factory = Get-Default-Factory Localized;
Localized hello = Call-Method (Get-Method hello-factory "Create" "1.0.0");
Call-Method (Get-Method hello "Add-Localization" "1.0.0") eng-locale "Hello, World!";
Call-Method (Get-Method hello "Add-Localization" "1.0.0") ita-locale "Ciao, Mondo!":

# ...

Call-Method (Get-Method printer "Print" "1.0.0") hello;
```

It's almost done, however there are a few stylistic choices that Enterprise-Lang™ enforces in order to ensure good code quality. Namely:
- The package name must be explicitly stated in every project
- The package name must be a valid domain, the compiler will do an HTTPS lookup and if it fails, the compilation fails
- To aid readability, the fully qualified names must allways be used, otherwise it may be confusing to the reader which package a certain class or method comes from.

Applying these changes, this is our final listing:

```powershell
Package com.example;

Use-Package org.enterprise-lang::io::STDIOPrinterFactory;
Use-Package org.enterprise-lang::io::Printer;
Use-Package org.enterprise-lang::locale::Locale;
Use-Package org.enterprise-lang::locale::factories::ENG;
Use-Package org.enterprise-lang::locale::factories::ITA;
Use-Package org.enterprise-lang::string::LocalizedString;

# create the locales
ENG::DefaultFactory eng-locale-factory-factory = Get-Defaut-Factory ENG;
ENG eng-locale-factory = Call-Method (Get-Method eng-locale-factory-factory "Create" "1.0.0");
Locale eng-locale = Call-Method (Get-Method eng-locale-factory "Create" "1.0.0");
ITA::DefaultFactory ita-locale-factory-factory = Get-Defaut-Factory ITA;
ITA ita-locale-factory = Call-Method (Get-Method ita-locale-factory-factory "Create" "1.0.0");
Locale ita-locale = Call-Method (Get-Method eng-locale-factory "Create" "1.0.0");

# create the localized strings
LocalizedString::Default-Factory hello-factory = Get-Default-Factory Localized;
Localized hello = Call-Method (Get-Method hello-factory "Create" "1.0.0");
Call-Method (Get-Method hello "Add-Localization" "1.0.0") eng-locale "Hello, World!";
Call-Method (Get-Method hello "Add-Localization" "1.0.0") ita-locale "Ciao, Mondo!";

# create the printer factory
STDIOPrinterFactory::DefaultFactory printer-factory-factory = Get-Default-Factory STDIOPrinterFactory;
STDIOPrinterFactory printer-factory = Call-Method (Get-Method printer-factory-factory "Create" "1.0.0");

# create the printer
Printer printer = Call-Method (Get-Method printer-factory "Create" "1.0.0");

# print "Hello, World!"
Call-Method (Get-Method printer "Print" "1.0.0") hello;
```

> Coming soon: implementing a `Main` class, implementing classes in general

## Ideas

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
- All method calls, except for built-in methods, are asynchronous and non-atomic and must be awaited. This includes simple arithmetic operations.
- The standard library is purposefully extremely deep nested and verbose.
- Everything uses `Kebab-Camel-Case` because why not.
- Names must always be fully qualified
- Packages must be imported one layer at a time, yet, they must still be fully qualified
- Everything must be in a namespace
- Everything attribute and method static by default, unless you specify with `Non-Static`
- Comments that aren't doc comments must start with `[No-Doc]`
- Methods must be assigned a "call handler", that handles the call to the method. Methods can have a default call handler, but it must be explicitly annotated with `[Use-Default-Call-Hanlder]`


## Old Example (Deprecated)

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
