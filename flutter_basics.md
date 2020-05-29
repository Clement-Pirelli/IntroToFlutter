# Flutter basics

[When last we left off](advanced_dart.md), we acquainted ourselves with some more advanced features of dart, and now that we speak the same language, we can start building on it and getting to our main goal : developing apps with flutter.

## The widget tree

Before we can get into some code, we have to understand how flutter works on at least a surface level.
In flutter, everything is a widget in the widget tree.
The widget tree essentially lays out widgets as parents and their child/children. A `FlatButton` might have a `Text` as its child, for example.

The widget tree is then "built", meaning it is traversed first from top to bottom to inform children of their parent's constraints, and then vice versa.

The widget tree exists for two reasons :
* It allows for a completely reactive code base, where only affected widgets need to be rebuilt on a change
* It allows the programmer to lay out constraints easily both from parents to children and from children to parents, due to the building process explained above.

When rebuilding, all affected widgets are essentially recreated from scratch, meaning they are first constructed and then built.

A more detailed explanation of the widget tree and flutter internals is provided in the excellent book "Flutter in action" which should be provided to you.

## Stateful vs Stateless

There are two types of widgets you should care about for now, stateful and stateless widgets.
As the name implies, stateful widgets are widgets which need to keep track of state, which is fairly tricky to do seeing as every time a widget changes, their state is reset.
Internally, this means that a separate State object, which is associated with a unique key for its widget, is created. This State is mostly left alone when the app is rebuilt. This allows the app to keep track of state.

## The default project

By now, it's a good idea to have your IDE of choice (I recommend intellij IDEA) open with flutter set up. When you're done setting it up, you should have the default flutter project in front of you. In the `lib` folder, you'll find your `main.dart` file, complete with a *lot* of comments. Almost an overwhelming amount. I advise you to read it all as it lays out a lot of flutter's essence quite elegantly.

