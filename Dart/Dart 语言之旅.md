## 重要概念

- 所有变量引用的都是**对象**，每个对象都是一个**类**的实例。数字、函数以及 `null` 都是对象。除去 `null` 以外（如果你开启了[空安全](https://dart.cn/null-safety)）, 所有的类都继承于 [Object](https://api.dart.cn/stable/dart-core/Object-class.html) 类。

- 尽管 Dart 是强类型语言，但是在声明变量时指定类型是可选的，因为 Dart 可以进行类型推断。

- 如果你开启了[空安全](https://dart.cn/null-safety)，变量在未声明为可空类型时不能为`null`。你可以通过在类型后加上问号 (`?`) 将类型声明为可空。例如，`int?` 类型的变量可以是整形数字或 `null`。如果你 **明确知道** 一个表达式不会为空，但 Dart 不这么认为时，你可以在表达式后添加 `!` 来断言表达式不为空（为空时将抛出异常）。例如：`int x = nullableButNotNullInt!`

- 如果你想要显式地声明允许任意类型，使用 `Object?`（如果你 [开启了空安全](https://dart.cn/null-safety#enable-null-safety)）、 Object 或者 [特殊类型 dynamic](https://dart.cn/guides/language/effective-dart/design#avoid-using-dynamic-unless-you-want-to-disable-static-checking) 将检查延迟到运行时进行。

- Dart 支持泛型，比如 `List<int>`（表示一组由 int 对象组成的列表）或 `List<Object>`（表示一组由任何类型对象组成的列表）。

- Dart 支持顶级函数（例如 `main` 方法），同时还支持定义属于类或对象的函数（即 *静态* 和 *实例方法*）。你还可以在函数中定义函数（*嵌套* 或 *局部函数*）。

- Dart 支持顶级 **变量**，以及定义属于类或对象的变量（*静态*和*实例变量*）。实例变量有时称之为域或属性。

- Dart 没有类似于 Java 那样的 `public`、`protected` 和 `private` 成员访问限定符。如果一个标识符以下划线 (`_`) 开头则表示该标识符在库内是私有的。可以查阅 [库和可见性](https://dart.cn/guides/language/language-tour#libraries-and-visibility) 获取更多相关信息。

- Dart 中 **表达式** 和 **语句** 是有区别的，表达式有值而语句没有。比如条件表达式 `expression condition ? expr1 : expr2` 中含有值 `expr1` 或 `expr2`。与 [if-else 分支语句](https://dart.cn/guides/language/language-tour#if-and-else)相比，`if-else` 分支语句则没有值。一个语句通常包含一个或多个表达式，但是一个表达式不能只包含一个语句。

- Dart 工具可以显示 警告 和 错误 两种类型的问题。警告表明代码可能有问题但不会阻止其运行。错误分为编译时错误和运行时错误；编译时错误代码无法运行；运行时错误会在代码运行时导致 [异常](https://dart.cn/guides/language/language-tour#exceptions)。

## 变量

```dart
var name = 'Geek JKFX';
```

变量仅存储对象的引用。如果一个对象的引用不局限于单一的类型，可以将其指定为 `Object`（或 `dynamic`）类型。

> 本文遵循 [风格建议指南](https://dart.cn/guides/language/effective-dart/design#types) 中的建议，通过 `var` 声明局部变量而非使用指定的类型。

### 默认值

在 Dart 中，未初始化以及可空类型的变量拥有一个默认的初始值 `null`。（如果你未迁移至 [空安全](https://dart.cn/null-safety)，所有变量都为可空类型。）即便数字也是如此，因为在 Dart 中一切皆为对象，数字也不例外。

如果你启用了空安全，那么你初始化一个*非可空*的变量时必须赋予初值。

你不必在定义声明一个局部变量的时候去进行初始化，但你需要使用其之前赋值。

*顶层*和*类变量*都是懒式初始化，初始化代码将在首次使用变量的时候执行。

### Late 变量

Dart 2.12 添加了 `late` 修饰符，其有如下两个使用场景：

- 声明一个非空变量，其在声明之后进行初始化操作

- 懒式初始化一个变量

如果你确保一个变量在使用之前被赋值，而Dart的控制流分析并没有检测到，你可以通过`late`修饰一个变量修复错误提示。

```dart
late String description;

void main() {
  description = 'Feijoada!';
  print(description);
}
```

> 如果你并没有初始化`late`变量，当变量被使用的时候会抛出运行时错误。

当你使用`late`修饰变量而在声明变量时不进行初始化，那么初始化操作将在变量首次被使用的时候执行。懒式初始化有几个方便的场景：

- 一个变量初始化操作非常耗时，并且不确定会被使用。

- 你初始化一个实例变量，并且初始化操作需要访问`this`。

### Final 和 Const

如果你不想更改一个变量，可以使用关键字 `final` 或者 `const` 修饰变量，这两个关键字可以替代 `var` 关键字或者加在一个具体的类型前。一个 `final` 变量只可以被赋值一次；一个 `const` 变量是一个编译时常量（`const` 变量同时也是 `final` 的）。顶层的 `final` 变量或者类的 `final` 变量在其第一次使用的时候被初始化。

> [实例变量](https://dart.cn/guides/language/language-tour#instance-variables) 可以是 `final` 的但不可以是 `const` 。

使用关键字 `const` 修饰变量表示该变量为 **编译时常量** 。如果使用 `const` 修饰类中的变量，则必须加上 `static` 关键字，即 `static const`（顺序不能颠倒）。在声明 `const` 变量时可以直接为其赋值，也可以使用其它的 `const` 变量为其赋值。

`const` 关键字不仅仅可以用来定义常量，还可以用来创建 常量值，该常量值可以赋予给任何变量。你也可以将构造函数声明为 `const` 的，这种类型的构造函数创建的对象是不可改变的。

你可以在常量中使用 [类型检查和强制类型转换](https://dart.cn/guides/language/language-tour#type-test-operators) (`is` 和 `as`)、 [集合中的 if](https://dart.cn/guides/language/language-tour#collection-operators) 以及 [展开操作符](https://dart.cn/guides/language/language-tour#spread-operator) (`...` 和 `...?`)。

> 尽管`final`对象不可以被修改，但是它的字段是可以改变的。对比之下，`const`对象和它的字段都是不可以被改变的，他们都是*immutable*的。

## 内置类型

Dart 语言支持下列内容：

- [Numbers](https://dart.cn/guides/language/language-tour#numbers) (`int`, `double`)

- [Strings](https://dart.cn/guides/language/language-tour#strings) (`String`)

- [Booleans](https://dart.cn/guides/language/language-tour#booleans) (`bool`)

- [Lists](https://dart.cn/guides/language/language-tour#lists) (也被称为 *arrays*)

---

- [Sets](https://dart.cn/guides/language/language-tour#sets) (`Set`)

- [Maps](https://dart.cn/guides/language/language-tour#maps) (`Map`)

- [Runes](https://dart.cn/guides/language/language-tour#characters) (常用于在 `Characters` API 中进行字符替换)

---

- [Symbols](https://dart.cn/guides/language/language-tour#symbols) (`Symbol`)

- The value null (`Null`)

---

一些特殊类型在Dart语言中也有着特别作用：

- `Object` 所有Dart类的超类（*superclass*），除了`Null`之外。

- `Future` 和 `Stream` 用在 [异步支持](https://dart.cn/guides/language/language-tour#asynchrony-support) 中。
  
- `Iterable` 用于 [for-in 循环](https://dart.cn/guides/libraries/library-tour#iteration) 以及*同步* [生成器函数](https://dart.cn/guides/language/language-tour#generator)。

- `Never` 表明一个表达式永远不会成功地完成评估（*evaluating*）。多数情况下用于总是抛出一个异常的函数。

- `dynamic` 表明你想要禁用静态检查。通常你应该使用`Object`或`Object?`代替。

- `void` 表明一个永远不会被使用的值。通常用作返回类型。

`Object`、`Object?`、`Null`和`Never`类在类层次（*class hierarchy*）中有着特殊作用，描述在[top-and-bottom](https://dart.cn/null-safety/understanding-null-safety#top-and-bottom)和[理解空安全](https://dart.cn/null-safety/understanding-null-safety)中。

### Number

Dart 支持两种 `Number` 类型：

[int](https://api.dart.cn/stable/dart-core/int-class.html)：整数值；长度不超过 64 位，具体取值范围 [依赖于不同的平台](https://dart.cn/guides/language/numbers)。在 DartVM 上其取值位于 -2^63 至 2^63 - 1 之间。在 Web 上，整型数值代表着 JavaScript 的数字（64 位无小数浮点型），其允许的取值范围在 -2^53 至 2^53 - 1 之间。

[double](https://api.dart.cn/stable/dart-core/double-class.html)：64 位的双精度浮点数字，且符合 IEEE 754 标准。

`int` 和 `double` 都是 [num](https://api.dart.cn/stable/dart-core/num-class.html) 的子类。 num 中定义了一些基本的运算符比如 +、-、*、/ 等，还定义了 `abs()`、`ceil()` 和 `floor()` 等方法（位运算符，比如 >> 定义在 int 中）。如果 num 及其子类不满足你的要求，可以查看 [dart:math](https://api.dart.cn/stable/dart-math) 库中的 API。

下面是字符串和数字之间转换的方式：

```dart
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(onePointOne == 1.1);

// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');
```

整型支持传统的位移操作，比如移位（`<<`、`>>` 和 `>>>`）、补码 (`~`)、按位与 (`&`)、按位或 (`|`) 以及按位异或 (`^`)。

更多示例请查看 [移位操作符](https://dart.cn/guides/language/language-tour#bitwise-and-shift-operators) 小节。

### String

Dart 字符串（String 对象）包含了 UTF-16 编码的字符序列。可以使用单引号或者双引号来创建字符串。

在字符串中，请以 `${表达式}` 的形式使用表达式，如果表达式是一个标识符，可以省略掉 `{}`。如果表达式的结果为一个对象，则 Dart 会调用该对象的 `toString` 方法来获取一个字符串。

可以使用 `+` 运算符或并列放置多个字符串来连接字符串。使用三个单引号或者三个双引号也能创建多行字符串。

在字符串前加上 `r` 作为前缀创建 “raw” 字符串（即不会被做任何处理（比如转义）的字符串）。

你可以查阅 [Runes 与 grapheme clusters](https://dart.cn/guides/language/language-tour#characters) 获取更多关于如何在字符串中表示 Unicode 字符的信息。

字符串字面量是一个编译时常量，只要是编译时常量 (null、数字、字符串、布尔) 都可以作为字符串字面量的插值表达式:

```dart
// These work in a const string.
const aConstNum = 0;
const aConstBool = true;
const aConstString = 'a constant string';

// These do NOT work in a const string.
var aNum = 0;
var aBool = true;
var aString = 'a string';
const aConstList = [1, 2, 3];

const validConstString = '$aConstNum $aConstBool $aConstString';
// const invalidConstString = '$aNum $aBool $aString $aConstList';
```

可以查阅 [字符串和正则表达式](https://dart.cn/guides/libraries/library-tour#strings-and-regular-expressions) 获取更多关于如何使用字符串的信息。

### 布尔类型

Dart 使用 `bool` 关键字表示布尔类型，布尔类型只有两个对象 `true` 和 `false`，两者都是编译时常量。

Dart 的类型安全不允许你使用类似 `if (nonbooleanValue)` 或者 `assert (nonbooleanValue)` 这样的代码检查布尔值。相反，你应该总是显示地检查布尔值。

### List

数组 (**Array**) 是几乎所有编程语言中最常见的集合类型，在 Dart 中数组由 [List](https://api.dart.cn/stable/dart-core/List-class.html) 对象表示。通常称之为 **List**。

List 的下标索引从 0 开始，第一个元素的下标为 0，最后一个元素的下标为 `list.length - 1`。

在 List 字面量前添加 `const` 关键字会创建一个编译时常量。

```dart
var constantList = const [1, 2, 3];
// constantList[1] = 1; // This line will cause an error.
```

Dart 在 2.3 引入了 **扩展操作符**（`...`）和 **空感知扩展操作符**（`...?`），它们提供了一种将多个元素插入集合的简洁方法。

例如，你可以使用扩展操作符（`...`）将一个 List 中的所有元素插入到另一个 List 中：

```dart
var list = [1, 2, 3];
var list2 = [0, ...list];
assert(list2.length == 4);
```

如果扩展操作符右边可能为 `null` ，你可以使用 *null-aware* 扩展操作符（`...?`）来避免产生异常：

```dart
var list2 = [0, ...?list];
assert(list2.length == 1);
```

可以查阅[扩展操作符建议](https://github.com/dart-lang/language/blob/master/accepted/2.3/spread-collections/feature-specification.md)获取更多关于如何使用扩展操作符的信息。

Dart 还同时引入了 **集合中的 if** 和 **集合中的 for** 操作，在构建集合时，可以使用条件判断 (`if`) 和循环 (`for`)。

下面示例是使用 **集合中的 if** 来创建一个 List 的示例，它可能包含 3 个或 4 个元素：

```dart
var nav = ['Home', 'Furniture', 'Plants', if (promoActive) 'Outlet'];
```

下面是使用 **集合中的 for** 将列表中的元素修改后添加到另一个列表中的示例：

```dart
var listOfInts = [1, 2, 3];
var listOfStrings = ['#0', for (var i in listOfInts) '#$i'];
assert(listOfStrings[1] == '#1');
```

你可以查阅 [集合中使用控制流建议](https://github.com/dart-lang/language/blob/master/accepted/2.3/control-flow-collections/feature-specification.md) 获取更多关于在集合中使用 `if` 和 `for` 的细节内容和示例。

List 类中有许多用于操作 List 的便捷方法，你可以查阅 [泛型](https://dart.cn/guides/language/language-tour#generics) 和 [集合](https://dart.cn/guides/libraries/library-tour#collections) 获取更多与之相关的信息。

### Set

在 Dart 中，`set` 是一组特定元素的无序集合。 Dart 支持的集合由集合的字面量（*literals*）和 [Set](https://api.dart.cn/stable/dart-core/Set-class.html) 类提供。

> **set 还是 map?** Map 字面量语法相似于 Set 字面量语法。因为先有的 Map 字面量语法，所以 `{}` 默认是 Map 类型。如果忘记在 `{}` 上注释类型或赋值到一个未声明类型的变量上，那么 Dart 会创建一个类型为 `Map<dynamic, dynamic>` 的对象。

使用 `add()` 方法或 `addAll()` 方法向已存在的 Set 中添加项目。使用 `.length` 可以获取 Set 中元素的数量。可以在 Set 变量前添加 `const` 关键字创建一个 Set 编译时常量。

从 Dart 2.3 开始，Set 可以像 List 一样支持使用扩展操作符（`...` 和 `...?`）以及 Collection `if` 和 `for` 操作。你可以查阅 [List 扩展操作符](https://dart.cn/guides/language/language-tour#spread-operator) 和 [List 集合操作符](https://dart.cn/guides/language/language-tour#collection-operators) 获取更多相关信息。

你也可以查阅 [泛型](https://dart.cn/guides/language/language-tour#generics) 以及 [Set](https://dart.cn/guides/libraries/library-tour#sets) 获取更多相关信息。

### Map

通常来说，Map 是用来关联 keys 和 values 的对象。其中键和值都可以是任何类型的对象。每个 *键* 只能出现一次但是 *值* 可以重复出现多次。 Dart 中 Map 提供了 Map 字面量以及 [Map](https://api.dart.cn/stable/dart-core/Map-class.html) 类型两种形式的 Map。

> 如果你之前是使用的 C# 或 Java 这样的语言，也许你想使用 `new Map()` 构造 Map 对象。但是在 Dart 中，`new` 关键词是可选的。(且不被建议使用) 你可以查阅 [构造函数的使用](https://dart.cn/guides/language/language-tour#using-constructors) 获取更多相关信息。

如果检索的 Key 不存在于 Map 中则会返回一个 null。使用 `.length` 可以获取 Map 中键值对的数量。在一个 Map 字面量前添加 `const` 关键字可以创建一个 Map 编译时常量。

Map 可以像 List 一样支持使用扩展操作符（`...` 和 `...?`）以及集合的 if 和 for 操作。

### Runes 与 grapheme cluster

在 Dart 中，[runes](https://api.dart.cn/stable/dart-core/Runes-class.html) 公开了字符串的 Unicode 码位。使用 [characters 包](https://pub.flutter-io.cn/packages/characters) 来访问或者操作用户感知的字符，也被称为 [Unicode (扩展) grapheme clusters](https://unicode.org/reports/tr29/#Grapheme_Cluster_Boundaries)。

Unicode 编码为每一个字母、数字和符号都定义了一个唯一的数值。因为 Dart 中的字符串是一个 UTF-16 的字符序列，所以如果想要表示 32 位的 Unicode 数值则需要一种特殊的语法。

表示 Unicode 字符的常见方式是使用 `\uXXXX`，其中 XXXX 是一个四位数的 16 进制数字。例如心形字符（♥）的 Unicode 为 `\u2665`。对于不是四位数的 16 进制数字，需要使用大括号将其括起来。例如大笑的 emoji 表情（😆）的 Unicode 为 `\u{1f600}`。

如果你需要读写单个 Unicode 字符，可以使用 `characters` 包中定义的 `characters` getter。它将返回 [Characters](https://pub.flutter-io.cn/documentation/characters/latest/characters/Characters-class.html) 对象作为一系列 grapheme clusters 的字符串。

### Symbol

[Symbol](https://api.dart.cn/stable/dart-core/Symbol-class.html) 表示 Dart 中声明的操作符或者标识符。你几乎不会需要 Symbol，但是它们对于那些通过名称引用标识符的 API 很有用，因为代码压缩后，尽管标识符的名称会改变，但是它们的 Symbol 会保持不变。

可以使用在标识符前加 # 前缀来获取 Symbol：

```dart
#radix
#bar
```

Symbol 字面量是编译时常量。

## 函数

Dart 是一种真正面向对象的语言，所以即便函数也是对象并且类型为 [Function](https://api.dart.cn/stable/dart-core/Function-class.html)，这意味着函数可以被赋值给变量或者作为其它函数的参数。你也可以像调用函数一样调用 Dart 类的实例。详情请查阅 [可调用的类](https://dart.cn/guides/language/language-tour#callable-classes)。

语法 `=> 表达式` 是 `{ return 表达式; }` 的简写， `=>` 有时也称之为 **箭头** 函数。

> 在 `=>` 与 `;` 之间的只能是 *表达式* 而非 *语句*。比如你不能将一个 [if语句](https://dart.cn/guides/language/language-tour#if-and-else) 放在其中，但是可以放置 [条件表达式](https://dart.cn/guides/language/language-tour#conditional-expressions)。

### 参数

函数可以有两种形式的参数：**必要参数** 和 **可选参数**。必要参数定义在参数列表前面，可选参数则定义在必要参数后面。可选参数可以是 **命名的** 或 **位置的**。

> 某些 API（特别是 [Flutter](https://flutter.cn/) 控件的构造器）只使用命名参数，即便参数是强制性的。

向函数传入参数或者定义函数参数时，可以使用 *尾逗号*。

### 命名参数

命名参数默认为可选参数，除非他们被特别标记为 `required` 。

当你调用函数时，可以使用 `参数名: 参数值` 的形式来指定命名参数。

定义函数时，使用 `{参数1, 参数2, …}` 来指定命名参数。

虽然命名参数是可选参数的一种类型，但是你仍然可以使用 `required` 来标识一个命名参数是必须的参数，此时调用者必须为该参数提供一个值。

### 可选的位置参数

使用 `[]` 将一系列参数包裹起来作为位置参数。

### 默认参数值

可以用 `=` 为函数的命名参数和位置参数定义默认值，默认值必须为编译时常量，没有指定默认值的情况下默认值为 `null` 。

### main() 函数

每个 Dart 程序都必须有一个 `main()` 顶级函数作为程序的入口， `main()` 函数返回值为 `void` 并且有一个 `List<String>` 类型的可选参数。

你可以通过使用 [参数库](https://pub.flutter-io.cn/packages/args) 来定义和解析命令行参数。

可以将函数作为参数传递给另一个函数。也可以将函数赋值给一个变量。

### 匿名函数

你可以创建一个没有名字的方法，称之为 **匿名函数**、 **Lambda 表达式** 或 **Closure 闭包**。你可以将匿名方法赋值给一个变量然后使用它

匿名方法看起来与命名方法类似，在括号之间可以定义参数，参数之间用逗号分割。

后面大括号中的内容则为函数体：

```dart
([[类型] 参数[, …]]) {
  函数体;
}; 
```

如果函数体内只有一行返回语句，你可以使用胖箭头缩写法。

### 词法作用域

Dart 是词法有作用域语言，变量的作用域在写代码的时候就确定了，大括号内定义的变量只能在大括号内访问，与 Java 类似。

### 词法闭包

**闭包** 即一个函数对象，即使函数对象的调用在它原始作用域之外，依然能够访问在它词法作用域内的变量。

函数可以封闭定义到它作用域内的变量。接下来的示例中，函数 `makeAdder()` 捕获了变量 `addBy`。无论函数在什么时候返回，它都可以使用捕获的 `addBy` 变量。

```dart


/// Returns a function that adds [addBy] to the
/// function's argument.
Function makeAdder(int addBy) {
  return (int i) => addBy + i;
}

void main() {
  // Create a function that adds 2.
  var add2 = makeAdder(2);

  // Create a function that adds 4.
  var add4 = makeAdder(4);

  assert(add2(3) == 5);
  assert(add4(3) == 7);
}
```

### 返回值

所有的函数都有返回值。没有显示返回语句的函数最后一行默认为执行 `return null;`。

## 运算符

Dart 可以将运算符实现为 [一个类的成员](https://dart.cn/guides/language/language-tour#_operators)。

| **描述**       | **运算符**                                  |
| -------------- | ------------------------------------------- |
| 一元后缀       | `表达式++ 表达式-- () [] . ?. !`            |
| 一元前缀       | `-表达式 !表达式 ~表达式 ++表达式 --表达式` |
| 乘除法         | `* / % ~/`                                  |
| 加减法         | `+ -`                                       |
| 位运算         | `<< >> >>>`                                 |
| 二进制与       | `&`                                         |
| 二进制异或     | `^`                                         |
| 二进制或       | `                                           | ` |
| 关系和类型测试 | `>= > <= < as is is!`                       |
| 相等判断       | `== !=`                                     |
| 逻辑与         | `&& `                                       |
| 逻辑或         | `                                           |   | ` |
| 空判断         | `??`                                        |
| 条件表达式     | `表达式 1 ? 表达式 2 : 表达式 3`            |
| 级联           | `.. ?..`                                    |
| 赋值           | `= *= /= += -= &= ^=` 等等……                |

### 类型判断运算符

`as`、`is`、`is!` 运算符是在运行时判断对象类型的运算符。

| **Operator** | **Meaning**                                                                                                 |
| ------------ | ----------------------------------------------------------------------------------------------------------- |
| `as`         | 类型转换（也用作指定 [类前缀](https://dart.cn/guides/language/language-tour#specifying-a-library-prefix))） |
| `is`         | 如果对象是指定类型则返回 *true*                                                                             |
| `is!`        | 如果对象是指定类型则返回 *false*                                                                            |

当且仅当 `obj` 实现了 `T` 的接口，`obj is T` 才是 true。例如 `obj is Object` 总为 true，因为所有类都是 Object 的子类。

仅当你确定这个对象是该类型的时候，你才可以使用 `as` 操作符可以把对象转换为特定的类型。

### 条件表达式

Dart 有两个特殊的运算符可以用来替代 [if-else](https://dart.cn/guides/language/language-tour#if-%E5%92%8C-else) 语句：

`条件 ? 表达式 1 : 表达式 2`

如果*条件*为 true，执行*表达式 1*并返回执行结果，否则执行*表达式 2* 并返回执行结果。

`表达式 1 ?? 表达式 2`

如果*表达式 1* 为非 null 则返回其值，否则执行*表达式 2* 并返回其值。

### 级联运算符

级联运算符 (`..`, `?..`) 可以让你在同一个对象上连续调用多个对象的变量或方法。

```dart
var paint = Paint()
  ..color = Colors.black
  ..strokeCap = StrokeCap.round
  ..strokeWidth = 5.0;
```

`Paint()` 构造器返回一个 `Point` 对象。在对象后跟的级联符号运算符，忽略了所有可能被返回的值。上面的代码等价于：

```dart
var paint = Paint();
paint.color = Colors.black;
paint.strokeCap = StrokeCap.round;
paint.strokeWidth = 5.0;
```

如果级联运算符用在了可能为null的对象上，那么使用 *null-shorting* （`?..`）在第一个操作上。以 `?..` 开头保证了任何级联运算符都是在非空对象上进行操作。

```dart
querySelector('#confirm') // Get an object.
  ?..text = 'Confirm' // Use its members.
  ..classes.add('important')
  ..onClick.listen((e) => window.alert('Confirmed!'));
```

上面的代码相当于：

```dart
var button = querySelector('#confirm');
button?.text = 'Confirm';
button?.classes.add('important');
button?.onClick.listen((e) => window.alert('Confirmed!'));
```

级联运算符可以嵌套。在返回对象的函数中谨慎使用级联操作符。

### 其他运算符

| **运算符** | **名字**      | **描述**                                                                                                        |
| ---------- | ------------- | --------------------------------------------------------------------------------------------------------------- |
| ()         | 使用方法      | 代表调用一个方法                                                                                                |
| []         | 访问 List     | 访问 List 中特定位置的元素                                                                                      |
| ?[]        | 判空访问 List | 左侧调用者不为空时，访问 List 中特定位置的元素                                                                  |
| .          | 访问成员      | 成员访问符                                                                                                      |
| ?.         | 条件访问成员  | 与上述成员访问符类似，但是左边的操作对象不能为 null，例如 foo?.bar，如果 foo 为 null 则返回 null ，否则返回 bar |
| !          | 空断言操作符  | 将表达式的类型转换为其基础类型，如果转换失败会抛出运行时异常。例如 foo!.bar，如果 foo 为 null，则抛出运行时异常 |

## 流程控制语句



- `if` 和 `else`

- `for` 循环

- `while` 和 `do-while` 循环

- `break` 和 `continue`

- `switch` 和 `case`

- `assert`


使用 `try-catch` 和 `throw` 也能影响控制流，详情参考[异常](https://dart.cn/guides/language/language-tour#exceptions)部分。

### 断言

在开发过程中，可以在条件表达式为 false 时使用 — `assert(条件, 可选信息);` — 语句来打断代码的执行。第二个参数可以为其添加一个字符串消息。

`assert` 的第一个参数可以是值为布尔值的任何表达式。如果表达式的值为 true，则断言成功，继续执行。如果表达式的值为 false，则断言失败，抛出一个 [AssertionError](https://api.dart.cn/stable/dart-core/AssertionError-class.html) 异常。

在生产环境代码中，断言会被忽略，与此同时传入 assert 的参数不被判断。

## 异常

与 Java 不同的是，Dart 的所有异常都是非必检异常，方法不必声明会抛出哪些异常，并且你也不必捕获任何异常。

Dart 提供了 [Exception](https://api.dart.cn/stable/dart-core/Exception-class.html) 和 [Error](https://api.dart.cn/stable/dart-core/Error-class.html) 两种类型的异常以及它们一系列的子类，你也可以定义自己的异常类型。但是在 Dart 中可以将任何非 null 对象作为异常抛出而不局限于 Exception 或 Error 类型。也可以抛出任意的对象。

### 捕获异常

捕获异常可以避免异常继续传递（重新抛出异常除外）。捕获一个异常可以给你处理它的机会。

对于可以抛出多种异常类型的代码，也可以指定多个 catch 语句，每个语句分别对应一个异常类型，如果 catch 语句没有指定异常类型则表示可以捕获任意异常类型：

```dart
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // A specific exception
  buyMoreLlamas();
} on Exception catch (e) {
  // Anything else that is an exception
  print('Unknown exception: $e');
} catch (e) {
  // No specified type, handles all
  print('Something really unknown: $e');
}
```

如上述代码所示可以使用 `on` 或 `catch` 来捕获异常，使用 `on` 来指定异常类型，使用 `catch` 来捕获异常对象，两者可同时使用。

你可以为 catch 方法指定两个参数，第一个参数为抛出的异常对象，第二个参数为栈信息 [StackTrace](https://api.dart.cn/stable/dart-core/StackTrace-class.html) 对象。

关键字 `rethrow` 可以将捕获的异常再次抛出。

### Finally

无论是否抛出异常，`finally` 语句始终执行，如果没有指定 `catch` 语句来捕获异常，则异常会在执行完 `finally` 语句后抛出。

`finally` 语句会在任何匹配的 `catch` 语句后执行。

你可以阅读 Dart 核心库概览的 [异常](https://dart.cn/guides/libraries/library-tour#exceptions) 章节获取更多相关信息。

## 类

Dart 是支持基于 mixin 继承机制的面向对象语言，所有对象都是一个类的实例，而除了 `Null` 以外的所有的类都继承自 [Object](https://api.dart.cn/stable/dart-core/Object-class.html) 类。 **基于 mixin 的继承** 意味着尽管每个类（[top class](https://dart.cn/null-safety/understanding-null-safety#top-and-bottom) `Object?` 除外）都只有一个超类，一个类的代码可以在其它多个类继承中重复使用。 [扩展方法](https://dart.cn/guides/language/language-tour#extension-methods) 是一种在不更改类或创建子类的情况下向类添加功能的方式。

### 使用类的成员

对象的 **成员** 由函数和数据（即 **方法** 和 **实例变量**）组成。方法的 **调用** 要通过对象来完成，这种方式可以访问对象的函数和数据。

使用（`.`）来访问对象的实例变量或方法：

使用 `?.` 代替 `.` 可以避免因为左边表达式为 null 而导致的问题。

### 使用构造函数

可以使用 **构造函数** 来创建一个对象。构造函数的命名方式可以为 `类名` 或 `类名 . 标识符` 的形式。

构造函数名前面的的 `new` 关键字是可选的。

一些类提供了[常量构造函数](https://dart.cn/guides/language/language-tour#constant-constructors)。使用常量构造函数，在构造函数名之前加 `const` 关键字，来创建编译时常量时。

两个使用相同构造函数相同参数值构造的编译时常量是同一个对象：

```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);

assert(identical(a, b)); // They are the same instance!
```

在 **常量上下文** 场景中，你可以省略掉构造函数或字面量前的 `const` 关键字。

### 获取对象的类型

可以使用 `Object` 对象的 `runtimeType` 属性在运行时获取一个对象的类型，该对象类型是 [Type](https://api.dart.cn/stable/dart-core/Type-class.html) 的实例。

> 使用 类型测试符 而不是 `runtimeType` 用来测试一个对象的类型。在生产环境中，测试 `object is Type` 要比 `object.runtimeType == Type` 稳定的多。

### 实例变量

所有未初始化的实例变量其值均为 `null` 。

所有实例变量均会隐式地声明一个 *Getter* 方法。非终值的实例变量和 `late final` 声明但未声明初始化的实例变量还会隐式地声明一个 *Setter* 方法。你可以查阅 [Getter 和 Setter](https://dart.cn/guides/language/language-tour#getters-and-setters) 获取更多相关信息。

实例变量可以是 `final` 的，它们只能被赋值一次，有几种方式：在声明处初始化 `final` 而非 `late` 的实例变量、使用构造器参数或使用构造器的 [初始化列表](https://dart.cn/guides/language/language-tour#initializer-list) 。

如果你需要在构造器体之后对一个 `final` 的实例变量赋值，你可以使用以下几种方法：

- 使用 工厂构造器 。

- 使用 `late final` 但是要 [注意](https://dart.cn/guides/language/effective-dart/design#avoid-public-late-final-fields-without-initializers) ：`late final` 并没有初始化器添加 setter 方法到 API。

### 构造函数

声明一个与类名一样的函数即可声明一个构造函数（对于[命名式构造函数](https://dart.cn/guides/language/language-tour#named-constructors) 还可以添加额外的标识符）。大部分的构造函数形式是生成式构造函数，其用于创建一个类的实例。

使用 `this` 关键字引用当前实例。

### 初始化参数

对于大多数编程语言来说在构造函数中为实例变量赋值的过程都是类似的，而 Dart 则提供了一种特殊的语法糖来简化该步骤。

构造中初始化的参数可以用于初始化非空或 `final` 修饰的变量，它们都必须被初始化或提供一个默认值。

```dart
class Point {
  final double x;
  final double y;

  // Sets the x and y instance variables
  // before the constructor body runs.
  Point(this.x, this.y);
}
```

### 默认构造函数

如果你没有声明构造函数，那么 Dart 会自动生成一个无参数的构造函数并且该构造函数会调用其父类的无参数构造方法。

### 构造函数不被继承

子类不会继承父类的构造函数，如果子类没有声明构造函数，那么只会有一个默认无参数的构造函数。

### 命名式构造函数

可以为一个类声明多个命名式构造函数来表达更明确的意图：

```dart
const double xOrigin = 0;
const double yOrigin = 0;

class Point {
  final double x;
  final double y;

  Point(this.x, this.y);

  // Named constructor
  Point.origin()
      : x = xOrigin,
        y = yOrigin;
}
```

记住构造函数是不能被继承的，这将意味着子类不能继承父类的命名式构造函数，如果你想在子类中提供一个与父类命名构造函数名字一样的命名构造函数，则需要在子类中显式地声明。

调用父类非默认构造函数

默认情况下，子类的构造函数会调用父类的匿名无参数构造方法，并且该调用会在子类构造函数的函数体代码执行前，如果子类构造函数还有一个 [初始化列表](https://dart.cn/guides/language/language-tour#initializer-list)，那么该初始化列表会在调用父类的该构造函数之前被执行，总的来说，这三者的调用顺序如下：

1. 初始化列表

1. 父类的无参数构造函数

1. 当前类的构造函数

如果父类没有匿名无参数构造函数，那么子类必须调用父类的其中一个构造函数，为子类的构造函数指定一个父类的构造函数只需在构造函数体前使用（`:`）指定。

> 传递给父类构造函数的参数不能使用 `this` 关键字，因为在参数传递的这一步骤，子类构造函数尚未执行，子类的实例对象也就还未初始化，因此所有的实例成员都不能被访问，但是类成员可以。

### 初始化列表

除了调用父类构造函数之外，还可以在构造函数体执行之前初始化实例变量。每个实例变量之间使用逗号分隔。

```dart
// Initializer list sets instance variables before
// the constructor body runs.
Point.fromJson(Map<String, double> json)
    : x = json['x']!,
      y = json['y']! {
  print('In Point.fromJson(): ($x, $y)');
}
```

> 初始化列表表达式 = 右边的语句不能使用 `this` 关键字。

### 重定向构造函数

有时候类中的构造函数仅用于调用类中其它的构造函数，此时该构造函数没有函数体，只需在函数签名后使用（:）指定需要重定向到的其它构造函数 (使用 `this` 而非类名)：

```dart
class Point {
  double x, y;

  // The main constructor for this class.
  Point(this.x, this.y);

  // Delegates to the main constructor.
  Point.alongXAxis(double x) : this(x, 0);
}
```

### 常量构造函数

如果类生成的对象都是不变的，可以在生成这些对象时就将其变为编译时常量。你可以在类的构造函数前加上 `const` 关键字并确保所有实例变量均为 `final` 来实现该功能。

```dart
class ImmutablePoint {
  static const ImmutablePoint origin = ImmutablePoint(0, 0);

  final double x, y;

  const ImmutablePoint(this.x, this.y);
}
```

常量构造函数创建的实例并不总是常量，具体可以参考[使用构造函数](https://dart.cn/guides/language/language-tour#using-constructors)章节。

### 工厂构造函数

使用 `factory` 关键字标识类的构造函数将会令该构造函数变为工厂构造函数，这将意味着使用该构造函数构造类的实例时**并非总是会返回新的实例对象**。例如，工厂构造函数可能会从缓存中返回一个实例，或者返回一个子类型的实例。

> 另一种处理懒加载变量的方式是 [使用 late final（谨慎使用）](https://dart.cn/guides/language/effective-dart/design#avoid-public-late-final-fields-without-initializers)。

在如下的示例中， `Logger` 的工厂构造函数从缓存中返回对象，和 `Logger.fromJson` 工厂构造函数从 JSON 对象中初始化一个最终变量。

```dart
class Logger {
  final String name;
  bool mute = false;

  // _cache is library-private, thanks to
  // the _ in front of its name.
  static final Map<String, Logger> _cache = <String, Logger>{};

  factory Logger(String name) {
    return _cache.putIfAbsent(name, () => Logger._internal(name));
  }

  factory Logger.fromJson(Map<String, Object> json) {
    return Logger(json['name'].toString());
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```

> 在工厂构造函数中无法访问 `this`。

工厂构造函数的调用方式与其他构造函数一样：

```dart
var logger = Logger('UI');
logger.log('Button clicked');

var logMap = {'name': 'UI'};
var loggerJson = Logger.fromJson(logMap);
```

### 方法

为了表示重写操作符，使用 `operator` 标识来进行标记。下面是重写 `+` 和 `-` 操作符的例子

```dart
class Vector {
  final int x, y;

  Vector(this.x, this.y);

  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);

  // Operator == and hashCode not shown.
  // ···
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```

### Getter 和 Setter

`Getter` 和 `Setter` 是一对用来读写对象属性的特殊方法，上面说过实例对象的每一个属性都有一个隐式的 Getter 方法，如果为非 final 属性的话还会有一个 Setter 方法，你可以使用 `get` 和 `set` 关键字为额外的属性添加 Getter 和 Setter 方法：

```dart
class Rectangle {
  double left, top, width, height;

  Rectangle(this.left, this.top, this.width, this.height);

  // Define two calculated properties: right and bottom.
  double get right => left + width;
  set right(double value) => left = value - width;
  double get bottom => top + height;
  set bottom(double value) => top = value - height;
}

void main() {
  var rect = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -8);
}
```

### 抽象方法

实例方法、Getter 方法以及 Setter 方法都可以是抽象的，定义一个接口方法而不去做具体的实现让实现它的类去实现该方法，抽象方法只能存在于 [抽象类](https://dart.cn/guides/language/language-tour#abstract-classes)中。

直接使用分号（`;`）替代方法体即可声明一个抽象方法：

```dart
abstract class Doer {
  // Define instance variables and methods...

  void doSomething(); // Define an abstract method.
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // Provide an implementation, so the method is not abstract here...
  }
}
```

### 隐式接口

每一个类都隐式地定义了一个接口并实现了该接口，这个接口包含所有这个类的实例成员以及这个类所实现的其它接口。如果想要创建一个 A 类支持调用 B 类的 API 且不想继承 B 类，则可以实现 B 类的接口。

一个类可以通过关键字 `implements` 来实现一个或多个接口并实现每个接口定义的 API：

```dart
// A person. The implicit interface contains greet().
class Person {
  // In the interface, but visible only in this library.
  final String _name;

  // Not in the interface, since this is a constructor.
  Person(this._name);

  // In the interface.
  String greet(String who) => 'Hello, $who. I am $_name.';
}

// An implementation of the Person interface.
class Impostor implements Person {
  String get _name => '';

  String greet(String who) => 'Hi $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Person('Kathy')));
  print(greetBob(Impostor()));
}
```

如果需要实现多个类接口，可以使用逗号分割每个接口类：

```dart
class Point implements Comparable, Location {...}
```

### 扩展一个类

使用 `extends` 关键字来创建一个子类，并可使用 `super` 关键字引用一个父类。

### 重写类成员

子类可以重写父类的实例方法（包括 [操作符](https://dart.cn/guides/language/language-tour#_operators)）、 Getter 以及 Setter 方法。你可以使用 `@override` 注解来表示你重写了一个成员。

一个重写方法的声明必须在几个方面与它所覆盖的方法相匹配。

- 返回类型必须与被覆盖方法的返回类型相同（或为其子类型）。

- 参数类型必须与被覆盖方法的参数类型相同（或者是超类型）。

- 如果被覆盖的方法接受n个位置参数，那么被覆盖的方法也必须接受n个位置参数。

- 一个[泛型方法](https://dart.cn/guides/language/language-tour#using-generic-methods)不能覆盖一个非泛型方法，而一个非泛型方法也不能覆盖一个泛型方法。

你可以使用 `covariant` 关键字 来缩小代码中那些符合 [类型安全](https://dart.cn/guides/language/type-system) 的方法参数或实例变量的类型。

> 如果重写 `==` 操作符，必须同时重写对象 `hashCode` 的 Getter 方法。你可以查阅 [实现映射键](https://dart.cn/guides/libraries/library-tour#implementing-map-keys) 获取更多关于重写的 `==` 和 `hashCode` 的例子。

### noSuchMethod 方法

如果调用了对象上不存在的方法或实例变量将会触发 `noSuchMethod` 方法，你可以重写 `noSuchMethod` 方法来追踪和记录这一行为：

```dart
class A {
  // Unless you override noSuchMethod, using a
  // non-existent member results in a NoSuchMethodError.
  @override
  void noSuchMethod(Invocation invocation) {
    print('You tried to use a non-existent member: '
        '${invocation.memberName}');
  }
}
```

只有下面其中一个条件成立时，你才能调用一个未实现的方法：

- 接收方是静态的 `dynamic` 类型。

- 接收方具有静态类型，定义了未实现的方法（抽象亦可），并且接收方的动态类型实现了 `noSuchMethod` 方法且具体的实现与 `Object` 中的不同。

你可以查阅 [noSuchMethod 转发规范](https://github.com/dart-lang/sdk/blob/master/docs/language/informal/nosuchmethod-forwarding.md) 获取更多相关信息。

### 扩展方法

扩展方法是向现有库添加功能的一种方式。你可能已经在不知道它是扩展方法的情况下使用了它。例如，当您在 IDE 中使用代码完成功能时，它建议将扩展方法与常规方法一起使用。

有关使用以及实现扩展方法的详细信息，请参阅 [扩展方法页面](https://dart.cn/guides/language/extension-methods)。

### 枚举类型

枚举类型是一种特殊的类型，也称为 **enumerations** 或 **enums**，用于定义一些固定数量的常量值。

### 使用枚举

使用关键字 `enum` 来定义枚举类型：

```dart
enum Color { red, green, blue }
```

你可以在声明枚举类型时使用 [尾随逗号](https://dart.cn/guides/language/language-tour#trailing-comma)。

每一个枚举值都有一个名为 `index` 成员变量的 Getter 方法，该方法将会返回以 0 为基准索引的位置值。

```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```

想要获得全部的枚举值，使用枚举类的 `values` 方法获取包含它们的列表：

```dart
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```

你可以在 `switch` 语句中使用枚举，但是需要注意的是必须处理枚举值的每一种情况，即每一个枚举值都必须成为一个 `case` 子句，不然会出现警告。

枚举类型有如下两个限制：

- 枚举不能成为子类，也不可以 mix in，你也不可以实现一个枚举。

- 不能显式地实例化一个枚举类。

### 使用 Mixin 为类添加功能

Mixin 是一种在多重继承中复用某个类中代码的方法模式。

使用 `with` 关键字并在其后跟上 Mixin 类的名字来使用 Mixin 模式：

```dart
class Musician extends Performer with Musical {
  // ···
}

class Maestro extends Person with Musical, Aggressive, Demented {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
```

想要实现一个 Mixin，请创建一个继承自 Object 且未声明构造函数的类。除非你想让该类与普通的类一样可以被正常地使用，否则请使用关键字 `mixin` 替代 `class`。

```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;

  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print('Humming to self');
    }
  }
}
```

可以使用关键字 `on` 来指定哪些类可以使用该 Mixin 类，比如有 Mixin 类 A，但是 A 只能被 B 类使用，则可以这样定义 A :

```dart
class Musician {
  // ...
}
mixin MusicalPerformer on Musician {
  // ...
}
class SingerDancer extends Musician with MusicalPerformer {
  // ...
}
```

### 类变量和方法

使用关键字 `static` 可以声明类变量或类方法。

### 静态变量

静态变量（即类变量）常用于声明类范围内所属的状态变量和常量。

静态变量在其首次被使用的时候才被初始化。

### 静态方法

静态方法（即类方法）不能对实例进行操作，因此不能使用 `this`。但是他们可以访问静态变量。

> 对于一些通用或常用的静态方法，应该将其定义为顶级函数而非静态方法。

可以将静态方法作为编译时常量。例如，你可以将静态方法作为一个参数传递给一个常量构造函数。

## 泛型

`<…>` 符号表示数组是一个 *泛型*（或 *参数化类型*） [通常](https://dart.cn/guides/language/effective-dart/design#do-follow-existing-mnemonic-conventions-when-naming-type-parameters) 使用一个字母来代表类型参数，比如 E、T、S、K 和 V 等等。

### 为什么使用泛型？

泛型常用于需要要求类型安全的情况，但是它也会对代码运行有好处：

- 适当地指定泛型可以更好地帮助代码生成。

- 使用泛型可以减少代码重复。

### 使用集合字面量

List、Set 以及 Map 字面量也可以是参数化的。定义参数化的 List 只需在中括号前添加 `<type>`；定义参数化的 Map 只需要在大括号前添加 `<keyType, valueType>`

```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var uniqueNames = <String>{'Seth', 'Kathy', 'Lars'};
var pages = <String, String>{
  'index.html': 'Homepage',
  'robots.txt': 'Hints for web robots',
  'humans.txt': 'We are people, not machines'
};
```

### 使用类型参数化的构造函数

在调用构造方法时也可以使用泛型，只需在类名后用尖括号（`<...>`）将一个或多个类型包裹即可：

```dart
var nameSet = Set<String>.from(names);
```

### 泛型集合以及它们所包含的类型

Dart的泛型类型是 **固化的**，这意味着即便在运行时也会保持类型信息：

```dart
var names = <String>[];
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```

> 与 Java 不同的是，Java 中的泛型是类型 **擦除** 的，这意味着泛型类型会在运行时被移除。在 Java 中你可以判断对象是否为 List 但不可以判断对象是否为 `List<String>`。

### 限制参数化类型

有时使用泛型的时候，你可能会想限制可作为参数的泛型范围，也就是参数必须是指定类型的子类，这时候可以使用 `extends` 关键字。

```dart
class Foo<T extends SomeBaseClass> {
  // Implementation goes here...
  String toString() => "Instance of 'Foo<$T>'";
}

class Extender extends SomeBaseClass {...}

var foo = Foo();
print(foo); // Instance of 'Foo<SomeBaseClass>'
```

### 使用泛型方法

起初 Dart 只支持在类的声明时指定泛型，现在同样也可以在方法上使用泛型，称之为 **泛型方法**：

```dart
T first<T>(List<T> ts) {
  // Do some initial work or error checking, then...
  T tmp = ts[0];
  // Do some additional checking or processing...
  return tmp;
}
```

你可以查阅 [使用泛型函数](https://github.com/dart-lang/sdk/blob/master/pkg/dev_compiler/doc/GENERIC_METHODS.md) 获取更多关于泛型的信息。

## 库和可见性

`import` 和 `library` 关键字可以帮助你创建一个模块化和可共享的代码库。代码库不仅只是提供 API 而且还起到了封装的作用：以下划线（`_`）开头的成员仅在代码库中可见。 **每个 Dart 程序都是一个库**，即便没有使用关键字 `library` 指定。

Dart 的库可以使用 [包工具](https://dart.cn/guides/packages) 来发布和部署。

### 使用库

使用 `import` 来指定命名空间以便其它库可以访问。

`import` 的唯一参数是用于指定代码库的 URI，对于 Dart 内置的库，使用 `dart:xxxxxx` 的形式。而对于其它的库，你可以使用一个文件系统路径或者以 `package:xxxxxx` 的形式。 `package:xxxxxx` 指定的库通过包管理器（比如 pub 工具）来提供

> **URI** 代表统一资源标识符。
> 
> **URL**（统一资源定位符）是一种常见的 URI。

### 指定库前缀

如果你导入的两个代码库有冲突的标识符，你可以为其中一个指定前缀。

```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.dart' as lib2;

// Uses Element from lib1.
Element element1 = Element();

// Uses Element from lib2.
lib2.Element element2 = lib2.Element();
```

### 导入库的一部分

如果你只想使用代码库中的一部分，你可以有选择地导入代码库。

```dart
// Import only foo.
import 'package:lib1/lib1.dart' show foo;

// Import all names EXCEPT foo.
import 'package:lib2/lib2.dart' hide foo;
```

### 延迟加载库

延迟加载（也常称为 懒加载）允许应用在需要时再去加载代码库。

> **目前只有 dart2js 支持延迟加载** Flutter、Dart VM 以及 DartDevc 目前都不支持延迟加载。

使用 `deferred as` 关键字来标识需要延时加载的代码库。

### 实现库

查阅 [创建依赖库包](https://dart.cn/guides/libraries/create-library-packages) 可以获取有关如何实现库包的建议，包括：

- 如何组织库的源文件。

- 如何使用 `export` 命令。

- 何时使用 `part` 命令。

- 何时使用 `library` 命令。

- 如何使用导入和导出命令实现多平台的库支持。

## 异步支持

Dart 代码库中有大量返回 [Future](https://api.dart.cn/stable/dart-async/Future-class.html) 或 [Stream](https://api.dart.cn/stable/dart-async/Stream-class.html) 对象的函数，这些函数都是 **异步** 的，它们会在耗时操作（比如I/O）执行完毕前直接返回而不会等待耗时操作执行完毕。

`async` 和 `await` 关键字用于实现异步编程，并且让你的代码看起来就像是同步的一样。

### 处理 Future

可以通过下面两种方式，获得 Future 执行完成的结果：

- 使用 `async` 和 `await`，在 [异步编程 codelab](https://dart.cn/codelabs/async-await) 中有更多描述；

- 使用 Future API，具体描述参考 [库概览](https://dart.cn/guides/libraries/library-tour#future)。

使用 `async` 和 `await` 的代码是异步的，但是看起来有点像同步代码。

必须在带有 `async` 关键字的 **异步函数** 中使用 `await`。

```dart
Future<void> checkVersion() async {
  var version = await lookUpVersion();
  // Do something with version
}
```

> 尽管异步函数可以处理耗时操作，但是它并不会等待这些耗时操作完成，异步函数执行时会在其遇到第一个 `await` 表达式（[代码行](https://github.com/dart-lang/sdk/blob/main/docs/newsletter/20170915.md#synchronous-async-start)）时返回一个 Future 对象，然后等待 await 表达式执行完毕后继续执行。

使用 `try` 、 `catch` 以及 `finally` 来处理使用 `await` 导致的异常：

```dart
try {
  version = await lookUpVersion();
} catch (e) {
  // React to inability to look up the version
}
```

你可以在异步函数中多次使用 await 关键字。

> `await` 表达式的返回值通常是一个 Future 对象；如果不是的话也会自动将其包裹在一个 Future 对象里。 Future 对象代表一个“承诺”， `await` 表达式会阻塞直到需要的对象返回。

如果在使用 `await` 时导致编译错误，请确保 `await` 在一个异步函数中使用。

### 声明异步函数

**异步函数** 是函数体由 `async` 关键字标记的函数。

将关键字 `async` 添加到函数并让其返回一个 Future 对象。

如果函数没有返回有效值，需要设置其返回类型为 `Future<void>`。

### 处理 Stream

如果想从 Stream 中获取值，可以有两种选择：

- 使用 `async` 关键字和一个 **异步循环**（使用 `await for` 关键字标识）。

- 使用 Stream API。详情参考 [库概览](https://dart.cn/guides/libraries/library-tour#stream)。

使用 `await for` 定义异步循环看起来是这样的：

```dart
await for (varOrType identifier in expression) {
  // Executes each time the stream emits a value.
}
```

`expression` 的类型必须是 Stream。执行流程如下：

1. 等待直到 Stream 返回一个数据。

1. 使用 1 中 Stream 返回的数据执行循环体。

1. 重复 1、2 过程直到 Stream 数据返回完毕。

使用 `break` 和 `return` 语句可以停止接收 Stream 数据，这样就跳出了循环并取消注册监听 Stream。

如果在实现异步 for 循环时遇到编译时错误，请检查确保 `await for` 处于异步函数中。

你可以查阅库概览中有关 `dart:async` 的部分获取更多有关异步编程的信息。

## 生成器

当你需要延迟地生成一连串的值时，可以考虑使用 *生成器函数*。Dart 内置支持两种形式的生成器方法：

- **同步** 生成器：返回一个 [Iterable](https://api.dart.cn/stable/dart-core/Iterable-class.html) 对象。

- **异步** 生成器：返回一个 [Stream](https://api.dart.cn/stable/dart-async/Stream-class.html) 对象。

通过在函数上加 `sync*` 关键字并将返回值类型设置为 Iterable 来实现一个 **同步** 生成器函数，在函数中使用 `yield` 语句来传递值：

```dart
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}
```

实现 **异步** 生成器函数与同步类似，只不过关键字为 `async*` 并且返回值为 Stream：

```dart
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}
```

如果生成器是递归调用的，可以使用 `yield*` 语句提升执行性能：

```dart
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```

## 可调用类

通过实现类的 `call()` 方法，允许使用类似函数调用的方式来使用该类的实例。

## 隔离区

大多数计算机中，甚至在移动平台上，都在使用多核 CPU。为了有效利用多核性能，开发者一般使用共享内存的方式让线程并发地运行。然而，多线程共享数据通常会导致很多潜在的问题，并导致代码运行出错。

为了解决多线程带来的并发问题，Dart 使用 isolate 替代线程，所有的 Dart 代码均运行在一个 **isolate** 中。每一个 isolate 有它自己的堆内存以确保其状态不被其它 isolate 访问。

所有的 Dart 代码都是在一个 isolate 中运行，而非线程。每个 isolate 都有一个单独的执行线程，并且不与其他的 isolate 共享任何可变对象。

## Typedef

类型别名是引用某一类型的简便方法，因为其使用关键字 `typedef`，因此通常被称作 **typedef**。

```dart
typedef IntList = List<int>;
IntList il = [1, 2, 3];
```

类型别名可以有类型参数:

```dart
typedef ListMapper<X> = Map<X, List<X>>;
Map<String, List<String>> m1 = {}; // Verbose.
ListMapper<String> m2 = {}; // Same thing but shorter and clearer.
```

## 元数据

使用元数据可以为代码增加一些额外的信息。元数据注解以 `@` 开头，其后紧跟一个编译时常量（比如 `deprecated`）或者调用一个常量构造函数。

Dart 中有两个注解是所有代码都可以使用的： `@deprecated`、`@Deprecated` 和 `@override`。你可以查阅 [扩展一个类](https://dart.cn/guides/language/language-tour#extending-a-class) 获取有关 `@override` 的使用示例。

可以自定义元数据注解。下面的示例定义了一个带有两个参数的 @todo 注解：

```dart
library todo;

class Todo {
  final String who;
  final String what;

  const Todo(this.who, this.what);
}
```

使用 @Todo 注解的示例：

```dart
import 'todo.dart';

@Todo('seth', 'make this do something')
void doSomething() {
  print('do something');
}
```

元数据可以在 library、class、typedef、type parameter、 constructor、factory、function、field、parameter 或者 variable 声明之前使用，也可以在 import 或 export 之前使用。可使用反射在运行时获取元数据信息。

## 注释

Dart 支持单行注释、多行注释和文档注释。

### 单行注释

单行注释以 `//` 开始。所有在 `//` 和该行结尾之间的内容均被编译器忽略。

### 多行注释

多行注释以 `/*` 开始，以 `*/` 结尾。所有在 `/*` 和 `*/` 之间的内容均被编译器忽略（不会忽略文档注释），多行注释可以嵌套。

### 文档注释

文档注释可以是多行注释，也可以是单行注释，文档注释以 `///` 或者 `/**` 开始。在连续行上使用 `///` 与多行文档注释具有相同的效果。

在文档注释中，除非用中括号括起来，否则分析器会忽略所有文本。使用中括号可以引用类、方法、字段、顶级变量、函数和参数。括号中的符号会在已记录的程序元素的词法域中进行解析。

在生成的文档中，`[feed]` 会成为一个链接，指向 `feed` 方法的文档， `[Food]` 会成为一个链接，指向 `Food` 类的 API 文档。

解析 Dart 代码并生成 HTML 文档，可以使用 Dart 的文档生成工具 [dart doc](https://dart.cn/tools/dartdoc)。

## 总结

本页概述了 Dart 语言中常用的功能。还有更多特性有待实现，但我们希望它们不会破坏现有代码。有关更多信息，请参考 [Dart 语言规范](https://dart.cn/guides/language/spec) 和 [高效 Dart 语言指南](https://dart.cn/guides/language/effective-dart)。

要了解更多关于 Dart 核心库的内容，请参考 [Dart 核心库概览](https://dart.cn/guides/libraries/library-tour)。
