# 扩展编码风格指南

本文档中的 「必须（MUST）」、「不得（MUST NOT）」、「必须（REQUIRED）」、「必须（SHALL）」、
「不得（SHALL NOT）」、「应当（SHOULD）」、「不应（SHOULD NOT）」、「推荐（RECOMMENDED）」、
「可以（MAY）」和「可选（OPTIONAL）」等关键词应遵照照 [RFC 2119] 的说明进行解释。

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 概述

本规范继承、扩展和取代 [PSR-2]（编码风格指南），并且要求遵循 [PSR-1]（基本编码标准）。

像 [PSR-2][] 一样，本规范致力于缩小扫描不同作者的代码时的认知分歧。这是通过列举一组如何格式化 PHP
代码的共享规则来实现的。 此 PSR 试图提供一组编码风格工具能实现的、项目能声明遵守的、开发者能方便地在
不同项目间关联的方法。 当不同的作者跨项目合作时，所有项目共用一组指导原则是有帮助的。因此，本指南的好
处不是规则自身，而是共享这些规则。

自2012年 [PSR-2][] 被接受以来， PHP 有了一些影响到编码风格指导原则的改变。但在起草的时候，但在起草
的时候，[PSR-2] 几乎涵盖了 PHP 全部已有功能，新功能阐述很开放。因此，本规范试图在拥有可用新功能的新
背景下阐述 PSR-2,并对 PSR-2 进行订正。

### 上一个语言版本

本文档中所有不为你的项目支持的 PHP 版本的内容，都可以忽略。

### 示例

本例包含了下文规则的快速预览：

~~~php
<?php

declare(strict_types=1);

namespace Vendor\Package;

use Vendor\Package\{ClassA as A, ClassB, ClassC as C};
use Vendor\Package\SomeNamespace\ClassD as D;

use function Vendor\Package\{functionA, functionB, functionC};

use const Vendor\Package\{ConstantA, ConstantB, ConstantC};

class Foo extends Bar implements FooInterface
{
    public function sampleFunction(int $a, int $b = null): array
    {
        if ($a === $b) {
            bar();
        } elseif ($a > $b) {
            $foo->bar($arg1);
        } else {
            BazClass::bar($arg2, $arg3);
        }
    }

    final public static function bar()
    {
        // method body
    }
}
~~~

## 2. 总则

### 2.1 基本编码标准

代码**必须**完全遵守 [PSR-1] 的全部规则。

PSR-1 中术语 'StudlyCaps' **必须** 解析为 PascalCase：包括第一个字母在内的每个单词的首字母大写。

### 2.2 文件

所有 PHP 文件**必须*“ 仅使用 Unix LF（换行符）作为行结束符。

所有 PHP 文件**必须**以使用单个 LF 结束的非空白行结尾。

仅包含 PHP 的文件必须省略 `?>` 标签。

### 2.3 行

行长**不得**有硬界限。

行长的软限制必须是 120 个字符。

行**不应**超过80个字符；过长的行**应当**拆分成多个连续的不超过80个字符的行。

行尾**不得**有尾空白。

**可以**添加空行，以增加可读性，或者标示出（内容自）相关的代码块（明文禁止的情况除外）。

每行**不得**超过一个语句。

### 2.4 缩进

代码中每级缩进**必须**为4个空格，**不得**使用制表符。

### 2.5 关键字和类型

所有 PHP 保留关键字和类型 [[1]][keywords][[2]][types] **必须**使用小写字母。

PHP 未来版本中新增的类型和关键字也**必须**使用小写字母。

类型关键字必须使用短格式，比如用 `bool` 而不使用 `boolean`，使用 `int` 而不使用 `integer` 等等。

## 3. Declare 语句， Namespace 和 Import 语句

PHP 文件头可以由几个不同的块构成。块**必须**用单个空行分隔，每个块**不得**包含空行。
块**必须**按如下顺序排列，不过不需要的块可以省略。

