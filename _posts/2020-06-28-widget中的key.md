在flutter中，万物皆widget。其构造函数会接收一个名为key的参数。

```java
...
/// The [key] property controls how one widget replaces another widget in the
/// tree. If the [runtimeType] and [key] properties of the two widgets are
/// [operator==], respectively, then the new widget replaces the old widget by
/// updating the underlying element (i.e., by calling [Element.update] with the
/// new widget). Otherwise, the old element is removed from the tree, the new
/// widget is inflated into an element, and the new element is inserted into the
/// tree.

abstract class Widget extends DiagnosticableTree {
  /// Initializes [key] for subclasses.
  const Widget({ this.key });
...
```

根据官方注释，这key是用来标记新旧widget是被替换还是被更新。那么什么时候去使用key呢？下面我们会通过一个例子来说明key的用处。

``` java
void main() => runApp(new MaterialApp(home: PositionedTiles()));

class PositionedTiles extends StatefulWidget {
 @override
 State<StatefulWidget> createState() => PositionedTilesState();
}

class PositionedTilesState extends State<PositionedTiles> {
 List<Widget> tiles = [
   StatelessColorfulTile(),
   StatelessColorfulTile(),
 ];

 @override
 Widget build(BuildContext context) {
   return Scaffold(
     body: Row(children: tiles),
     floatingActionButton: FloatingActionButton(
         child: Icon(Icons.sentiment_very_satisfied), onPressed: swapTiles),
   );
 }

 swapTiles() {
   setState(() {
     tiles.insert(1, tiles.removeAt(0));
   });
 }
}

class StatelessColorfulTile extends StatelessWidget {
 Color myColor = UniqueColorGenerator.getColor();
 @override
 Widget build(BuildContext context) {
   return Container(
       color: myColor, child: Padding(padding: EdgeInsets.all(70.0)));
 }
}

```

在这个例子中，一个StatefulWidget利用Row去展示两个StatelessWidget，每一个都有随机的颜色，还有一个按钮，点击之后，两个StatelessWidget会交换在Row中的位置。

![](https://miro.medium.com/max/400/1*edgczyvaQRgGRy8yhht0QQ.gif)

现在将两个StatelessWidget改用StatefulWidget去实现

``` java
List<Widget> tiles = [
   StatefulColorfulTile(),
   StatefulColorfulTile(),
];

...
class StatefulColorfulTile extends StatefulWidget {
 @override
 ColorfulTileState createState() => ColorfulTileState();
}

class ColorfulTileState extends State<ColorfulTile> {
 Color myColor;

 @override
 void initState() {
   super.initState();
   myColor = UniqueColorGenerator.getColor();
 }

 @override
 Widget build(BuildContext context) {
   return Container(
       color: myColor,
       child: Padding(
         padding: EdgeInsets.all(70.0),
       ));
 }
}
```

但是点击button之后好像什么都没有发生

![](https://miro.medium.com/max/400/1*T7TBQx9DhaQ16gbX68XxVw.gif)

为了解决这个问题，只要在StatefulWidget的构造函数中传入key即可。

``` java
List<Widget> tiles = [
  StatefulColorfulTile(key: UniqueKey()), // Keys added here
  StatefulColorfulTile(key: UniqueKey()),
];

...
class StatefulColorfulTile extends StatefulWidget {
  StatefulColorfulTile({Key key}) : super(key: key);  // NEW CONSTRUCTOR

  @override
  ColorfulTileState createState() => ColorfulTileState();
}

class ColorfulTileState extends State<ColorfulTile> {
  Color myColor;

  @override
  void initState() {
    super.initState();
    myColor = UniqueColorGenerator.getColor();
  }

  @override
  Widget build(BuildContext context) {
    return Container(
        color: myColor,
        child: Padding(
          padding: EdgeInsets.all(70.0),
        ));
  }
}

```

![](https://miro.medium.com/max/400/1*3XbdhaQ9_lPfILdViiipeQ.gif)

那么这一切背后的原理是什么呢？

简单的的来说，对于StatefulWidget，其状态State是由Element Tree持有的，当交换了widget的位置，由于两个StatefulWidget有着相同的type，所有其对应的StatefulElement并不会交换，所以视觉上，两个widget并没有互换。

![](https://miro.medium.com/max/750/1*7n-u4yexzRZDEtNvbrsG1g.gif)

当两个StatefulWidget有着不同的key时，Element Tree中的两个StatefulElement也会随之互换。

![](https://miro.medium.com/max/750/1*AcBxC8IF_irZpFARt-Nqyw.gif)

一句话总结就是：当你需要改变一个StatefulWidget集合的顺序或者size时，考虑使用key。

原贴地址: https://medium.com/flutter/keys-what-are-they-good-for-13cb51742e7d
