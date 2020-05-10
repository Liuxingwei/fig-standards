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


When you have a return type declaration present, there MUST be one space after
the colon followed by the type declaration. The colon and declaration MUST be
on the same line as the argument list closing parenthesis with no spaces between
the two characters.

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

In nullable type declarations, there MUST NOT be a space between the question mark
and the type.

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

When using the reference operator `&` before an argument, there MUST NOT be
a space after it, like in the previous example.

There MUST NOT be a space between the variadic three dot operator and the argument
name:

```php
public function process(string $algorithm, ...$parts)
{
    // processing
}
```

When combining both the reference operator and the variadic three dot operator,
there MUST NOT be any space between the two of them:

```php
public function process(string $algorithm, &...$parts)
{
    // processing
}
```

### 4.6 `abstract`, `final`, and `static`

When present, the `abstract` and `final` declarations MUST precede the
visibility declaration.

When present, the `static` declaration MUST come after the visibility
declaration.

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

### 4.7 Method and Function Calls

When making a method or function call, there MUST NOT be a space between the
method or function name and the opening parenthesis, there MUST NOT be a space
after the opening parenthesis, and there MUST NOT be a space before the
closing parenthesis. In the argument list, there MUST NOT be a space before
each comma, and there MUST be one space after each comma.

~~~php
<?php

bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
~~~

Argument lists MAY be split across multiple lines, where each subsequent line
is indented once. When doing so, the first item in the list MUST be on the
next line, and there MUST be only one argument per line. A single argument being
split across multiple lines (as might be the case with an anonymous function or
array) does not constitute splitting the argument list itself.

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

## 5. Control Structures

The general style rules for control structures are as follows:

- There MUST be one space after the control structure keyword
- There MUST NOT be a space after the opening parenthesis
- There MUST NOT be a space before the closing parenthesis
- There MUST be one space between the closing parenthesis and the opening
  brace
- The structure body MUST be indented once
- The body MUST be on the next line after the opening brace
- The closing brace MUST be on the next line after the body

The body of each structure MUST be enclosed by braces. This standardizes how
the structures look and reduces the likelihood of introducing errors as new
lines get added to the body.

### 5.1 `if`, `elseif`, `else`

An `if` structure looks like the following. Note the placement of parentheses,
spaces, and braces; and that `else` and `elseif` are on the same line as the
closing brace from the earlier body.

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

The keyword `elseif` SHOULD be used instead of `else if` so that all control
keywords look like single words.

Expressions in parentheses MAY be split across multiple lines, where each
subsequent line is indented at least once. When doing so, the first condition
MUST be on the next line. The closing parenthesis and opening brace MUST be
placed together on their own line with one space between them. Boolean
operators between conditions MUST always be at the beginning or at the end of
the line, not a mix of both.

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

### 5.2 `switch`, `case`

A `switch` structure looks like the following. Note the placement of
parentheses, spaces, and braces. The `case` statement MUST be indented once
from `switch`, and the `break` keyword (or other terminating keywords) MUST be
indented at the same level as the `case` body. There MUST be a comment such as
`// no break` when fall-through is intentional in a non-empty `case` body.

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

Expressions in parentheses MAY be split across multiple lines, where each
subsequent line is indented at least once. When doing so, the first condition
MUST be on the next line. The closing parenthesis and opening brace MUST be
placed together on their own line with one space between them. Boolean
operators between conditions MUST always be at the beginning or at the end of
the line, not a mix of both.

~~~php
<?php

switch (
    $expr1
    && $expr2
) {
    // structure body
}
~~~

### 5.3 `while`, `do while`

A `while` statement looks like the following. Note the placement of
parentheses, spaces, and braces.

~~~php
<?php

while ($expr) {
    // structure body
}
~~~

Expressions in parentheses MAY be split across multiple lines, where each
subsequent line is indented at least once. When doing so, the first condition
MUST be on the next line. The closing parenthesis and opening brace MUST be
placed together on their own line with one space between them. Boolean
operators between conditions MUST always be at the beginning or at the end of
the line, not a mix of both.

~~~php
<?php

while (
    $expr1
    && $expr2
) {
    // structure body
}
~~~

Similarly, a `do while` statement looks like the following. Note the placement
of parentheses, spaces, and braces.

~~~php
<?php

do {
    // structure body;
} while ($expr);
~~~