* 起始的 `<?php` 标签。
* 文件级文档块。
* 一个或多个 declare 语句。
* 文件的 Namespace 声明。
* 一个或多个基于类的 `use` 导入语句。
* 一个或名个基于函数的 `use` 导入语句。
* 一个或多个基于常量的 `use` 导入语句。
* 文件中的其余代码。

当文件同时包含 HTML 和 PHP，上面的成分仍然可用。这种情况下，必须将它们放在文件最开头，并且剩余的代码由
PHP 关闭标签和混合的 HTML 和 PHP 构成。

如果 `<?php` 标签在文件的第一行，它**必须**单独占一行，除非这是一个包含 PHP 开关标签之外的标记的文件。

导入语句**必须**从不用前导斜线，且必须总是完全合规。

下面的例子包含了列表中的全部代码块：

~~~php
<?php

/**
 * This file contains an example of coding styles.
 */

declare(strict_types=1);

namespace Vendor\Package;

use Vendor\Package\{ClassA as A, ClassB, ClassC as C};
use Vendor\Package\SomeNamespace\ClassD as D;
use Vendor\Package\AnotherNamespace\ClassE as E;

use function Vendor\Package\{functionA, functionB, functionC};
use function Another\Vendor\functionD;

use const Vendor\Package\{CONSTANT_A, CONSTANT_B, CONSTANT_C};
use const Another\Vendor\CONSTANT_D;

/**
 * FooBar is an example class.
 */
class FooBar
{
    // ... additional PHP code ...
}

~~~

合成命名空间的深度**不得**超过丙层。下面的代码片段是允许的最大合成深度：

~~~php
<?php

use Vendor\Package\SomeNamespace\{
    SubnamespaceOne\ClassA,
    SubnamespaceOne\ClassB,
    SubnamespaceTwo\ClassY,
    ClassZ,
};
~~~

下面的代码示例是不允许的：

~~~php
<?php

use Vendor\Package\SomeNamespace\{
    SubnamespaceOne\AnotherNamespace\ClassA,
    SubnamespaceOne\ClassB,
    ClassZ,
};
~~~

如果想在 PHP 开始和结束标记中包含严格类型声明，则开始标记、严格类型声明和结束标记**必须**在文件的第一行。

例如：

~~~php
<?php declare(strict_types=1) ?>
<html>
<body>
    <?php
        // ... additional PHP code ...
    ?>
</body>
</html>
~~~

Declare 语句**不得**包含空白，且**必须**是 `declare(strict_types=1)` （分号是可选的）。

允许使用 declare 语句块，但必须是如下格式。注意大花括号和留白的位置：

~~~php
declare(ticks=1) {
    // some code
}
~~~

## 4. 类、属性和方法

术语 "类" 包括类、接口和 traits。

注释和语句**不得**与右大花括号在同一行。

实例化一个类时，即使不需要向构造函数传递参数，括号也是**必须**的。

~~~php
new Foo();
~~~

### 4.1 Extends 和 Implements

关键字 `extends` 和 `implements` 声明**必须**和类名在同一行。

类的左大花括号**必须**单独占一行；右大花括号**必须**在代码体的下一行。

左大花括号的前后**不得**有空行。

右大花括号前**不得**有空行。

~~~php
<?php

namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements \ArrayAccess, \Countable
{
    // constants, properties, methods
}
~~~

`implements` 和接口中的 `extends` 列表**可以**拆分为多行，每个后续行有一个缩进。这种情况下，
列表的第一个元素**必须**在下一行，**并且每行一个接口。

~~~php
<?php

namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements
    \ArrayAccess,
    \Countable,
    \Serializable
{
    // constants, properties, methods
}
~~~

### 4.2 使用 traits

类中用于实现 traits 的 `use` 关键字**必须**在左大花括号的下一行声明。

~~~php
<?php

namespace Vendor\Package;

use Vendor\Package\FirstTrait;

