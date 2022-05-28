Flutter 从 React 中吸取灵感，通过现代化框架创建出精美的组件。它的核心思想是用 widget 来构建你的 UI 界面。 Widget 描述了在当前的配置和状态下视图所应该呈现的样子。当 widget 的状态改变时，它会重新构建其描述（展示的 UI），框架则会对比前后变化的不同，以确定底层渲染树从一个状态转换到下一个状态所需的最小更改。

## Hello world

创建一个最小的 Flutter 应用简单到仅需调用 [runApp()](https://api.flutter-io.cn/flutter/widgets/runApp.html) 方法并传入一个 widget 即可：

```dart
import 'package:flutter/material.dart';

void main() {
  runApp(
    const Center(
      child: Text(
        'Hello, world!',
        textDirection: TextDirection.ltr,
      ),
    ),
  );
}
```

`runApp()` 函数会持有传入的 [Widget](https://api.flutter-io.cn/flutter/widgets/Widget-class.html)，并且使它成为 widget 树中的根节点。框架会强制让根 widget 铺满整个屏幕。

在写应用的过程中，取决于是否需要管理状态，你通常会创建一个新的组件继承 [StatelessWidget](https://api.flutter-io.cn/flutter/widgets/StatelessWidget-class.html) 或 [StatefulWidget](https://api.flutter-io.cn/flutter/widgets/StatefulWidget-class.html)。 Widget 的主要工作是实现 [build()](https://api.flutter-io.cn/flutter/widgets/StatelessWidget/build.html) 方法，该方法根据其它较低级别的 widget 来描述这个 widget。框架会逐一构建这些 widget，直到最底层的描述 widget 几何形状的 [RenderObject](https://api.flutter-io.cn/flutter/rendering/RenderObject-class.html)。

## 基础 Widget

Flutter 自带了一套强大的基础 widgets，下面列出了一些常用的：

- [Text](https://api.flutter-io.cn/flutter/widgets/Text-class.html)  
`Text` widget 可以用来在应用内创建带样式的文本。
- [Row](https://api.flutter-io.cn/flutter/widgets/Row-class.html), [Column](https://api.flutter-io.cn/flutter/widgets/Column-class.html)  
这两个 flex widgets 可以让你在水平 (`Row`) 和垂直(`Column`) 方向创建灵活的布局。它是基于 web 的 flexbox 布局模型设计的。
- [Stack](https://api.flutter-io.cn/flutter/widgets/Stack-class.html)  
`Stack` widget 不是线性（水平或垂直）定位的，而是按照绘制顺序将 widget 堆叠在一起。你可以用 [Positioned](https://api.flutter-io.cn/flutter/widgets/Positioned-class.html) widget 作为 `Stack` 的子 widget，以相对于 `Stack` 的上，右，下，左来定位它们。 Stack 是基于 Web 中的绝对位置布局模型设计的。
- [Container](https://api.flutter-io.cn/flutter/widgets/Container-class.html)  
`Container` widget 可以用来创建一个可见的矩形元素。 Container 可以使用 [BoxDecoration](https://api.flutter-io.cn/flutter/painting/BoxDecoration-class.html) 来进行装饰，如背景，边框，或阴影等。 `Container` 还可以设置外边距、内边距和尺寸的约束条件等。另外，`Container`可以使用矩阵在三维空间进行转换。

请确认在 `pubspec.yaml` 文件中 `flutter` 部分有 `uses-material-design: true` 这条，它能让你使用预置的 [Material icons](https://design.google.com/icons/)。

为了获得(`MaterialApp`)主题的数据，许多 Material Design 的 widget 需要在 [MaterialApp](https://api.flutter-io.cn/flutter/material/MaterialApp-class.html) 中才能显现正常。因此，请使用 `MaterialApp` 运行应用。

## 使用 Material 组件

Flutter 提供了许多 widget，可帮助你构建遵循 Material Design 的应用。 Material 应用以 [MaterialApp](https://api.flutter-io.cn/flutter/material/MaterialApp-class.html) widget 开始，它在你的应用的底层下构建了许多有用的 widget。这其中包括 [Navigator](https://api.flutter-io.cn/flutter/widgets/Navigator-class.html)，它管理由字符串标识的 widget 栈，也称为“routes”。 `Navigator` 可以让你在应用的页面中平滑的切换。使用 [MaterialApp](https://api.flutter-io.cn/flutter/material/MaterialApp-class.html) widget 不是必须的，但这是一个很好的做法。

注意，widget 作为参数传递给了另外的 widget。 [Scaffold](https://api.flutter-io.cn/flutter/material/Scaffold-class.html) widget 将许多不同的 widget 作为命名参数，每个 widget 都放在了 Scofford 布局中的合适位置。同样的，[AppBar](https://api.flutter-io.cn/flutter/material/AppBar-class.html) widget 允许我们给 [leading](https://api.flutter-io.cn/flutter/material/AppBar-class.html#leading)、[title](https://api.flutter-io.cn/flutter/material/AppBar-class.html#title) widget 的 [actions](https://api.flutter-io.cn/flutter/material/AppBar-class.html#actions) 传递 widget。这种模式在整个框架会中重复出现，在设计自己的 widget 时可以考虑这种模式。

有关更多信息，请参阅 [Material 组件](https://flutter.cn/docs/development/ui/widgets/material)。

> Material 是 Flutter 中两个自带的设计之一，如果想要以 iOS 为主的设计，可以参考 [Cupertino components](https://flutter.cn/docs/development/ui/widgets/cupertino)，它有自己版本的 [CupertinoApp](https://api.flutter-io.cn/flutter/cupertino/CupertinoApp-class.html) 和 [CupertinoNavigationBar](https://api.flutter-io.cn/flutter/cupertino/CupertinoNavigationBar-class.html)。

## 处理手势

大多数应用都需要通过系统来处理一些用户交互。构建交互式应用程序的第一步是检测输入手势。

[GestureDetector](https://api.flutter-io.cn/flutter/widgets/GestureDetector-class.html) widget 没有可视化的展现，但它能识别用户的手势。当用户点击 Container 时， `GestureDetector` 会调用其 [onTap()](https://api.flutter-io.cn/flutter/widgets/GestureDetector-class.html#onTap) 回调，在这里会向控制台打印一条消息。你可以使用 `GestureDetector` 检测各种输入的手势，包括点击，拖动和缩放。

许多 widget 使用 [GestureDetector](https://api.flutter-io.cn/flutter/widgets/GestureDetector-class.html) 为其他 widget 提供可选的回调。例如，[IconButton](https://api.flutter-io.cn/flutter/material/IconButton-class.html)、[ElevatedButton](https://api.flutter-io.cn/flutter/material/ElevatedButton-class.html) 和 [FloatingActionButton](https://api.flutter-io.cn/flutter/material/FloatingActionButton-class.html) widget 都有 [onPressed()](https://api.flutter-io.cn/flutter/material/ElevatedButton-class.html#onPressed) 回调，当用户点击 widget 时就会触发这些回调。

有关更多信息，请参阅 [Flutter 中的手势](https://flutter.cn/docs/development/ui/advanced/gestures)。

## 根据用户输入改变 Widget

到目前为止，这个页面仅使用了无状态的 widget。无状态 widget 接收的参数来自于它的父 widget，它们储存在 [final](https://dart.cn/guides/language/language-tour#final-and-const) 成员变量中。当 widget 需要被 [build()](https://api.flutter-io.cn/flutter/widgets/StatelessWidget/build.html) 时，就是用这些存储的变量为创建的 widget 生成新的参数。

为了构建更复杂的体验，例如，以更有趣的方式对用户输入做出反应—应用通常带有一些状态。 Flutter 使用 StatefulWidgets 来实现这一想法。 StatefulWidgets 是一种特殊的 widget，它会生成 State 对象，用于保存状态。

您可能想知道为什么 StatefulWidget 和 State 是独立的对象。在 Flutter 中，这两种类型的对象具有不同的生命周期。 Widget 是临时对象，用于构造应用当前状态的展示。而 State 对象在调用 `build()` 之间是持久的，以此来存储信息。

在更复杂的应用中，widget 层次不同的部分可能负责不同的关注点；例如，一个 widget 可能呈现复杂的用户界面，来收集像日期或位置这样特定的信息，而另一个 widget 可能使用该信息来改变整体的展现。

在 Flutter 中，widget 通过回调得到状态改变的通知，同时当前状态通知给其他 widget 用于显示。重定向这一流程的共同父级是 `State`。

有关更多信息，请参阅：
- API 文档: [StatefulWidget](https://api.flutter-io.cn/flutter/widgets/StatefulWidget-class.html)
- API 文档: [State.setState](https://api.flutter-io.cn/flutter/widgets/State/setState.html)

## 响应 widget 的生命周期事件

在 StatefulWidget 上调用 [createState()](https://api.flutter-io.cn/flutter/widgets/StatefulWidget-class.html#createState) 之后，框架将新的状态对象插入到树中，然后在状态对象上调用 [initState()](https://api.flutter-io.cn/flutter/widgets/State-class.html#initState)。 [State](https://api.flutter-io.cn/flutter/widgets/State-class.html) 的子类可以重写 `initState` 来完成只需要发生一次的工作。实现 `initState` 需要调用父类的 `super.initState` 方法来开始。

当不再需要状态对象时，框架会调用状态对象上的 [dispose()](https://api.flutter-io.cn/flutter/widgets/State-class.html#dispose) 方法。可以重写`dispose` 方法来清理状态。实现 `dispose` 时通常通过调用 `super.dispose` 来结束。

有关更多信息，请参阅 [State](https://api.flutter-io.cn/flutter/widgets/State-class.html)。

## Key

使用 key 可以控制框架在 widget 重建时与哪些其他 widget 进行匹配。默认情况下，框架根据它们的 [runtimeType](https://api.flutter-io.cn/flutter/widgets/Widget-class.html#runtimeType) 以及它们的**显示顺序**来匹配。使用 key 时，框架要求两个 widget 具有相同的 [key](https://api.flutter-io.cn/flutter/foundation/Key-class.html) 和 `runtimeType`。

Key 在构建相同类型 widget 的多个实例时很有用。例如，`ShoppingList` widget，它只构建刚刚好足够的 `ShoppingListItem` 实例来填充其可见区域：
- 如果没有 key，当前构建中的第一个条目将始终与前一个构建中的第一个条目同步，在语义上，列表中的第一个条目如果滚动出屏幕，那么它应该不会再在窗口中可见。
- 通过给列表中的每个条目分配为“语义” key，无限列表可以更高效，因为框架将通过相匹配的语义 key 来同步条目，并因此具有相似（或相同）的可视外观。此外，语义上同步条目意味着在有状态子 widget 中，保留的状态将附加到相同的语义条目上，而不是附加到相同数字位置上的条目。

有关更多信息，请参阅 [Key](https://api.flutter-io.cn/flutter/foundation/Key-class.html) API。

## 全局 key

全局 key 可以用来标识唯一子 widget。全局 key 在整个 widget 结构中必须是全局唯一的，而不像本地 key 只需要在兄弟 widget 中唯一。由于它们是全局唯一的，因此可以使用全局 key 来检索与 widget 关联的状态。

有关更多信息，请参阅 [GlobalKey](https://api.flutter-io.cn/flutter/widgets/GlobalKey-class.html) API。
