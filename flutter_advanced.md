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
var localization = Provider.of<Localization>(context);
Text(localization.get('_helloWorld')); //will be rebuilt on Localization's notifyListener
```

So now our button can both access the localization instance easily, and load another localization file without the rest of the app needing to check if they need to rebuild all the time :

```dart
FlatButton(
  onPressed: () => localization.load('assets/swedish.json')
);
```

So to summarize, `Provider` gives us an easy way to access state from all over the app in a reactive way. 

## Business Logic

Business logic is anything that isn't related to the "presentation layer", aka showing data to the user. This includes interacting with a database, writing to files, calculating prices, etc.
Business logic being embedded into your presentation layer is a bad idea. This is because it breaks the [DRY principle](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) as well as the [single-responsibility principle](https://en.wikipedia.org/wiki/Single-responsibility_principle) : your logic should be contained in one place tasked only with handling this logic, while your presentation layer should only tackle the challenges of showing things to the user.

Business logic should therefore be separated, and in our case accessible through a `Provider`.

In this example, our app needs to handle the loading and unloading of boxes. A screen of our app will present a button for each box, to load and unload it.

With no business logic separation, it looks a little bit like this: 

```dart

//stateful widget here

class BoxButtonsState extends State<BoxButtons> {
  
  var boxes = <Box>[]; //our list of boxes
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      //button to add a box
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed () {
          print('Adding a new box!');
          setState(()=> boxes.add(_getNewBoxFromUser()));
        }
      ), 
      //list of boxes
      body: ListView.builder(
        itemCount: boxes.length,
        builder: (context, index){
          return Row(
            children: <Widget>[
              //display the name and contents of the box
              Text(boxes[i].name + ' ' + boxes[i].contents.reduce(value, element) => value + ' ' + element)
              //button to remove a box
              FlatButton(
                onPressed: (){
                  print('Removing box ' + boxes[i].id + '!');
                  setState(()=> boxes.removeAt(i));
                },
                child: Text('Remove')
              ),
            ]
          );
        }
      )
    );
  }
}
```

I've made this example extremely simple, and yet we can already see that this code cannot scale. The `boxes` list simply cannot be accessed from any other part of the app's tree. This is an extremely simple example, and so there isn't much logic here, but imagine adding loading/adding/removing the boxes from a database into the mix.

By adding business logic separation, we end up with something like :

```dart
class Boxes extends ChangeNotifier{
  var _list = <Box>[];
  
  void addNewBox(){ /*logic here*/ }
  void removeBox(int index){ /*logic here*/ }
  
  String getBoxText(int index){ /*logic here*/ }
  
  int get amount => _list.length;
}

class BoxButtonsState extends State<BoxButtons> {

  @override
  Widget build(BuildContext context) {
 
    var boxes = Provider.of<Boxes>(context);
    
    return Scaffold(
      //button to add a box
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed () => boxes.addNewBox()
      ), 
      //list of boxes
      body: ListView.builder(
        itemCount: boxes.amount,
        builder: (context, index){
          return Row(
            children: <Widget>[
              //display the name and contents of the box
              Text(boxes.getBoxText(i))
              //button to remove a box
              FlatButton(
                onPressed: () => boxes.removeBox(i),
                child: Text('Remove')
              ),
            ]
          );
        }
      )
    );
  }
}
```
Now, if we wish to change the way we add a box, for example by storing them in a database or on a file, we can do that without changing any code related to UI. We can also access the boxes from anywhere below the `ChangeNotifierProvider` for `Boxes` in the widget tree.
