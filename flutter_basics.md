DRAFT

# Flutter basics

[When last we left off](advanced_dart.md), we looked at some more advanced features of dart.

Now that we speak the same language, we can start building on it and getting to our main goal : developing in flutter.

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

### Container

`Container` is probably the most basic of all widgets. It's a box to which you can give a width, a height or constraints. You can also give it a child widget and a decoration (for example, a background image, or rounded corners) :

```dart
Container(
  child: Text("Hello World!"),
  color: Colors.blue
);
```

You can also give a Container `padding` or a `margin` :


```dart
Container(
  padding: EdgeInsets.all(2.0),
  margin: EdgeInsets.all(5.0)
);
```

We'll get to padding in a little bit. Essentially, `padding` is applied to the inside of the container while `margin` is applied to the outside, like so :

![Padding vs Margin](https://flutter.dev/assets/ui/layout/margin-padding-border-9616dd0d7af45b95e6fcface25cd933b6b4a0fda51c1ab1bb9287bc8ed92c356.png)
 
