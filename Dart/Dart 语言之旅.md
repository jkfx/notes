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

- [Sets](https://dart.cn/guides/language/language-tour#sets) (`Set`)
- [Maps](https://dart.cn/guides/language/language-tour#maps) (`Map`)
- [Runes](https://dart.cn/guides/language/language-tour#characters) (常用于在 `Characters` API 中进行字符替换)

- [Symbols](https://dart.cn/guides/language/language-tour#symbols) (`Symbol`)
- The value null (`Null`)

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

#### 命名参数

命名参数默认为可选参数，除非他们被特别标记为 `required` 。

当你调用函数时，可以使用 `参数名: 参数值` 的形式来指定命名参数。

定义函数时，使用 `{参数1, 参数2, …}` 来指定命名参数。

虽然命名参数是可选参数的一种类型，但是你仍然可以使用 `required` 来标识一个命名参数是必须的参数，此时调用者必须为该参数提供一个值。

#### 可选的位置参数

使用 `[]` 将一系列参数包裹起来作为位置参数。

#### 默认参数值

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
