# Dart学习笔记


Dart据说是一款高效、简洁、已通过实战检验的语言，今天先从语言层面的基础开始学习，需要说明的是，这个笔记是以已有其他语言编程经验的人的角度写的（但不会JS），所以突出了一些非共性的地方方便理解。

<!--more-->

# Dart学习笔记

### 1.Dart简介

Dart是谷歌开发的计算机语言，它是开源的，诞生于2011年10月10日。Dart是一款高效、简洁、已通过实战检验的语言，能够应对现代应用程序开发的挑战，并大大加强和精简了类型系统，清理了语法，并重建了大部分开发工具链，使得移动和Web开发更愉快高效。

Dart有着这样一些特性，被Flutter团队看重：

- AOT（Ahead Of Time）编译，编译成快速、可预测的本地代码，使得Flutter变得更快且几乎所有组件可定制
- 也可以JIT（Just In Time）编译，开发周期非常短，支持了Flutter流行的亚秒级有状态热重载
- 可以在没有锁的情况下进行对象分配与垃圾回收
- 不需要单独的声明式布局语言或单独的可视化界面构建器，因为其声明式编程易读且可视化，Flutter容易提供高级工具使布局更简单
- 易于学习，具有静态语言和动态语言用户都熟悉的特性

这些特性并不是Dart所独有的，但Dart将它们组合的恰到好处

在Dart里，有这样一些**重要特性**：

- 所有东西都是对象，包括变量、数字、函数等，所有的对象都是类的实例，且继承自内置的Object类
- 弱数据类型，支持动态类型dynamic
- Dart在运行前解析，指定数据类型和编译时常量能提高运行速度
- 统一的程序入口main()
- 没有public、protected与private，私有特性通过在变量或函数加上下划线表示
- 支持anync/await异步处理



### 2.变量

声明变量使用var关键字，**变量保存的是对象的引用**，也可以使用对象类型和动态类型（dynamic）来声明。未初始化的变量默认值是 null。即使变量是数字类型默认值也是 null，因为在 Dart 中一切都是对象。

```dart
var nameA = 'jmmmjA';
String nameB = 'jmmmjB';
dynamic nameC = 'jmmmjC';

int a;
assert(a == null);
```

且在声明局部变量时，官方的代码风格建议使用var来声明。

声明常量使用final或const关键字，其区别是：const用于编译时常量，其实际上是隐式的final类型，还可以用来创建常量值；而final声明的常量可以且仅可以被赋值一次。

数字类型字面量是编译时常量。 在算术表达式中，只要参与计算的因子是编译时常量， 那么算术表达式的结果也是编译时常量。

```dart
final a = 1;	
final int b = 2;
//这两种都是可以的
```



### 3.基本数据类型

Dart内建类型包括：Number、String、Boolean、List、Map

