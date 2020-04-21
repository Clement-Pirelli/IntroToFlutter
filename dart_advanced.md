# Advanced dart

[When last we left off](dart_percentage.md), we hopefully cemented our newly-acquired knowledge of dart by making a class using some of the language's features.

However, there are some parts of the language I intentionally left off. 

They're a bit more advanced and it felt like too much to include them in our initial foray into the language. However, they make up a big chunk of what you'll be working with and so it's good to know about them.



## Function storing

Let's imagine you want to design a button class. Your button class' whole point is the execution of some piece of code every time the button is pressed.

```dart

class Button{
  
  /*other stuff like rendering, etc*/
  
  //input handling if the press is within the bounds of the button
  void onScreenTap(MousePosition position)
  {
    if(withinBounds(position)){
      //execute code here!
    }
  }
  
}

```

So as not to have to make one class per piece of code you wish to execute, we can make the button class store a function to execute instead.
Dart supports this easily. The syntax is such :

```

returnType Function(Parameter types) name;

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

## Short-hand functions

The answer to that question is no! Short-hand functions, also called lambdas, are essentially just unnamed functions. 

Defining them has the exact same syntax as normal functions except you can omit their name and return type, like so :

```
int i = 0;
//note that i isn't incremented yet since we haven't called the function, only defined it
//also note that the type of func is "void Function()"
var func = (){ i++; }; 

func(); // i is incremented!
```

If we now come back to our previous example, we can see how useful this kind of syntactic sugar is :

```

int i = 0; 
//i will only be incremented when the button is pressed, just like before
Button b = Button(() => i++);

``` 

No more one-time use functions polluting your classes, yay!

## Algorithms

## Futures
## `async`/`await`
## The `mixin` keyword