class ClassName
{
    use FirstTrait;
}
~~~

导入到类的每个 trait **必须**单独占一行，且**必须**使用独立的 `use` 语句。

~~~php
<?php

namespace Vendor\Package;

use Vendor\Package\FirstTrait;
use Vendor\Package\SecondTrait;
use Vendor\Package\ThirdTrait;

class ClassName
{
    use FirstTrait;
    use SecondTrait;
    use ThirdTrait;
}
~~~

如果一个类的 `use` 导入语句后没有任何内容，类的右大花括号**必须** 在 `use` 语句的下一行。

~~~php
<?php

namespace Vendor\Package;

use Vendor\Package\FirstTrait;

class ClassName
{
    use FirstTrait;
}
~~~

其他情况下， `use` 后面**必须**有一个空行。

~~~php
<?php

namespace Vendor\Package;

use Vendor\Package\FirstTrait;

class ClassName
{
    use FirstTrait;

    private $property;
}
~~~

`insteadof` 和 `as` 必须按下面的方式使用，注意缩进、留白和换行。

~~~php
<?php

class Talker
{
    use A;
    use B {
        A::smallTalk insteadof B;
    }
    use C {
        B::bigTalk insteadof C;
        C::mediumTalk as FooBar;
    }
}
~~~

### 4.3 属性和常量

所有属性**必须**声明可见性。

如果项目的 PHP 最小版本支持常量的可见性（PHP 7.1 及以上版本），所有的常量**必须**声明可见性。 

**不得**使用 `var` 关键字声明属性。

**不得**使用一条语句声明多个属性。

**不得**在属性名中使用单个下划线前缀表示 protected 和 private。也就是说下划线前缀没有明确的意义。

类型声明和属性名之间**必须**有一个空格。

属性声明示例如下：

~~~php
<?php

namespace Vendor\Package;

class ClassName
{
    public $foo = null;
    public static int $bar = 0;
}
~~~

### 4.4 方法和函数

所有方法**必须**声明可见性。

**不得**在方法中使用单个下划线前缀表示 protected 和 private。也就是说下划线前缀没有明确的意义。

声明方法和函数时**不得**在方法名或函数名后加空格。左大花括号**必须**单独占一行，右大花括号必须在其代码体的下一行。参数的左括号后面和右括号前面均**不得**有空格。

方法声明示例如下，注意括号、逗号、留白和花括号的位置。

~~~php
<?php

namespace Vendor\Package;

class ClassName
{
    public function fooBarBaz($arg1, &$arg2, $arg3 = [])
    {
        // method body
    }
}
~~~

函数声明示例如下，注意括号、逗号、留白和花括号的位置。

~~~php
<?php

function fooBarBaz($arg1, &$arg2, $arg3 = [])
{
    // function body
}
~~~

### 4.5 方法和函数的参数

在参数列表中，每个逗号前**不得**有空格，但每个逗号后面**必须**有空格。

带有默认值的方法和函数参数**必须**放在参数列表的最后。

~~~php
<?php

namespace Vendor\Package;

class ClassName
{
    public function foo(int $arg1, &$arg2, $arg3 = [])
    {
        // method body
    }
}
~~~

参数列表**可以**拆分为多行，每个后续行有一个缩进。这种情况下，列表中的第一个**必须**在新行上，并且每行**必须**只有一个参数。

在参数列表拆分成多行的时候，右括号和左大花括号**必须**放在同一行，它们之间有一个空格。

~~~php
<?php

namespace Vendor\Package;

class ClassName
{
    public function aVeryLongMethodName(
        ClassTypeHint $arg1,
        &$arg2,
        array $arg3 = []
    ) {
        // method body
    }
}
~~~

如果有返回类型声明，冒号后面**必须**有一个空格，接着是类型声明。冒号、类型声明与参数列表**必须**在同一行，并且参数列表的右与冒号之间没有空格。

~~~php
<?php

declare(strict_types=1);

