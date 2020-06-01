# Advanced Flutter

[Last time](flutter_basics.md), we had a brief overview of flutter's basics. Now we're going to look at some more advanced features you'll definitely encounter in the Rosenkvarn apps.

## State Management

Imagine your app has some piece of data the entire app needs to access, like the localization of the app :

```dart
class Localization
{
  Map<String, String> _data; //key-value pairs
  
  Localization(String path) => load(path); //load on create
  
  void load(String path) {
    data = loadDataAt(path);
  }
  
  String get(String key) => _data[key];
  
  //details like loadDataAt here, they don't matter for this example
}
```

Now let's say the app lets the user change the language whenever they want to. How would you go about implementing this in flutter?
Remember, the solution has to be reactive, we don't want to rebuild the entire tree every frame. So we can't simply have everything which uses `Localization.data` fetch their key every frame. 

They need some way to be notified to rebuild only when the state changes. There are many ways to do this. The one we use is `Provider`, a package you can find [here](https://pub.dev/packages/provider).

Explaining `Provider` in a vacuum makes very little sense. Let's adapt our `Localization` class to our new state management solution and I'll explain how everything works along the way.

First, we need `Localization` to inherit from `ChangeNotifier`. [This class](https://api.flutter.dev/flutter/foundation/ChangeNotifier-class.html) simply provides an interface to notify, register and remove listeners :

```dart
class Localization extends ChangeNotifier
{
  void load(String path) {
    data = loadDataAt(path);
    notifyListeners(); //new! every time we load something new, we'll notify all our listeners
  }
  
  //rest is unchanged
}
```

Next, we need to place this class in the widget tree so that we can access it through the `BuildContext`. The provider package includes a generic class called `ChangeNotifierProvider`, which we should place above the rest of our app in the widget tree :

```dart
class Application extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ChangeNotifierProvider<Localization>(
      create: (context) => Localization('assets/english.json'),
      child: MaterialApp(/*your app here*/)
    );
  }
}
```

We can now call access the `Localization` instance we created by calling `Provider.of<Localization>(context)`. This will also automatically register the calling widget as a listener to be notified (and therefore rebuilt) whenever `Localization` calls `notifyListener`, in our case during `load` :

```dart
Text(Provider.of<Localization>(context).get('_helloWorld')); //will be rebuilt on Localization's notifyListener
```

So now our button can both access the localization instance easily, and load another localization file without the rest of the app needing to check if they need to rebuild all the time :

```dart
FlatButton(
  onPressed: () => Provider.of<Localization>(context).load('assets/swedish.json')
);
```

## Business Logic
