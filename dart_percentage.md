# Making a small class : Percentage

[Last page](dart_basics.md) had a lot of information in one go, especially since all of these concepts are, in practice, intertwined.
Let's make a small, practical (I'm not going to use `Quintuple` any time soon) class to exercise our newly acquired dart-fu.

The raison d'être for our class is that : 

- Percentages are often used in code, whether it's taking a percentage of a dimension for scaling, a percentage of a value for VAT, etc.
- 10% can be either be represented by `10.0` or `.1`, depending on who you talk to
- Printing percentages is always annoying because we have to add the '%' character to the string every time.
- When expecting a user to pass a percentage to a function, right now they're just passing a double, which is more prone to errors

So let's get to it!

We define our class with the one member we really need, the value from 0 to 100, which is a `double`
We can define a little helper method which divides that by 100 in case we need to multiply something by the percentage.

```dart
class Percentage {
  //from 0.0 to 100.0
  double outOfOneHundred; 
  
  double getRatio(){ return outOfOneHundred / 100.0; }
  
  //remember, this means the constructor expects a double and assigns it to "outOfOneHundred"
  Percentage(this.outOfOneHundred);
}
```

A use case for our class could be something like : 

```dart
//remember, [] means this is a positional, optional argument
double getScreenHeight([Percentage percentage]){ 
  
  //let's just imagine it's that simple
  var screenHeight = Screen.height; 
  
  //remember, "percentage" will default to null if the caller doesn't specify
  if(percentage != null) {
    return screenHeight * percentage.getRatio();
  } else {
    return screenHeight;
  }
}
```

Wonderful, we've got type safety (no more just sending in a `double`) and a clear choice between 0 to 100 and 0.0 to 1.0.
However, let's improve on this class a little bit before we call it a day. There's some stuff we might not want to have to deal with, such as the `* percentage.getRatio()` call, which is clunky and a lot of typing. Two friends will rescue us here, operator overloading and getters.
Also, what if we want to make a `const Percentage`? Right now, our only constructor isn't const, and `outOfOneHundred` is mutable. I think making `outOfOneHundred` `final` makes a lot of sense : we don't want to store `var fiftyPercent = Percentage(50.0)` and then be able to change the value to something else.
Furthermore, we still don't have our easy String. Turns out every object has a `toString()` method we can override.

So, let's change the class a little bit :

```dart
class Percentage {
  //now final
  final double outOfOneHundred; 
  
  //now a getter 
  double get ratio{ return outOfOneHundred/100.0; }
  
  //operator overloading
  double operator*(double other) { return ratio * other; }
  
  //const constructor : we'll be able to make const Percentages now
  const Percentage(this.outOfOneHundred);
  
  @override
  String toString() {
    return outOfOneHundred.toString() + '%';
  }
}
```

Nice!

We've now achieved what we wanted to! I hope you now have a bit of a more grounded knowledge the dart features you'll be most likely to be using. In the next page, things will get more interesting as we start delving into flutter itself! 