namespace Vendor\Package;

class ReturnTypeVariations
{
    public function functionName(int $arg1, $arg2): string
    {
        return 'foo';
    }

    public function anotherFunction(
        string $foo,
        string $bar,
        int $baz
    ): string {
        return 'foo';
    }
}
~~~

在允许为空值的类型声明中，问号与类型声明之间**不得**有空格。

~~~php
<?php

declare(strict_types=1);

namespace Vendor\Package;

class ReturnTypeVariations
{
    public function functionName(?string $arg1, ?int &$arg2): ?string
    {
        return 'foo';
    }
}
~~~

在参数前使用引用操作符 `&` 时，`&` 后**不得**有空格，参见前面的例子。

可变数量的参数定义操作符（...）与参数名之间**不得**有空格。

~~~php
public function process(string $algorithm, ...$parts)
{
    // processing
}
~~~

当同时使用引用操作符和可变数量参数定义操作符时，两个符号之间**不得**有空格。

~~~php
public function process(string $algorithm, &...$parts)
{
    // processing
}
~~~

### 4.6 `abstract`、`final` 和 `static`

如果可见性、 `abstract` 和 `final` 声明同时出现，则可见性声明**必须**在最前面。

如果包含 `static` 声明，它也**必须**在可见性声明之后。

~~~php
<?php

namespace Vendor\Package;

abstract class ClassName
{
    protected static $foo;

    abstract protected function zim();

    final public static function bar()
    {
        // method body
    }
}
~~~

### 4.7 方法和函数调用

当发生方法或函数调用时，在方法或函数名与左括号之间**不得**有空格。左括号之后和右括号之前也**不得**有空格。
在参数列表中，逗号前**不得**有空格，逗号后**必须**有一个空格。

~~~php
<?php

bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
~~~

参数列表**可以**拆分为多行，每个后续行有一个缩进。这种情况下，列表中的第一个**必须**在新行上，并且每行**必须**只有一个参数。
单个参数被拆分为多行（例如带有数组或匿名函数）不属于拆分参数列表自身。

~~~php
<?php

$foo->bar(
    $longArgument,
    $longerArgument,
    $muchLongerArgument
);
~~~

~~~php
<?php

somefunction($foo, $bar, [
  // ...
], $baz);

$app->get('/hello/{name}', function ($name) use ($app) {
    return 'Hello ' . $app->escape($name);
});
~~~

## 5. 控制结构

控制结构的通用风格规则如下：

* 控制结构关键词后面**必须**有一个空格
* 左括号后面**不得**有空格
* 右括号前面**不得**有空格
* 右括号和左大花括号之间**必须**有一个空格
* 代码体**必须**有一缩进
* 代码体**必须**在左大花括号下面另起新行
* 右大花括号**必须**在代码代下面另起新行

控制结构的代码体**必须**用大花括号包住。这会使控制结构的外观标准化，并降低向代码体添加新行时引入错误的可能。

### 5.1 `if`、`elseif`、`else`

`if` 结构如下例。注意括号、留白和大花括号的位置；`else`、`elseif` 与前一段代码体的右大花括号在同一行。

~~~php
<?php

if ($expr1) {
    // if body
} elseif ($expr2) {
    // elseif body
} else {
    // else body;
}
~~~

**应该**使用 `elseif` 代替 `else if`，这样一来所有的控制结构资讯看上去都是单个单词。

括号内的表达式**可以**拆成多行，每个后续行至少要有一个缩进，必须一个条件**必须**另起新行。右括号和左大花括号**必须**在同一行，并且两者之间要有一个空格。条件之间的布尔（逻辑）运算符**必须**总是在行的开头或结尾，不能滥用。

~~~php
<?php

if (
    $expr1
    && $expr2
) {
    // if body
} elseif (
    $expr3
    && $expr4
) {
    // elseif body
}
~~~

### 5.2 `switch`、`case`

