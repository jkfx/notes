每周一个小部件。

---

## FocusableActionDetector

```dart
GestureDetector(
    onTap: showDash,
    child: FocusableActionDetector(
        onShowHoverHighlight: onHover,
        onShowFocusHighlight: onFocust,
        actions: {MAP OF ACTIONS},
        shortcuts: {MAP OF SHORTCUTS},
        child: <Button>,
    ),
)
```

![image](https://tvax4.sinaimg.cn/large/006VTcCxly1h3fxumzpztj31bb0k0k0e.jpg)

---

## shared_preferences

所有设备都可用相同代码访问持久性存储。

![image](https://tvax2.sinaimg.cn/large/006VTcCxly1h3fxyhaj0zj31b60nrwog.jpg)

适合保存用户主题偏好之类的数据。

将 `SharedPreferences` 添加为应用的依赖：

```sh
flutter pub add shared_preferences
```

之后创建实例就可以保存数据：

```dart
SharedPreferences prefs = await SharedPreferences.getInstance();
```

```dart
prefs.setBool("darkMode", val);
```

还可以键值查询来取回数据：

```dart
prefs.getBool("darkMode");

// 若数据不存在将返回 null
dm != null
    ? darkMode = darkModePref
    : darkMode = false;
```

务必确认不要存放重要的应用数据。

---

## google_fonts

```dart
Text(
    'Dash is awesome!',
    style: GoogleFonts.lobster(),
)
```

![image](https://tva4.sinaimg.cn/large/006VTcCxly1h3fy94abprj30c00360t6.jpg)

也可以只改变字体：

```dart
Text(
    'Dash is awesome!',
    style: GoogleFonts.lobster(
        textStyle: TextStyle(
            fontSize: 20,
        ),
    ),
)
```

使用 google_fonts 来创建文本主题：

```dart
ThemeData(
    primarySwatch: Colors.blue,
    textTheme: GoogleFonts.lobsterTextTheme(
        Theme.of(context).textTheme,
    ),
)
```

默认情况下会经由 HTTP 来获取字体。

也可以将字体放到应用的 assets 文件中：

```dart
// pubspec.yaml
assets:
    - google_fonts/
```

之后设置 HTTP 请求为 false ：

```dart
GoogleFonts.config.allowRuntimeFetching = false;
```

---

## RepaintBoundary

首先在 main 方法中将 `debugRepaintRainbowEnable` 设置为 `true` ：

```dart
debugRepaintRainbowEnable = true;
runApp(MyApp());
```

RepaintBoundary 将子部件分离到自己的层中：

```dart
RepaintBoundary(
    child: MyAnimatingWidget(),
)
```

包装到 RepaintBoundary 之前要检查不必要的重绘，这样会带来一部分 CPU 和内存的开销。

---

## StatefulBuilder

可以从单独的部件中获得性能提升。

为了减少不必要的重绘将下面的代码：

```dart
bool isExpanded = false;

Widget build(context) {
    return Column(
        children: [
            InexpensiveStateful(
                isExpanded: isExpanded,
                onTap: (bool newValue) =>
                    setState(() => isExpanded = val),
            ),
            ExpensiveSateless(),
        ],
    );
}
```

使用 `StatefulBuilder` 包装 Stateful 的部分，改为：

```dart
bool isExpanded = false;

Widget build(context) {
    return Column(
        children: [
            StatefuleBuilder(
                builder: (ctx, setState) =>
                    InexpensiveStateful(
                        isExpanded: isExpanded,
                        onTap: (bool newValue) =>
                            setState(() => isExpanded = val),
                ),
            ),
            ExpensiveSateless(),
        ],
    );
}
```

---

## ScaffoldMessenger

```dart
ScaffoldMessenger.of(context);
```

---

## DropdownButton

从下拉列表选择选项：

```dart
DropdownButton(
    items: const [
        DropdownMenuItem(child: Text('Dash'), value: 'Dash'),
        DropdownMenuItem(child: Text('Sparky'), value: 'Sparky'),
        DropdownMenuItem(child: Text('Snoo'), value: 'Snoo'),
        DropdownMenuItem(child: Text('Clippy'), value: 'Clippy'),
    ].
)
```

![image](https://tva4.sinaimg.cn/large/006VTcCxly1h3fz40jp7uj30cn0fcmyl.jpg)

item 都在一个列表中，它们的类型都需要一样。

在选择新值时调用 setState 更新变量：

```dart
void dropdownCallback(String? selectedValue) {
    if (selectedValue is String) {
        setState(() {
            _dropdownValue = selectedValue;
        });
    }
}

DropdownButton(
    items: const [...],
    value: _dropdownValue,
    onChanged: dropdownCallback,
)
```

常用属性：

- iconSize
- iconEnabledColor
- icon
- isExpanded
- style（可以给文本上色）

---

## Badges

```dart
Badge(
    badgeContent: Text("9",
        style: const TextStyle(color: Colors.white),
    ),
    child: Icon(
        Icons.access_time,
    ),
    badgeColor: Colors.blue,
    animationType: BadgeAnimationType.fade,
    animationDuration: Duration(milliseconds: 250),
)
```

![image](https://tva3.sinaimg.cn/large/006VTcCxly1h3fzeib20bj31190fvn4x.jpg)

若要停止动画效果以及隐藏标记：

```dart
Badge(
    ...,
    // 停止动画
    toAnimate: false,
    // 隐藏标记
    showBadge: _voicemails > 0 ? true : false,
)
```

---

## Baseline

有时候不知道把文字的顶部放在哪里，但是知道文字的底部应该放在哪里，使用基线：

```dart
Baseline(
    baseline: 100,
    baselineType: TextBaseline.a;phabetic,
    child: MyText(),
)
```

![image](https://tva3.sinaimg.cn/large/006VTcCxly1h3fzond2t6j314y0mkn3d.jpg)

---

## get_it

在应用运行之前注册一个应用程序运行之后会需要用到的类：

```dart
GetIt.I.registerSingleton<MyDatabase>(
    MyDatabase(),
);
```

当需要时取回：

```dart
final MyDatabase db = GetIt.I.get<MyDatabase>();
```

如果不需要在注册时就初始化类，就使用懒式初始化：

```dart
GetIt.I.registerFactory<MyBloc>(
    () => MyBloc(),
);

final myBloc = GetIt.I.get<MyBloc>();
```

当只有大多数东西都是 widget 时，尝试 get_it 来定位那些不是的东西。

---

## path_provider

使用 path_provider 来访问设备的文件系统。

加载用户的文档目录：

```dart
Directory appDocDir = await
getApplicationDocumentsDirectory();
```

或者加载应用数据的支持目录：

```dart
Directory appSupportDir = await
getApplicationSupportDirectory();
```

也可以加载一个临时文件夹存放不超过当前会话的数据：

```dart
Directory appTempDir = await
getTemporaryDirectory();
```

---

## Freezed

使用之前添加如下内容到 `pubspec.yaml` 文件中：

```yaml
dependencies:
  freezed_annotation: any

dev_dependencies:
  build_runner: any
  freezed: any
  json_serializable: any
```

- 先用 `@Freezed()` 注解修饰一个类，指定将其纳入的超类
- 使用工厂构造器
- 在构造器中指定数据类所需字段
- 还可以使用 `@Default` 装饰器来分配默认值
- 加个 FromJson 构造函数
- 在文件顶部加入 `part` 的声明

```dart
part 'my_class.freezed.dart';
part 'my_class.g.dart';

@Freezed()
class MyClass with _$MyClas {
    const factory MyClass({
        required int id,
        @Default(true) bool isImportant,
    });

    factory MyClass.fromJson(Map<String, dynamic> json)
        => _$MyClassFromJson(json);
}
```

最后执行：

```sh
flutter pub build_runner build --delete-conflicting-outputs
```

---
