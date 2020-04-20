DRAFT

# Flutter basics

[When last we left off](dart_percentage.md), we hopefully cemented our newly-acquired knowledge of darty by creating a simple class with some of the language's unique features.

Now that we speak the same language, we can start building on it and getting to our main goal : developing in flutter.

## Theory

Before we can get into some code, we have to understand how flutter works on at least a surface level.
In flutter, everything is a widget in the widget tree.
The widget tree essentially lays out widgets as parents and their child/children. A `FlatButton` might have a `Text` as its child, for example.

The widget tree is then "built", meaning it is traversed first from top to bottom to inform children of their parent's constraints, and then vice versa.

The widget tree exists for two reasons :
* It allows for a completely reactive code base, where only affected widgets need to be rebuilt on a change
* It allows the programmer to lay out constraints easily both from parents to children and from children to parents, due to the building process explained above



There are two types of widgets you should care about for now, stateful and stateless widgets.