`switch` 结构示例如下。注意括号、留白和大花括号的位置。`case` 语句必须基于 `switch` 缩进一次，`break` 关键字（或其它的终止关键词）**必须**与同级的 `case` 体保持相同的缩进。当非空的 `case` 体是 fall-through 模式时，**必须使用类似 `// no break` 的注释。

~~~php
<?php

switch ($expr) {
    case 0:
        echo 'First case, with a break';
        break;
    case 1:
        echo 'Second case, which falls through';
        // no break
    case 2:
    case 3:
    case 4:
        echo 'Third case, return instead of break';
        return;
    default:
        echo 'Default case';
        break;
}
~~~

括号内的表达式**可以**拆成多行，每个后续行至少要有一个缩进，必须一个条件**必须**另起新行。右括号和左大花括号**必须**在同一行，并且两者之间要有一个空格。条件之间的布尔（逻辑）运算符**必须**总是在行的开头或结尾，不能滥用。

~~~php
<?php

switch (
    $expr1
    && $expr2
) {
    // structure body
}
~~~

### 5.3 `while`、`do while`

`while` 结构示例如下。注意括号、留白和大花括号的位置。

~~~php
<?php

while ($expr) {
    // structure body
}
~~~

括号内的表达式**可以**拆成多行，每个后续行至少要有一个缩进，必须一个条件**必须**另起新行。右括号和左大花括号**必须**在同一行，并且两者之间要有一个空格。条件之间的布尔（逻辑）运算符**必须**总是在行的开头或结尾，不能滥用。

~~~php
<?php

while (
    $expr1
    && $expr2
) {
    // structure body
}
~~~

`do while` 语句示例如下。注意括号、留白和大花括号的位置。

~~~php
<?php

do {
    // structure body;
} while ($expr);
~~~

括号内的表达式**可以**拆成多行，每个后续行至少要有一个缩进，必须一个条件**必须**另起新行。右括号和左大花括号**必须**在同一行，并且两者之间要有一个空格。条件之间的布尔（逻辑）运算符**必须**总是在行的开头或结尾，不能滥用。

~~~php
<?php

do {
    // structure body;
} while (
    $expr1
    && $expr2
);
~~~

### 5.4 `for`

`for` 语句示例如下。注意括号、留白和大花括号的位置。

~~~php
<?php

for ($i = 0; $i < 10; $i++) {
    // for body
}
~~~

括号内的表达式**可以**拆成多行，每个后续行至少要有一个缩进，必须一个条件**必须**另起新行。右括号和左大花括号**必须**在同一行，并且两者之间要有一个空格。条件之间的布尔（逻辑）运算符**必须**总是在行的开头或结尾，不能滥用。

~~~php
<?php

for (
    $i = 0;
    $i < 10;
    $i++
) {
    // for body
}
~~~

### 5.5 `foreach`

`foreach` 语句示例如下。注意括号、留白和大花括号的位置。

~~~php
<?php

foreach ($iterable as $key => $value) {
    // foreach body
}
~~~

### 5.6 `try`, `catch`, `finally`

`try-catch-finally` 语句示例如下。注意括号、留白和大花括号的位置。

~~~php
<?php

try {
    // try body
} catch (FirstThrowableType $e) {
    // catch body
} catch (OtherThrowableType | AnotherThrowableType $e) {
    // catch body
} finally {
    // finally body
}
~~~

## 6. 运算符

运算符的风格规则运算量（所采用的操作数的数量）分组。

运算符允许用空格包围，**可以**使用空格来提高可读性。

这里未说明的所有运算符均未定义。

### 6.1. 一元运算符

自增/自减运算符与操作数之间**不得**有空格。

~~~php
$i++;
++$j;
~~~

类型转换运算符与操作数之间**不得**有空格。

~~~php
$intValue = (int) $input;
~~~

### 6.2. 二元运算符

包括[算术][]、[比较][]、[赋值][]、[位][]、
[逻辑][]、[字符串][]和[类型][]在内的所有二元运算符前后都**必须**有至少一个空格：

