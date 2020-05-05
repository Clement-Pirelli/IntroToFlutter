# Advanced dart

[When last we left off](dart_percentage.md), we hopefully cemented our newly-acquired knowledge of dart by making a class using some of the language's features.

However, there are some parts of the language I intentionally left off. 

They're a bit more advanced and it felt like too much to include them in our initial foray into the language. However, they make up a big chunk of what you'll be working with and so it's good to know about them.

## Value vs reference

In dart, most objects are passed by reference. Only primitive types (`int`, `double`, `bool`, `String`) are passed by value.
In practical terms, this means the program :

```dart
void increment(int anInt) => anInt++;

int main(){
	int i = 0;
	increment(i);
	print(i);
}
```

will print 0 because `i` is copied into the `increment` function, and therefore any changes to `anInt` will not affect `i`.

when needed (which is almost never), one can choose to write something like :

```dart
class Container<T>{
	T value;
	Container(this.value);
}

void increment(Container<int> anInt)=> anInt.value++;

int main(){
	Container<int> i = Container<int>(0);
	increment(i);
	print(i.value);
}

```

User-defined classes are passed by reference, and this will therefore work as intended and output 1.
Passing by reference when one wanted to pass by value, or vice versa, can cause a lot of troubles, especially in business logic where it can lead to, for example, removing too many elements of a farmer's wares. Oddly specific example or just me speaking from experience, you will never know.

## Function storing

Let's imagine you want to design a button class. Your button class' whole point is the execution of some piece of code every time the button is pressed.

```dart

class Button{
  
  /*other stuff like rendering, etc*/
  
  //input handling if the press is within the bounds of the button
  void onScreenTap(MousePosition position)
  {
    if(withinButtonBounds(position)){
      //execute code here!
    }
  }
  
}

```

So as not to have to make one class per piece of code you wish to execute, we can make the button class store a function to execute instead.
Dart supports this easily. The syntax is such :

```dart

returnType Function(ParameterType1, ParameterType2, etc) name;

```

Now we can change our button class to incorporate our newfound knowledge :

```dart

//button.dart

class Button{

  void Function() _onPressed;

  Button(this._onPressed);
  
  /*other stuff like rendering, etc*/
  
  //input handling if the press is within the bounds of the button
  void onScreenTap(MousePosition position)
  {
    if(withinBounds(position)){
      _onPressed();
    }
  }
  
}

//main.dart

int i = 0;
void incrementI() => i++;
Button b = Button(incrementI);

```

We now have a modular button which can essentially execute any code you give it! 
You'll be working with a lot of such callback-storing code in flutter.

You might now be thinking : "this sounds quite cumbersome, do I really need to define a function somewhere every time I want to pass a different callback?"

## Shorthand functions

The answer to that question is no! Instead you can use shorthand functions, also called lambdas, which are essentially just unnamed functions. 

Defining them has the exact same syntax as normal functions except you can omit their name and return type, like so :

```dart
int i = 0;
//note that i isn't incremented yet since we haven't called the function, only defined it
//also note that the type of func is "void Function()"
var func = (){ i++; }; 

func(); // i is incremented!
```

If we now come back to our previous example, we can see how useful this kind of syntactic sugar is :

```dart

int i = 0; 
//i will only be incremented when the button is pressed, just like before
Button b = Button(() => i++);

``` 

No more one-time use functions polluting your classes, yay!

## Algorithms

Since most coding is iterating over some kind of container and applying some kind of algorithm, it makes sense not to write :

```dart
for(int i = 0; i < container.length; i++){
	//stuff
}
```

for every single use case.
Instead, dart provides easy to use common algorithms you can use paired with a shorthand function which will be executed on each element, for example.

This will not be an exhaustive list of all of the algorithms for the containers I'll talk about, but it should give you a decent idea of what you can achieve with them.

### forEach

The most rudimentary of algorithms is `forEach` : 

```dart
var ints = <int>[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]; //remember, this is a list literal
ints.forEach( (element) => print(element) ); //prints every int to the console
```

`forEach` is useful for side effects on a container as well as for simple transformation on a container. Remember however that primitive types are passed by value :

```dart
var ints = <int>[0, 1, 2, 3, 4, 5, 6, 7, 8, 9];

ints.forEach((element) => element++);

ints.forEach( (element) => print(element) );
```

This will print the exact same thing as before, because `element` is actually a copy of the "actual" element!

Note that for `Map`, `forEach` will iterate over every key's value :

```dart
var dictionary = <String, int>{'b' : 1, 'n' : 42};
dictionary.forEach((key, value)=> print(key + ' : ' + value.toString());
```
This will output b : 1 n : 42 

### map

Not to be confused with `Map`, `map` is another algorithm which takes a function. It essentially maps every element in a container with an element in a returned container through the provided function, like so :

```dart
var ints = <int>[0, 1, 2, 3, 4, 5, 6, 7, 8, 9];
var doubledInts = ints.map((element) => element*2 );
```

`doubledInts` will be an `Iterable<int>` (essentially, a list but not really), which will contain 0, 2, 4, 6, 8, 10 etc.
To convert it to a `List<int>`, you can simply write `doubledInts.toList()`.

### reduce

## Futures
## async/await
## The `mixin` keyword