Expressions in parentheses MAY be split across multiple lines, where each
subsequent line is indented at least once. When doing so, the first condition
MUST be on the next line. Boolean operators between conditions MUST
always be at the beginning or at the end of the line, not a mix of both.

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

A `for` statement looks like the following. Note the placement of parentheses,
spaces, and braces.

~~~php
<?php

for ($i = 0; $i < 10; $i++) {
    // for body
}
~~~

Expressions in parentheses MAY be split across multiple lines, where each
subsequent line is indented at least once. When doing so, the first expression
MUST be on the next line. The closing parenthesis and opening brace MUST be
placed together on their own line with one space between them.

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

A `foreach` statement looks like the following. Note the placement of
parentheses, spaces, and braces.

~~~php
<?php

foreach ($iterable as $key => $value) {
    // foreach body
}
~~~

### 5.6 `try`, `catch`, `finally`

A `try-catch-finally` block looks like the following. Note the placement of
parentheses, spaces, and braces.

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

## 6. Operators

Style rules for operators are grouped by arity (the number of operands they take).

When space is permitted around an operator, multiple spaces MAY be
used for readability purposes.

All operators not described here are left undefined.

### 6.1. Unary operators

The increment/decrement operators MUST NOT have any space between
the operator and operand.
~~~php
$i++;
++$j;
~~~

Type casting operators MUST NOT have any space within the parentheses:
~~~php
$intValue = (int) $input;
~~~

### 6.2. Binary operators

All binary [arithmetic][], [comparison][], [assignment][], [bitwise][],
[logical][], [string][], and [type][] operators MUST be preceded and
followed by at least one space:
~~~php
if ($a === $b) {
    $foo = $bar ?? $a ?? $b;
} elseif ($a > $b) {
    $foo = $a + $b * $c;
}
~~~

### 6.3. Ternary operators

The conditional operator, also known simply as the ternary operator, MUST be
preceded and followed by at least one space around both the `?`
and `:` characters:
~~~php
$variable = $foo ? 'foo' : 'bar';
~~~

When the middle operand of the conditional operator is omitted, the operator
MUST follow the same style rules as other binary [comparison][] operators:
~~~php
$variable = $foo ?: 'bar';
~~~

## 7. Closures

Closures MUST be declared with a space after the `function` keyword, and a
space before and after the `use` keyword.

The opening brace MUST go on the same line, and the closing brace MUST go on
the next line following the body.

There MUST NOT be a space after the opening parenthesis of the argument list
or variable list, and there MUST NOT be a space before the closing parenthesis
of the argument list or variable list.

In the argument list and variable list, there MUST NOT be a space before each
comma, and there MUST be one space after each comma.

Closure arguments with default values MUST go at the end of the argument
list.

If a return type is present, it MUST follow the same rules as with normal
functions and methods; if the `use` keyword is present, the colon MUST follow
the `use` list closing parentheses with no spaces between the two characters.

A closure declaration looks like the following. Note the placement of
parentheses, commas, spaces, and braces:

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

Argument lists and variable lists MAY be split across multiple lines, where
each subsequent line is indented once. When doing so, the first item in the
list MUST be on the next line, and there MUST be only one argument or variable
per line.

When the ending list (whether of arguments or variables) is split across
multiple lines, the closing parenthesis and opening brace MUST be placed
together on their own line with one space between them.

The following are examples of closures with and without argument lists and
variable lists split across multiple lines.

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

Note that the formatting rules also apply when the closure is used directly
in a function or method call as an argument.

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

## 8. Anonymous Classes

Anonymous Classes MUST follow the same guidelines and principles as closures
in the above section.

~~~php
<?php

$instance = new class {};
~~~

The opening brace MAY be on the same line as the `class` keyword so long as
the list of `implements` interfaces does not wrap. If the list of interfaces
wraps, the brace MUST be placed on the line immediately following the last
interface.

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
[arithmetic]: http://php.net/manual/en/language.operators.arithmetic.php
[assignment]: http://php.net/manual/en/language.operators.assignment.php
[comparison]: http://php.net/manual/en/language.operators.comparison.php
[bitwise]: http://php.net/manual/en/language.operators.bitwise.php
[logical]: http://php.net/manual/en/language.operators.logical.php
[string]: http://php.net/manual/en/language.operators.string.php
[type]: http://php.net/manual/en/language.operators.type.php