~~~php
if ($a === $b) {
    $foo = $bar ?? $a ?? $b;
} elseif ($a > $b) {
    $foo = $a + $b * $c;
}
~~~

### 6.3. 三元运算符

条件运算符（也被称为三元运算符）的 `?` 和 `:` 前后**必须**有至少一个空格：

~~~php
$variable = $foo ? 'foo' : 'bar';
~~~

如果条件符中间的操作数被省略，则该运算符**必须**遵循与其它[比较][]运算符相同的规则：

~~~php
$variable = $foo ?: 'bar';
~~~

## 7. 闭包

装饰**必须**用带有一个空格的 `function` 关键字声明，并且 `use` 关键字前后都要有一个空格。

左大花括号**必须**与 `function` 关键字在同一行，右大花括号**必须**在函数体的下一行。

参数列表或变量列表的左括号后和右括号前**不得**有空格。

在参数列表和变量列表中，逗号前**不得**有空格，逗号后**必须有一个空格。

带有默认值的闭包参数**必须**在参数列表的最后。

如果有返回类型声明，**必须**遵循与函数和方法相同的规则；如果有 `use` 关键字，冒号**必须**跟在 `use` 列表的右括号之后，并且不能带空格。

闭包声明的示例如下。注意括号、逗号、留白和大括号的位置：

~~~php
<?php

$closureWithArgs = function ($arg1, $arg2) {
    // body
};

$closureWithArgsAndVars = function ($arg1, $arg2) use ($var1, $var2) {
    // body
};

$closureWithArgsVarsAndReturn = function ($arg1, $arg2) use ($var1, $var2): bool {
    // body
};
~~~

参数列表和变量列表**可以**拆分为多行，每个后续行有一个缩进。这种情况下，列表中的第一个**必须**在新行上，并且每行**必须**只有一个参数。

下面分别是带有和不带有拆分成钓竿的参数列表及变量列表的闭包示例：

~~~php
<?php

$longArgs_noVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) {
   // body
};

$noArgs_longVars = function () use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
   // body
};

$longArgs_longVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
   // body
};

$longArgs_shortVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use ($var1) {
   // body
};

$shortArgs_longVars = function ($arg) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
   // body
};
~~~

这些规则也适用于闭包被直接作为函数或方法调用的参数的情况。

~~~php
<?php

$foo->bar(
    $arg1,
    function ($arg2) use ($var1) {
        // body
    },
    $arg3
);
~~~

## 8. 匿名类

匿名类**必须**遵循与上面小节中闭包相同的方针和原则。

~~~php
<?php

$instance = new class {};
~~~

如果 `implements` 接口列表没有折行，左大花括号就**必须**与 `class` 关键字在同一行。如果接口列表折行，左大花括号则**必须**放在最后一个列表所在的行。

~~~php
<?php

// Brace on the same line
$instance = new class extends \Foo implements \HandleableInterface {
    // Class content
};

// Brace on the next line
$instance = new class extends \Foo implements
    \ArrayAccess,
    \Countable,
    \Serializable
{
    // Class content
};
~~~

[PSR-1]: https://www.php-fig.org/psr/psr-1/
[PSR-2]: https://www.php-fig.org/psr/psr-2/
[keywords]: http://php.net/manual/en/reserved.keywords.php
[types]: http://php.net/manual/en/reserved.other-reserved-words.php
[算术]: http://php.net/manual/en/language.operators.arithmetic.php
[赋值]: http://php.net/manual/en/language.operators.assignment.php
[比较]: http://php.net/manual/en/language.operators.comparison.php
[位]: http://php.net/manual/en/language.operators.bitwise.php
[逻辑]: http://php.net/manual/en/language.operators.logical.php
[字符串]: http://php.net/manual/en/language.operators.string.php
[类型]: http://php.net/manual/en/language.operators.type.php
