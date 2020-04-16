# Dart Basics

Dart is similar to C# or Java in many ways.  

It is an object oriented, garbage collected language- This means you won't be doing much low-level coding. Also, memory management won't be your problem. 

To use flutter, you have to know dart, so I'll be taking you on a bit of a tour of the language.

If you want to play with the examples yourself, I recommend either downloading the [intelliJ IDEA IDE](https://www.jetbrains.com/help/idea/dart.html) or simply opening [DartPad](https://dartpad.dev/) in your browser. 

This won't be a full overview of every feature, just what you need to get started. A really good resource to complement this reading with is the official [Dart language tour](https://dart.dev/guides/language/language-tour)

## Variables

### Creating variables

Creating variables follows the same syntax as C# : 

```dart
int a = 0;
double b = 5.0;
String c = 'Hello World';
```

Note that dart doesn't have a `float` type; instead, `double` is used.

All variables can be `null` if assigned `null` or unassigned :

```dart
int a; //null
int b = null; //also null :P
```

`int` and `bool` are actually both classes (more on classes later) inheriting from the class `num`, which represents numbers.

### Dynamic

You might encounter code which returns/takes a value of type `dynamic`.
`dynamic` is Dart's way of saying "You won't know until you try". `dynamic` variables could be of any type. Trying to cast a `dynamic` to the wrong type will generate a runtime error :

```dart
int a = 42;
dynamic b = a;
String c = b as String; // no error until you run the code
```

For that reason, I'd advise not using `dynamic` unless you *really* have to. Your code will be clearer and less prone to runtime errors. 

### Immutability

Immutability in dart comes in two flavours : 

- `const` specifies the variable as a compile-time constant : 
  ```dart
  const int a = 5;
  a++; //compiler error! Trying to change a const variable!
  int b = 42;
  const int c = b; //error : const variables can only be assigned compile-time constants
  ```
- `final` specifies the variable as a run-time constant, that is to say, its value can only be assigned when it is created :
  ```dart
  int a = 42;
  final int b = a; //all is well
  c++; //compiler error! Trying to change a final variable after its instantiation!
  ```

### Type inference

The keyword `var` can be used to let the dart compiler guess what the type of a value is. Again, this is basically the exact same thing as in C# :

```dart
String a = 'Hello world!';
var b = a; //type of b will be string
var c = 0; //type of c will be int
```

`var` is a double-edged sword : you'll get shorter, more flexible code, but it might sometimes be less readable.

### Control flow

Good news, control flow in dart is the same as in C, C++, C#, Java and a myriad of other languages. `while`, `if`, `for`, `switch`, they're all there. That's it, really.

## Functions

### Defining a function

Defining and calling functions also follows C#'s syntax, in most ways :

```dart
bool Foo(double bar, double boz){ return bar < boz; } //a function taking one floating-point parameter, returning a boolean
```

### Positional vs. named parameters

In dart, parameters can be positional or named.
- A positional parameter must be supplied according to its position in the parameters of a function, like in the function above.
  Another way of declaring a positional parameter is through the `[variable]` syntax, like so :
  ```dart
  bool FooB([double bar, double boz]){ return bar < boz; }
  ```

- A named argument is declared using the `{variable}` syntax. It must be supplied according to its name, like so :
  ```dart
  bool Foo({double bar})
  Foo(bar: .0); //bar will be .0
  Foo(); //bar will be null
  ```
  Notice that parameters declared with the `[variable]` or `{variable}` syntax are optional by default.

### Optional parameters

Optional parameters can be supplied, but don't need to be. They will default to either `null`, or a value provided in the function declaration, like so :


```dart
void FooA(double bar = .0){} //bar will default to .0
void FooB({double bar}){} //bar will default to null
```

Positional, required parameters always go first when declaring/calling a function.
Positional, optional parameters come next.
Named parameters come last.


```dart

void Foo(double a, [double b, double c], {bool d}){}

```

For more information on optional parameters, check [this link](https://zaiste.net/dart-optional-function-parameters/)

## Files

### Importing and exporting

Writing your entire program in `main.dart` gets tiring after a while.
Creating one file per task you're trying to tackle is usually recommended.
To import a file into another file, you simply write `import 'my/files/path/here';`. This will make it so the importing file can use the code in the imported file :

```dart
//in Login.dart 
String LogMeInPlease(){ return 'no'; }

//in main.dart
import 'Login.dart';

LogMeInPlease();
```

Sometimes, it gets tedious to have to import a lot of files into the same program.
An example is when you have a bunch of utility methods scattered in a few files. You could then write something like :

```dart
//in utilities.dart:
export 'stringUtilities.dart';
export 'doubleUtilities.dart';
export 'intUtilities.dart';

//in main.dart:
import 'utilities.dart'; //stringUtilities.dart, doubleUtilities.dart and intUtilities.dart are now imported

``` 
### Scope

If the first character of a function/variable/class' name starts with `_`, it will not be visible outside of that file :

```dart
//in Login.dart 
String _LogMeInPlease(){ return 'fine'; }

//in main.dart
import 'Login.dart';

_LogMeInPlease(); //compiler error, _LogMeInPlease can't be accessed from this file
```

## Classes

### Declaring and defining

As with C#, classes are declared and defined at the same time, using the `class` keyword :

```dart

class A {
  int a = 0;
  int c; //defaults to null
  void b(){ a++; }
  //etc
}

```

### Instantiating

Objects of a class are instantiated by calling one of the class' constructors, which are special functions which bear the name of the class, just like in C# :

```dart

class B {
  int a;
  B(int givenA){ a = givenA; };
}

```

As they are generous gods, dart's makers have included a really nice feature into the language, which allows the programmer to alleviate their carpal tunnel syndrome somewhat by requiring much less typing. The code below is exactly equivalent to the code above :

```dart
class B{
  int a;
  B(this.a);
}
```

Note that if you do not specify a constructor, the default constructor (`ClassName()`) will still be defined.

It is possible to define a `const` constructor, so that the constructed object is a compile-time constant, like so :

```dart
class Foo{
  final int bar;
  final int gombo;
  const Foo(this.bar, this.gombo);
}
```

One may then declare a `const Foo f = const Foo(0, 2);`.

For more information on constructors, check [this link](https://dart.dev/guides/language/language-tour#constructors) 


To instantiate an object of a class, the syntax is :

```

A myA = A();
B myB = B(10);

```

Note : it used to be that you'd have to use `new` just like in C# but again, generous gods.

### Getters and setters

Getters and setters are a pretty nice quality of life addition to the language. For example, one might want to make a small class which calculates a price with VAT and some charges. Getters make everything much more straightforward :

```dart 
class Payment
{
  final double _VATPercentage;
  final double _initialPrice;
  final double _charges;
  Payment(this._initialPrice, this._VATPercentage, this._charges);
  
  double get VATPart { return _initialPrice * _VATPercentage/100.0; }
  double get finalPrice { return _initialPrice + VATPart + _charges; }
}

//somewhere else

var payment = Payment(200.0, 12.0, 5.0); //here we can see also the point of named parameters
var finalPrice = payment.finalPrice;
payment.finalPrice++; //compiler error! a getter isn't something you can set!

```

As you can see, this is essentially a shorthand for a function which takes no arguments.

A setter on the other hand is a shorthand for a function which returns nothing and sets a variable :

```dart
class StoredInt
{
  int _value;
  final String _path;
  
  void _loadValueFromPath(){ /*actually save the int from disk here*/ }
  void _saveValueAtPath(){ /*actually save the int to disk here*/ }
  int get value{ return value; }
  set value(int givenValue) { _value = givenValue; _saveValueAtPath(); }
  
  StoredInt(String path){ path = _path; _loadValueFromPath(); }
}

var mySavedInt = StoredInt("my/path/here.txt");
var value = mySavedInt.value; //gets the loaded value
mySavedInt = 24; //saves the value to disk
```

As you can see this is useful for providing easy, intuitive classes. 
What getters and setters aren't useful for is this kind of stuff :

```
class Foo{
  int _bar;
  int get bar { return _bar; }
  set bar(int givenBar) { _bar = givenBar; }
}
```

I would advise not doing this, since it just doesn't do anything making the variable visible wouldn't.

### Private members?

Dart has no concept of public/private members. The only way you can hide a member from the rest of the program is to move the class to its own file and have that member's name feature a `_` as the first character.


### Inheritance

This section will be very brief because honestly, you won't need to care about inheritance too much while working with flutter.

Declaring a child of another class is done using the keyword `extends`, like so :

```
class Foo{
  int a;
}

class Bar extends Foo
{
  void doStuff(){ a++; }
}
```

The child class will inherit all of the members of the parent class.


### Generics

Imagine you want to make a class which stores five items of one type, let's call the class `Quintuple`. You want to have a `Quintuple` of ints, a `Quintuple` of Strings, etc.

What you *could* do is write up a `QuintupleString` class, which holds five strings, and a `QuintupleInt` class which holds five ints, and after a lot of copy-paste and changing types you'd get what you want. But then whenever you want to change the behavior of your `Quintuple`, you have to change every copy-pasted piece of code, and after a while you just give up. Our merciful gods have provided us with an alternative however : generics.

```dart
class Quintuple<Type>{
  Type first;
  Type second;
  Type third;
  Type fourth;
  Type fifth;
  Quintuple(this.first, this.second, this.third, this.fourth, this.fifth);
}
```

This will effectively automatically create a copy-paste where `Type` is replaced by `int`. 

Now you can create a `Quintuple` of `int`s by simply writing : 
```dart
var foo = Quintuple<int>(42, 42, 42, 42, 42)
``` 

If you want to restrict which "copy-pastes" can be created, for example only to numbers, you can do so with `extends`, by writing :

```dart
class Quintuple<Type extends num>{
  //first second etc
}
```


### Operator overloading

In dart, operators on a type (`+`, `-`, `/`, `==`, `[]`) can be overloaded. 
For our previous example, the `Quintuple`, we might want to be able to divide every element by the same number.
The special function name `operator`, followed by the symbol of the operator you wish to overload, can be used :

```dart
class Quintuple<Type extends num>{
  
  Quintuple<Type> operator/(Type other){ return Quintuple<Type>(first/other, second/other, third/other, fourth/other, fifth/other); }
}
```

You could then write :

```
var foo = Quintuple<int>(0,1,2,3,4,5);
var bar = foo / 5;
``` 

### Static members

A class may have static members, which are declared, unsurprisingly, using the keyword `static` :

```
class Foo{
  static int bar;
}

Foo.bar = 5;
```

As with C# and a plethora of other object-oriented languages, static members are tied to the class, not to any instance. 




For more information on classes, check [this link](https://www.tutorialspoint.com/dart_programming/dart_programming_classes.htm).


## Containers

Let's face it, our `Quintuple` class is terrible. `first`, `second`, `third`, really? How do we put that in a `for` loop, even? We'd have to execute the same code on every single item, just like we did with the `/` operator, and honestly I know I get paid per hour but having to do that is just not fun, not productive, and overall just not a good time. 

### List

Luckily, `List` is here to help.
The `List` class is a generic container which can either be fixed or growable.
We can use it to replace our horrible `Quintuple`'s items by a 5-length list, like so :

```dart
class Quintuple<Type>{
  List<Type> items = List<Type>(5); //fixed length of 5
  Quintuple(Type first, Type second, Type third, Type fourth, Type fifth)
  {
    items[0] = first; //0 indexed, just like in most other programming languages
    items[1] = second; 
    //etc
  }
}
```

Note that in day-to-day code, lists can be easily created using this syntax :

```dart
var myList = <int>[1,2,2,4,5,6,10,42]; //this is a growable list
```

### Map

Dart's `Map` is a generic type featuring pairs of key and value. One can then look up the value for any key using the `[]` operator, with `O(1)` time complexity.
This is extremely useful when you need to be able to look up an element of some data without needing to search through all of the data, or when you have very sparse data (say, a grid where only a few cells are filled with data)

As with lists, maps can be instantiated using a special notation :

```
var myMap = <String, int>{'Hello World': 42, 'The funny number' : 1337};
```

## Wrapping up

I know this is a lot to take in. It's fine if you don't remember most of the syntax. [Next chapter](dart_percentage.md), we'll be writing a small class bit by bit to cement your knowledge a little bit.