It already showcases one `StatelessWidget`, `MyApp`.
`MyApp` simply builds a `MaterialApp` (Material is google's app design) which has a `StatefulWidget`, `MyHomePage`, as its homepage.

As you can see in the code, the `StatefulWidget` itself does almost nothing. Its state, `_MyHomePageState`, is the one doing the building, since it's the one which holds important information. `_MyHomePageState` builds a `Scaffold`, which is essentially boilerplate in which renderable widgets live. This scaffold displays a counter and holds a `FloatingActionButton` which, when pressed, calls `_incrementCounter`.

Notice that `_incrementCounter` doesn't simply add one to the counter. It first calls another function, `setState`.

`setState` executes whatever code is provided as its callback, but it also asks for the rebuilding of the `StatefulWidget`. If you were to simply add to the counter, no other part of your program would know about the change, and so, while your counter would indeed be incremented, no change would be shown.

You might've also noticed that every `build` method has a `BuildContext` as a parameter. The build context is needed for some static methods of other classes to know where you are in the tree. For example, if you wanted to know the current `Theme`, you would write :

```dart
var theme = Theme.of(context);
```

## Basic widgets

Flutter features a [ginormous amount of widgets](https://api.flutter.dev/flutter/widgets/widgets-library.html). Most of them have extremely specific uses though, so 20% of the widgets will do 80% of the work, pretty much.

All of the widgets I showcase here are wrapped in a `SafeArea`, which keeps the widgets from displaying on areas of the phone which aren't properly visible (say, behind the android status bar), as well as in a `Scaffold`. 

Do also note that this is only an overview of your options. If you wish to know more about a specific widget, a short google search should land you on the Flutter documentation page for that widget where you can find a detailed explanation for all of its properties. In IDEA, you can also press `Ctrl+P` when your cursor is inside a function call (in our case, a constructor) to see all of the parameters of the function.

<CtrlPExample here>

If that doesn't suffice (IDEA likes to hide the type of the parameters for example, which drives me crazy), you can click on the function name and press `Ctrl+Q`to see the whole function, where it comes from, and if you're lucky, a small explanation of its purpose.

[CtrlQExample]()

### Container

`Container` is probably the most basic of all widgets. It's a box to which you can give a width, a height or constraints to follow. You can also give it a child widget and a decoration (for example, a background image, or rounded corners) :

```dart
Container(
  child: Text("Hello World!"),
  color: Colors.blue,
  height: 200.0,
  width: 100.0
);
```

[ContainerExample]()

### Padding

The `Padding` widget is useful when you want widgets to be separated from eachother. It takes a `child` widget and a `padding` parameter, which is an `EdgeInsets`

```dart
Container(
  child: Padding(
    child: Text("Hello World!"),
    padding: EdgeInsets.all(20.0),
  ),
  color: Colors.blue,
  height: 200.0,
  width: 100.0
)
```

![PaddingExample here]()

`EdgeInsets` describes how padding is to be applied :
* `all` lays out the same specified amount of padding around the child widget. 
* `symmetric` applies the same specified horizontal and/or vertical padding symmetrically. 
* `fromLTRB` stands for "from Left Top Right Bottom", meaning a value must be specified for each side. 
* `only` lets you specify one and only one value for one side

The `Container` widget can also have a `padding` or a `margin`, which is just a shorthand for it having a child `Padding` widget before its child widget :


```dart
Container(
  child: Text("Hello world!"),
  padding: EdgeInsets.all(2.0),
  margin: EdgeInsets.all(5.0)
);
```

`margin` is like padding, but applied as a parent of the `Container` itself :

![Padding vs Margin](https://flutter.dev/assets/ui/layout/margin-padding-border-9616dd0d7af45b95e6fcface25cd933b6b4a0fda51c1ab1bb9287bc8ed92c356.png)
 
### Text

The `Text` widget is perhaps the crowning jewel of Flutter, and it better be, seeing as it's the main content of most apps. 
`Text` must be given a string to display. The rest of the parameters are optional. `style` can be provided to change the color, font size, font weight, and many more properties of the text. By default, Text informs itself of its style based on the closest `DefaultTextStyle` class in the widget tree. In most apps' case, that's the default Material theme set by the `MaterialApp` widget. 

If you want your own text style, you can either explicitly specify another style by creating and assigning a `TextStyle` : 

```dart
Text("Hello World!", 
  style: TextStyle(
    color: Colors.red, 
    fontSize: 20.0, 
    backgroundColor: Colors.white30, 
    fontWeight: FontWeight.bold,
  )
)
```

Or you can get one the current `Theme`'s styles (which you can also set using the `theme` parameter of `MaterialApp`'s constructor) :

```dart
Text("Hello World!",
  style: Theme.of(context).primaryTextTheme.subtitle2
)
```

## Row and Column

So far we've only seen widgets which take a single widget, but there are very few apps which can be built using a single chain of widgets. `Row` and `Column` both accept a `List<Widget>` as their `children`.

```dart
Row(
  mainAxisAlignment: MainAxisAlignment.center,
  children: <Widget>[
    Text("Hello", style: TextStyle(color: Colors.red),),
    Text("World", style: TextStyle(color: Colors.green),)
  ],
)
```

[RowExample]()

`mainAxisAlignment` decides the alignment of the children along the axis of the widget (horizontal for `Row`, vertical for `Column`).
You can also specify the `crossAxisAlignment`, which is the alignment along the other axis.

A more practical example of these widgets being used would be something like :

```dart

//somewhere
var events = <Event>[
  Event(address: "Ravinstigen 2", name: "Michael's rose market"),
  Event(address: "Fältgatan 5", name: "Fältgatan farmer's market"),
  Event(address: "Mejerigatan 5", name: "Bread wagon")
];

//in the scaffold
Column(
  crossAxisAlignment: CrossAxisAlignment.start,
  children: events.map(
    (event) => Padding(child: Text(event.name + ':' + event.address), padding: EdgeInsets.symmetric(vertical: 20.0),)
  ).toList()
)

```

[ColumnExample]()
  
Remember that `map` returns an iterable based on the elements of a list. Here, we have a list of events based on which we create `Text` widgets. 

This works fine for a few elements, but if we had ten thousand, it would iterate over and try to display all of them. It'd take a lot of time on rebuilds and you'd get an overflow error as widgets are rendered outside of the screen :

[OverflowExample]()

## ListView

To combat the thousands of elements overflowing, `ListView` can be used to render widgets in a scrollable list :

```dart
ListView(
  children: events.map(
    (event) => Padding(child: Text(event.name + ':' + event.address), padding: EdgeInsets.symmetric(vertical: 20.0),)
  ).toList()
)
```

However, we're still iterating over all of the elements. This is wasteful, since we'll only render a few of them, so every rebuild we'll be building thousands of elements the user will never see, even if we don't render them! To combat this problem, we can use `ListView.builder`, a special constructor which takes an `itemBuilder` function. 

`itemBuilder` takes the `BuildContext` and an `int`, `index`, which is the index of the widget being built currently :

```dart
ListView.builder(
  itemBuilder: (context, index) => Padding(
    child: Text(events[index].name + ':' + events[index].address), 
    padding: EdgeInsets.symmetric(vertical: 20.0),
  ),
  itemCount: events.length, //ListView.builder doesn't know the item count, since it doesn't simply take a List<Widget>
)
```

This way, the builder for a widget will only be called when the user scrolls to that widget.


There are many other layout widgets, such as `Stack`, `GridView` and `ListTile`. For more information on these and more, you can take a look at [this post](https://flutter.dev/docs/development/ui/layout) from the official flutter documentation.