Number：包括int与double，整数值不大于64位， 具体取决于平台。 在 Dart VM 上， 值的范围从 -263 到 263 - 1。Dart 被编译为 JavaScript 时，使用 [JavaScript numbers,](https://stackoverflow.com/questions/2802957/number-of-bits-in-javascript-numbers/2803010#2803010) 值的范围从 -253 到 253 - 1；double是64位（双精度）浮点数，依据 IEEE 754 标准。

String：Dart 字符串是一组 UTF-16 单元序列。 字符串通过单引号或者双引号创建，可以通过 `${`*`expression`*`}` 的方式内嵌表达式。 如果表达式是一个标识符，则 {} 可以省略。 在 Dart 中通过调用就对象的 `toString()` 方法来得到对象相应的字符串。在字符串中，如果两个字符串包含了相同的编码序列，那么这两个字符串相等，即可以用 == 运算符比较两个字符串是否相等。字符串直接可以用+运算符。

Boolean：Dart 使用 `bool` 类型表示布尔值。 Dart 只有字面量 `true` and `false` 是布尔类型， 这两个对象都是编译时常量；Dart 支持类型安全，意味着不能使用 `if (nonbooleanValue)` 或者 `assert (nonbooleanValue)`。 而是应该像下面这样，明确的进行值检查：

```dart
// 检查空字符串。
var fullName = '';
assert(fullName.isEmpty);

// 检查 0 值。
var hitPoints = 0;
assert(hitPoints <= 0);

// 检查 null 值。
var unicorn;
assert(unicorn == null);

// 检查 NaN 。
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```

List：表示数组，在 Dart 中的 *Array* 就是 List对象， 通常称之为 *List* 。List 类型包含了很多 List 的操作函数。其字面量用`[]`表示。

Map：用于关联key/value，其字面量用`{key:value}`表示，且{}默认表示Map。

Set：表示集合，在Dart2.2中引入，其字面量用`{}`表示。

Rune：Rune 用来表示字符串中的 UTF-32 编码字符。由于 Dart 字符串是一系列 UTF-16 编码单元， 因此要在字符串中表示32位 Unicode 值需要特殊语法支持。

Symbol：一个 Symbol 对象表示 Dart 程序中声明的运算符或者标识符。 你也许永远都不需要使用 Symbol ，但要按名称引用标识符的 API 时， Symbol 就非常有用了。 因为代码压缩后会改变标识符的名称，但不会改变标识符的符号。 通过字面量 Symbol ，也就是标识符前面添加一个 `#` 号，来获取标识符的 Symbol 。Symbol 字面量是编译时常量。



### 4.函数

函数是对象，类型是*Function*，意味着函数也可以作为参数直接传入函数。

Dart里的函数具有这些特性：

- 所有应用都需要有main()

- 支持箭头语法`=>{ return expr; } `或`=> expr;`

- 支持可选参数，包括命名可选参数与位置可选参数，命名可选参数是在调用函数时，可以使用指定命名参数 `paramName: value`，要求定义函数时，使用 `{param1, param2, …}` 来指定命名参数；位置可选参数则用`[]`括起来即可。

  使用 @required注释表示参数是 *required* 性质的命名参数。

  Flutter 创建实例的表达式可能很复杂， 因此窗口小部件构造函数仅使用命名参数。 这样创建实例的表达式更易于阅读。

- 支持参数默认值，使用方式如`[int a = 1]`,这样a是可选的，且未传入时默认值为1

- 支持匿名函数

  ([[*Type*] *param1*[, …]]) {
   *codeBlock*;
  };

- 所有函数都有返回值，若没有指定则默认返回null（系统会在最后添加隐式return）

- 支持词法闭包

  *闭包* 即一个函数对象，即使函数对象的调用在它原始作用域之外， 依然能够访问在它词法作用域内的变量。

  函数可以封闭定义到它作用域内的变量。 接下来的示例中， `makeAdder()` 捕获了变量 `addBy`。 无论在什么时候执行返回函数，函数都会使用捕获的 `addBy` 变量。

  用 == 比较闭包相等时，若是引用不同实例对象的同名方法会得到false

  ```dart
  Function makeAdder(num addBy) {
    return (num i) => addBy + i;
  }
  
  void main() {
    // 创建一个加 2 的函数。
    var add2 = makeAdder(2);
  
    // 创建一个加 4 的函数。
    var add4 = makeAdder(4);
  
    print(add2(3) == 5);//true
    print(add4(3) == 7);//true
  }
  ```

  

### 5.运算符

Dart定义了多种运算符，且大部分可重载，部分较为独特的运算符如下：

- `~/` 返回一个整数值的除法

- `??` a??会在a不为null时返回true，否则为false  

- `expr1??expr2` 如果 *expr1* 是 non-null， 返回 *expr1* 的值； 否则, 执行并返回 *expr2* 的值

- `as` 类型转换，且类型不符合或为null时会抛出异常

- `is` 对象是相应类型时返回true

- `is!` 对象不是相应类型时返回true

- `??=` 左为null时赋值

- `..` 级联符，可以实现对同一个对像进行一系列的操作。 除了调用函数， 还可以访问同一对象上的字段属性。 且可以嵌套。

  ```dart
  querySelector('#confirm') // 获取对象。
    ..text = 'Confirm' // 调用成员变量。
    ..classes.add('important')
    ..onClick.listen((e) => window.alert('Confirmed!'));
  
  // 上面的写法和下面等价
  
  var button = querySelector('#confirm');
  button.text = 'Confirm';
  button.classes.add('important');
  button.onClick.listen((e) => window.alert('Confirmed!'));
  ```

- `?.` 用法如a?.bar，在a不为空时和a.bar相等，在a为空时则为null

在使用二元运算符时，左操作数会决定使用的操作符种类，如aVector+aPoint所使用的+是Vector定义的



### 6.流程控制

dart的控制语句如下：

- `if` and `else`
- `for` loops
- `while` and `do`-`while` loops
- `break` and `continue`
- `switch` and `case`
- `assert`
- `try/catch`

需要注意的是条件表达式需要为Boolean类型，且对实现了Iterable 接口的对象（如List和Set）可以使用forEach()方法与for in语句

```dart
for (int i = 0; i < candidates.length; i++) {
  var candidate = candidates[i];
  if (candidate.yearsExperience < 5) {
    continue;
  }
  candidate.interview();
}

//当candidate实现了Iterable时，上面这段代码可以改为
candidates
    .where((c) => c.yearsExperience >= 5)
    .forEach((c) => c.interview());
```

assert 语句只在开发环境中有效， 在生产环境是无效的； Flutter 中的 assert 只在 debug 模式 中有效。 开发用的工具，例如 dartdevc 默认是开启 assert 功能。 其他的一些工具， 例如 dart 和 dart2js, 支持通过命令行开启 assert ： --enable-asserts。

assert 的第一个参数可以是解析为布尔值的任何表达式。 如果表达式结果为 true ， 则断言成功，并继续执行。 如果表达式结果为 false ， 则断言失败，并抛出异常 (AssertionError) ；assert 的第二个参数可以为其添加一个字符串消息。如：

```dart
assert(urlString.startsWith('https'),
    'URL ($urlString) should start with "https".');
```

### 7.异常

和java不同的是，Dart 中的所有异常是非检查异常。 方法不会声明它们抛出的异常， 也不要求捕获任何异常。Dart 提供了 Exception 和 Error 类型， 以及一些子类型。 当然也可以定义自己的异常类型。 但是，此外 Dart 程序可以抛出任何非 null 对象， 不仅限 Exception 和 Error 对象。

捕获语句中可以同时使用 `on` 和 `catch` ，也可以单独分开使用。 使用 on 来指定异常类型， 使用 catch 来 捕获异常对象。catch() 函数可以指定1到2个参数， 第一个参数为抛出的异常对象， 第二个为堆栈信息 ( 一个 StackTrace 对象 )。

如果仅需要部分处理异常， 那么可以使用关键字 `rethrow` 将异常重新抛出。

```D
//异常示例
try {
  breedMoreLlamas();
} on OutOfLlamasException {
  // 一个特殊的异常
  buyMoreLlamas();
} on Exception catch (e) {
  // 其他任何Exception
  print('Unknown exception: $e');
} catch (e, s) {
  print('Exception details:\n $e');
  print('Stack trace:\n $s');
  rethrow;
}finally{

}
```



### 8.类与泛型

#### 构造函数

- 构造函数构造函数的名字可以是 `ClassName` 或者 `ClassName.identifier`。**在没有声明构造函数的情况下， Dart 会提供一个默认的构造函数， 默认构造函数没有参数并会调用父类的无参构造函数。**使用构造函数时的new关键字是可选的。

- 构造函数支持**初始化列表**，类似C++。

- 支持**重定向构造函数**。

  ```dart
  class Point {
    num x, y;
  
    Point(num x, num y) {
      this.x = x;
      this.y = y;
    }
    //根据dart语法糖可以这样写： Point(this.x, this.y);
      
    // 命名构造函数
    Point.origin() {
      x = 0;
      y = 0;
    }
      
    // 指向主构造函数
    Point.alongXAxis(num x) : this(x, 0);
  }
  ```

- 支持[**常量构造函数**](https://www.dartcn.com/guides/language/language-tour#常量构造函数)，如果该类生成的对象是固定不变的， 那么就可以把这些对象定义为编译时常量。 为此，需要定义一个 `const` 构造函数， 并且声明所有实例变量为 `final`。 使用常量构造函数构造常量时，需要在构造函数名之前加 `const` 关键字；构造两个相同的编译时常量会产生一个唯一的， 标准的实例。在 *常量上下文* 中， 构造函数或者字面量前的 `const` 可以省略。 例如，下面代码创建了一个 const 类型的 map 对象，可以保留第一个 `const` 关键字，其余的全部省略。如果常量构造函数在常量上下文之外， 且省略了 `const` 关键字， 此时创建的对象是非常量对象。使用对象的 `runtimeType` 属性， 可以在运行时获取对象的类型， `runtimeType` 属性回返回一个 [Type](https://api.dartlang.org/stable/dart-core/Type-class.html) 对象。

  ```dart
  // 这里有很多的 const 关键字。
  const pointAndLine = const {
    'point': const [const ImmutablePoint(0, 0)],
    'line': const [const ImmutablePoint(1, 10), const ImmutablePoint(-2, 11)],
  };
  // 仅有一个 const ，由该 const 建立常量上下文。
  const pointAndLine = {
    'point': [ImmutablePoint(0, 0)],
    'line': [ImmutablePoint(1, 10), ImmutablePoint(-2, 11)],
  }; 
  ```


- 支持**工厂构造函数**：当执行构造函数并不总是创建这个类的一个新实例时，则使用 `factory` 关键字。 例如，一个工厂构造函数可能会返回一个 cache 中的实例， 或者可能返回一个子类的实例。以下示例演示了从缓存中返回对象的工厂构造函数：

  ```dart
  class Logger {
    final String name;
    bool mute = false;
  
    // 从命名的 _ 可以知，
    // _cache 是私有属性。
    static final Map<String, Logger> _cache =
        <String, Logger>{};
  
    factory Logger(String name) {
      if (_cache.containsKey(name)) {
        return _cache[name];
      } else {
        final logger = Logger._internal(name);
        _cache[name] = logger;
        return logger;
      }
    }
  
    Logger._internal(this.name);
  
    void log(String msg) {
      if (!mute) print(msg);
    }
  }
  
  // 工厂构造函数无法访问 this。
  ```

**子类不会继承父类的构造函数**。 子类不声明构造函数，那么它就只有默认构造函数 (匿名，没有参数) 。 这意味着父类的命名构造函数不会被子类继承。 如果希望使用父类中定义的命名构造函数创建子类， 就必须在子类中实现该构造函数。

**默认情况下，子类的构造函数会自动调用父类的默认构造函数（匿名，无参数）**。 父类的构造函数在子类构造函数体开始执行的位置被调用。 如果提供了一个 [initializer list](https://www.dartcn.com/guides/language/language-tour#initializer-list) （初始化参数列表）， 则初始化参数列表在父类构造函数执行之前执行。 执行顺序如下：

1. initializer list （初始化参数列表）
2. superclass’s no-arg constructor （父类的无名构造函数，不能访问this）
3. main class’s no-arg constructor （主类的无名构造函数）

如果父类中没有匿名无参的构造函数， 则需要手工调用父类的其他构造函数。 在当前构造函数冒号 (`:`) 之后，函数体之前，声明调用父类构造函数。

#### 方法与变量

- dart里定义非构造函数的方法默认是实例方法，定义类方法则在前加static关键字，但对于常见或广泛使用的工具和函数， 应该考虑使用顶级函数而不是静态方法。静态函数可以当做编译时常量使用。 例如，可以将静态方法作为参数传递给常量构造函数。

- 定义静态变量也是加static关键字

- Getter 和 Setter 是用于对象属性读和写的特殊方法。 回想之前的例子，每个实例变量都有一个隐式 Getter ，通常情况下还会有一个 Setter 。 使用 `get` 和 `set` 关键字实现 Getter 和 Setter ，能够为实例创建额外的属性。

  ```dart
  class Rectangle {
    num left, top, width, height;
  
    Rectangle(this.left, this.top, this.width, this.height);
  
    // 定义两个计算属性： right 和 bottom。
    num get right => left + width;
    set right(num value) => left = value - width;
    num get bottom => top + height;
    set bottom(num value) => top = value - height;
  }
  
  void main() {
    var rect = Rectangle(3, 4, 20, 15);
    assert(rect.left == 3);
    rect.right = 12;
    assert(rect.left == -8);
  }
  ```

  最开始实现 Getter 和 Setter 也许是直接返回成员变量； 随着需求变化， Getter 和 Setter 可能需要进行计算处理而使用方法来实现； 但是，调用对象的代码不需要做任何的修改。

  类似 (++) 之类操作符不管是否定义了 getter 方法，都能够正确的执行。 为了避免一些问题，操作符只调用一次 getter 方法， 然后把值保存到一个临时的变量中。

  

  

#### 抽象类与抽象方法

抽象类：定义抽象类需要加abstract关键字，抽象类里可以定义工厂构造函数来实例化

抽象方法：抽象类里的方法默认是抽象方法。



#### 隐式接口

每个类都隐式的定义了一个接口，接口包含了该类所有的实例成员及其实现的接口。 如果要创建一个 A 类，A 要支持 B 类的 API ，但是不需要继承 B 的实现， 那么可以通过 A 实现 B 的接口。

一个类可以通过 `implements` 关键字来实现一个或者多个接口， 并实现每个接口要求的 API。

#### 类继承与重载

- 类的继承使用`extends`关键字，支持*@override*注解

- 子类可以重写实例方法，getter 和 setter。下面的运算符可以被重载。 例如，想要实现两个向量对象相加，可以重载`+` 方法。

  | `<`  | `+`  | `|`  | `[]`  |
  | ---- | ---- | ---- | ----- |
  | `>`  | `/`  | `^`  | `[]=` |
  | `<=` | `~/` | `&`  | `~`   |
  | `>=` | `*`  | `<<` | `==`  |
  | `–`  | `%`  | `>>` |       |

  重载示例：

  ```dart
  class Vector {
    final int x, y;
  
    Vector(this.x, this.y);
  
    Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
    Vector operator -(Vector v) => Vector(x - v.x, y - v.y);
  
    // ···
  }
  ```

   `!=` 运算符为非可重载运算符。 因为 `e1 != e2` 表达式仅仅是 `!(e1 == e2)` 的语法糖。

  如果要重载 `==` 操作符，需要重写对象的 `hashCode` getter 方法。 重载 `==` 和 `hashCode` 的实例。

#### 类混合（Mixin）

Mixin 是复用类代码的一种途径， 复用的类可以在不同层级，之间可以不存在继承关系。

通过 `with` 后面跟一个或多个混入的名称，来 *使用* Mixin ， 下面的示例演示了两个使用 Mixin 的类：

```dart
class Musician extends Performer with Musical {
  // ···
}

class Maestro extends Person
    with Musical, Aggressive, Demented {
  Maestro(String maestroName) {
    name = maestroName;
    canConduct = true;
  }
}
```

通过创建一个继承自 Object 且没有构造函数的类，来 *实现* 一个 Mixin 。 如果 Mixin 不希望作为常规类被使用，使用关键字 `mixin` 替换 `class` 。 例如：

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

指定只有某些类型可以使用的 Mixin - 比如， Mixin 可以调用 Mixin 自身没有定义的方法 - 使用 `on` 来指定可以使用 Mixin 的父类类型：

```dart
mixin MusicalPerformer on Musician {
  // ···
}
```

**版本提示：** `mixin` 关键字在 Dart 2.1 中被引用支持。 早期版本中的代码通常使用 `abstract class` 代替。 更多有关 Mixin 在 2.1 中的变更信息，请参见 [Dart SDK changelog](https://github.com/dart-lang/sdk/blob/master/CHANGELOG.md) 和 [2.1 mixin specification](https://github.com/dart-lang/language/blob/master/accepted/2.1/super-mixins/feature-specification.md#dart-2-mixin-declarations) 。

**提示：** 对 Mixin 的一些限制正在被移除。 关于更多详情，参考 [proposed mixin specification.](https://github.com/dart-lang/sdk/blob/master/docs/language/informal/mixin-declaration.md)

有关 Dart 中 Mixin 的理论演变，参考 [A Brief History of Mixins in Dart](https://www.dartcn.com/articles/language/mixins).



#### 枚举类型

枚举类型也称为 *enumerations* 或 *enums* ， 是一种特殊的类，用于表示数量固定的常量值。

使用 `enum` 关键字定义一个枚举类型：

```dart
enum Color { red, green, blue }
```

枚举中的每个值都有一个 `index` getter 方法， 该方法返回值所在枚举类型定义中的位置（从 0 开始）。 例如Color.red.index与Color.green.index，第一个枚举值的索引是 0 ， 第二个枚举值的索引是 1；可以使用枚举的 `values` 常量， 获取所有枚举值的List。

可以在 switch语句中使用枚举， 如果不处理所有枚举值，会收到警告

枚举类型具有以下限制：

- 枚举不能被子类化，混合或实现。
- 枚举不能被显式实例化。



#### 泛型

和java类似，只是通配泛型的使用只有`<T extends A>`的用法而没有super的。



### 8.异步支持 

Dart 库中包含许多返回 Future 或 Stream 对象的函数. 这些函数在设置完耗时任务（例如 I/O 曹组）后， 就立即返回了，不会等待耗任务完成。 使用 `async` 和 `await` 关键字实现异步编程。 可以让你像编写同步代码一样实现异步操作。

#### Future

Future类似JavaScript里的promise，有两种使用方式

1. 使用 `async` 和 `await`：首先需要定义一个**异步函数**，即使用 `async` 标记的函数，返回Future，如果函数没有返回有效值， 需要设置其返回类型为 `Future<void>` 

   在异步函数中便可以使用`await`关键字（也只能在异步函数中用）执行同步操作。虽然异步函数可能会执行耗时的操作， 但它不会等待这些操作。 相反，异步函数只有在遇到第一个 await 表达式（详情见）时才会执行。 也就是说，它返回一个 Future 对象， 仅在await表达式完成后才恢复执行。

   在 `await 表达式` 中， `表达式` 的值通常是一个 Future 对象； 如果不是，这是表达式的值会被自动包装成一个 Future 对象。 Future 对象指明返回一个对象的承诺（promise）。 `await 表达式` 执行的结果为这个返回的对象。 await 表达式会阻塞代码的执行，直到需要的对象返回为止。

2. 使用 Future API，具体描述，参考 [库概览](https://www.dartcn.com/guides/libraries/library-tour#future)。

#### Stream

1. 使用 `async` 和 一个 *异步循环* `await for`。`await for`同样需要在异步函数中使用，以下是异步for循环的使用形式：

   ```dart
   await for (varOrType identifier in expression) {
     // Executes each time the stream emits a value.
   }
   ```

   上面 `expression` 返回的值必须是 Stream 类型。 执行流程如下：

   1. 等待，直到流发出一个值。
   2. 执行 for 循环体，将变量设置为该发出的值
   3. 重复1和2，直到关闭流。

   使用` break` 或者 `return` 语句可以停止接收 stream 的数据， 这样就跳出了 for 循环， 并且从 stream 上取消注册。

2. 使用 Stream API, 更多详情，参考 [in the library tour](https://www.dartcn.com/guides/libraries/library-tour#stream)。



### 9.库的使用

Dart语言的常用库包括：

| 包名(dart:xxxx) |                      描述                       |
| :-------------: | :---------------------------------------------: |
|      async      |       异步编程支持，提供Future与Stream类        |
|   collection    |             对dart:core提供集合支持             |
|     convert     | 对不同类型（json、UTF-8）之间提供字符编解码支持 |
|      core       |         Dart的内建类型、对象与核心功能          |
|      html       |                    网页开发                     |
|       io        |                   文件读写I/O                   |
|      math       |               数字常量及数学函数                |
|       svg       |            事件和动画的矢量图像支持             |

`import` 参数只需要一个指向库的 URI。 对于内置库，URI 拥有自己特殊的`dart:` 方案。 对于其他的库，使用系统文件路径或者 `package:` 方案 。 `package:` 方案指定由包管理器（如 pub 工具）提供的库。

支持**库前缀**，如果导入两个存在冲突标识符的库， 则可以为这两个库，或者其中一个指定前缀。

支持**部分导入库**，导入库的一部分。

```dart
import 'dart:core';

import 'package:lib1/lib1.dart';

import 'package:lib2/lib2.dart' as lib2;

// Import only foo.
import 'package:lib1/lib3.dart' show foo;

// Import all names EXCEPT foo.
import 'package:lib2/lib4.dart' hide foo;
```

支持**库的懒加载**：*Deferred loading*可以让应用在需要的时候再加载库。 下面是一些使用延迟加载库的场景：

- 减少 APP 的启动时间。
- 执行 A/B 测试，例如 尝试各种算法的 不同实现。
- 加载很少使用的功能，例如可选的屏幕和对话框。

要延迟加载一个库，需要先使用 `deferred as` 来导入，当需要使用的时候，使用库标识符调用 `loadLibrary()` 函数来加载库；在一个库上你可以多次调用 `loadLibrary()` 函数。但是该库只是载入一次。

- 延迟加载库的常量在导入的时候是不可用的。 只有当库加载完毕的时候，库中常量才可以使用。
- 在导入文件的时候无法使用延迟库中的类型。 如果你需要使用类型，则考虑把接口类型移动到另外一个库中， 让两个库都分别导入这个接口库。
- Dart 隐含的把 `loadLibrary()` 函数导入到使用 `deferred as`的命名空间 中。 `loadLibrary()` 方法返回一个Future。

```dart
import 'package:greetings/hello.dart' deferred as hello;

Future greet() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```



### 10.其他特性

#### 可调用类

通过实现类的 call() 方法， 能够让类像函数一样被调用。

有关把类当做方法使用的更多信息，请参考 [Emulating Functions in Dart](https://www.dartcn.com/articles/language/emulating-functions)



#### Isolates

大多数计算机中，甚至在移动平台上，都在使用多核CPU。 为了有效利用多核性能，开发者一般使用共享内存数据来保证多线程的正确执行。 然而， 多线程共享数据通常会导致很多潜在的问题，并导致代码运行出错。

所有 Dart 代码都在*隔离区*（ isolates ）内运行，而不是线程。 每个隔离区都有自己的内存堆，确保每个隔离区的状态都不会被其他隔离区访问。

有关更多信息，请参考 [dart:isolate library documentation.](https://api.dartlang.org/stable/dart-isolate)



#### 元数据

使用元数据可以提供有关代码的其他信息。 元数据注释以字符 `@` 开头， 后跟对编译时常量 (如 `deprecated`) 的引用或对常量构造函数的调用。对于所有 Dart 代码有两种可用注解：`@deprecated` 和 `@override`。可以自定义元数据注解。

 

#### 生成器

当需要延迟生成( lazily produce )一系列值时， 可以考虑使用_生成器函数_。 Dart 内置支持两种生成器函数：

- **Synchronous** 生成器： 返回一个 Iterable 对象。
- **Asynchronous** 生成器： 返回一个 Stream 对象。

通过在函数体标记 `sync*`， 可以实现一个**同步**生成器函数。 使用 `yield` 语句来传递值：

```dart
Iterable<int> naturalsTo(int n) sync* {
  int k = 0;
  while (k < n) yield k++;
}
```

通过在函数体标记 `async*`， 可以实现一个**异步**生成器函数。 使用 `yield` 语句来传递值：

```dart
Stream<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}
```

如果生成器是递归的，可以使用 `yield*` 来提高其性能：

```dart
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* naturalsDownFrom(n - 1);
  }
}
```

有关生成器的更多信息，请参考文章 [Dart Language Asynchrony Support: Phase 2](https://www.dartcn.com/articles/language/beyond-async) 。



### 11.其他问题

- **面对无法处理的方法调用，OC里有消息转发机制，Dart的对应处理方式?**

  当代码尝试使用不存在的方法或实例变量时， 通过重写 `noSuchMethod()` 方法，来实现检测和应对处理：

  ```dart
  class A {
    // 如果不重写 noSuchMethod，访问
    // 不存在的实例变量时会导致 NoSuchMethodError 错误。
    @override
    void noSuchMethod(Invocation invocation) {
      print('You tried to use a non-existent member: ' +
          '${invocation.memberName}');
    }
  }
  ```

  除非符合下面的任意一项条件， 否则没有实现的方法不能够被调用：

  ​	receiver 具有 `dynamic` 的静态类型 。

  ​	receiver 具有静态类型，用于定义为实现的方法 (可以是抽象的), 并且 receiver 的动态类型具有 `noSuchMethod()` 的实现， 该实现与 `Object` 类中的实现不同。

  有关更多信息，参考 [noSuchMethod forwarding specification.](https://github.com/dart-lang/sdk/blob/master/docs/language/informal/nosuchmethod-forwarding.md)


