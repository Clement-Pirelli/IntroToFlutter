# Making a small class : Percentage

[Last page](dart_basics.md) had a lot of information in one go, especially since all of these concepts are, in practice, intertwined.
Let's make a small, practical class to exercise our newly acquired dart-fu. It's not like I'm going to use `Quintuple` any time soon.

The use case for our class will be such: 

- Percentages are often used when scaling things according to a screen
- 10% can be either 10 or .1, depending on who you talk to
- Sometimes, we want to enforce rounded percentages, sometimes not
- Printing percentages is always annoying because we have to add '%' to the string every time.

So let's get to it!

```dart

class Percentage {
  double value;
  double get ratio => value / 100.0;
  Percentage(this.value);
}

```
