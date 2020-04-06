# 编码风格指南

> **弃用** - 2019-08-10 PSR-2 已被标记为弃用。建议使用 [PSR-12] 代替。

[PSR-12]: https://www.php-fig.org/psr/psr-12/

本指南扩展自 [PSR-1]（基本编码规范）。

本指南的目的是减少不同用户浏览代码时的认知分歧。这是通过列举一组有关如何格式化 PHP 代码的
共享规则来实现的。

本文中的风格规则来自于各个成员项目之间的共性。当不同的作者跨多个项目协作，在所有这些项目中使用同一套指导原则
是有帮助的。因此，本指南的好处不在于规则本身，而在于共享这些规则.

本文档中的 「必须（MUST）」、「不得（MUST NOT）」、「必须（REQUIRED）」、「必须（SHALL）」、
「不得（SHALL NOT）」、「应当（SHOULD）」、「不应（SHOULD NOT）」、「推荐（RECOMMENDED）」、
「可以（MAY）」和「可选（OPTIONAL）」等关键词应遵照 [RFC 2119] 的说明进行解释。

[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/liuxingwei/fig-standards/blob/master/accepted/PSR-0.md
[PSR-1]: https://github.com/liuxingwei/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md

## 1. 概述

- 代码**必须**遵循「基本编码标准」[^译者注1]规范 [[PSR-1]]。

- 代码**必须**使用 4 个空格缩进，**不得**使用制表符。

- 行长**不得**有硬边界；软边界**必须**是 120 个字符；每行**应当**少于或等于 80 个字符。

- `namespace` 声明后面**必须**跟一个空行，`use` 声明块的后面也**必须**跟一个空行。

- 类的左大花括号**必须**起始于一个新行，右大花括号**必须**在代码体的下一行。

- 方法的左花大括号**必须**起始于一个新行，右花大括号**必须**在方法体的下一行。

- 所有属性和方法**必须**声明可见性；`abstract` 和 `final` **必须**在可见性之前声明；`static` **必须**
在可见性之后声明。

- 流程控制关键诩后面**必须**有一个空格；方法和函数名后面**不得**有空格。

- 流程控制的左大花括号**必须**和关键词在同一行，右大花括号**必须**在代码体的下一行。

- 流程控制的左大花括号后面**不得**空格，右大花括号的前面也**不得**有空格。

### 1.1. 示例

下面这个例子包含了概述中的一些规则：

~~~php
<?php
namespace Vendor\Package;

use FooInterface;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class Foo extends Bar implements FooInterface
{
    public function sampleMethod($a, $b = null)
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
        // 方法体
    }
}
~~~

## 2. 总则

### 2.1. 基本编码标准

代码**必须**完全遵循 [PSR-1] 中概括的规则。

### 2.2. 文件

所有的 PHP 文件**必须**使用 Unix LF (linefeed) 作为行结束符。

所有的 PHP 文件**必须**用一个单独的空行作为文件的结束。

仅包含 PHP 代码的文件**必须**省略结束的 `?>` 标签。

### 2.3. 行

行长**不得**有硬边界。

行长的软边界**必须**是 120 个字符；自动检查器对软件边界**必须**告警，但**不得**报错。

行**不应**超过 80 个字符；长行**应当**被拆分成多个连续的行，每行不得超过 80 个字符。

非空行的末尾**不得**有尾空白。

**可以**适当的添加空行以提高可读性，同时表明代码块的关联性。

每行**不得**包含多个语句。

### 2.4. 缩进

代码**必须**使用 4 个空格缩进，**不得**使用制表符缩进。


> 注意：仅使用空格，不混用空格和制表符，有助于避免差异、补丁、历史和注释的问题。使用空格
> 还能使得为行内对齐插入细粒度的子缩进变得更容易。

### 2.5. 关键词和 True/False/Null

PHP [关键词] **必须**小写。

 PHP 常量 `true`、`false` 和 `null` 也**必须**小写。

[keywords]: http://php.net/manual/en/reserved.keywords.php

## 3. 命名空间和 Use 声明

`namespace` 声明后面**必须**跟一个空行。

所有 `use` 声明**必须**在 `namespace` 声明之后。

每个 `use` 关键词**必须**单独声明。

`use` 声明块后面**必须**跟一个空行。

示例：

~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

// ... 其它的 PHP 代码 ...

~~~

## 4. 类、属性和方法

术语「类」泛指所有的类、接口和 traits。

### 4.1. Extends 和 Implements

`extends` 和 `implements` 关键词**必须**和类名在同一行声明。

类的左大花括号**必须**单独占一行，右大花括号**必须**在代码体的下一行。

~~~php
<?php
namespace Vendor\Package;

use FooClass;
use BarClass as Bar;
use OtherVendor\OtherPackage\BazClass;

class ClassName extends ParentClass implements \ArrayAccess, \Countable
{
    // 常量、属性、方法
}
~~~

`implements` 列表**可以**拆成多行，后续的每行有一个缩进。这样做的时候，列表的第一个元素
**必须**在新行上，并且每个接口**必须**单独占一行。

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
    // 常量、属性、方法
}
~~~

### 4.2. 属性

每个属性**必须**声明可见性。

**不得**使用 `var` 关键词声明属性。

一个语句**不得**声明多个属性。

属性名**不应**使用单个下划线作前缀来表明属性的 protected 或 private 可见性。

属性声明见下面的例子：

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public $foo = null;
}
~~~

### 4.3. 方法

方法**必须**声明可见性。

方法名**不应**使用单个下划线作前缀来表明属性的 protected 或 private 可见性。

方法声明**不得**在方法名后面加空格。左大花括号**必须**单独占一行，右大花括号**必须**
在方法体的下一行。左大花括号后面**不得**有空格，右大花括号前**不得**有空格。

方法声明见下面的例子。注意括号、逗号、空格和大括号的位置。

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function fooBarBaz($arg1, &$arg2, $arg3 = [])
    {
        // 方法体
    }
}
~~~

### 4.4. 方法参数

参数列表中，逗号前**不得**有空格，逗号后**必须**有一个空格。

带有默认值的方法参数**必须**在参数列表的最后。

~~~php
<?php
namespace Vendor\Package;

class ClassName
{
    public function foo($arg1, &$arg2, $arg3 = [])
    {
        // 方法体
    }
}
~~~

参数列表**可以**拆成多行，后续的每行要有一个缩进。这样做的时候，列表的第一个元素**必须**在
下一行上，每个参数**必须**单独占一行。

如果参数列表拆成多行，右括号**必须**和左大花括号在同一行上，并且用一个空格分隔开。

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
        // 方法体
    }
}
~~~

### 4.5. `abstract`、`final` 和 `static`

`abstract` 和 `final` 声明**必须**在可见性声明之前。

`static` 声明**必须**在可见性声明之后。

~~~php
<?php
namespace Vendor\Package;

abstract class ClassName
{
    protected static $foo;

    abstract protected function zim();

    final public static function bar()
    {
        // 方法体
    }
}
~~~

### 4.6. 方法和函数调用

在调用方法或函数时，在方法或者函数名与左括号之间**不得**有空格，左括号后也**不得**有空格，
右括号前也**不得**有空格。参数列表的逗号前**不得**有空格，逗号后**必须**有一个空格。

~~~php
<?php
bar();
$foo->bar($arg1);
Foo::bar($arg2, $arg3);
~~~

参数列表**可以**拆成多行，后续的每行要有一个缩进。这样做的时候，列表的第一个元素**必须**在
下一行上，每个参数**必须**单独占一行。

~~~php
<?php
$foo->bar(
    $longArgument,
    $longerArgument,
    $muchLongerArgument
);
~~~

## 5. 流程控制

流程控制的总体规则如下：

- 流程控制关键词后面**必须**有一个空格
- 左括号后面**不得**有空格
- 右括号前面**不得**有空格
- 右括号和左大花括号间**必须**有一个空格
- 代码体**必须**有一个缩进
- 右大花括号**必须**在代码体的下一行。

流程的代码体**必须**用大花括号括住。这会使结构外观标准化，并且降低向代码体添加新行时引入错误的
可能性。

### 5.1. `if`、`elseif`、`else`

`if` 结构如下。注意括号、空格和大花括号的位置；`else` 和 `elseif` 与其前面的代码体的右大花
括号同处一行。

~~~php
<?php
if ($expr1) {
    // if 代码体
} elseif ($expr2) {
    // elseif 代码体
} else {
    // else 代码体;
}
~~~

**应当**使用 `elseif` 代替 `else if`，这样一来，流程控制关键词看上去像一个单词。

### 5.2. `switch`、`case`

`switch` 结构如下。注意括号、空格和大花括号的位置。`case` 语句**必须**相对 `switch` 缩进一次，
`break` 关键诩（或者其它终止关键词）**必须**和同级别的 `case` 体保持相同的缩进。一个非空的故
意要 fall-through 的 `case` 体**必须**有类似 `// no break` 的注释。

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

### 5.3. `while`、`do while`

`while` 语句如下。注意括号、空格和大花括号的位置。

~~~php
<?php
while ($expr) {
    // 循环体
}
~~~

与此类似，`do while` 语句如下。注意括号、空格和大花括号的位置。

~~~php
<?php
do {
    // 循环体;
} while ($expr);
~~~

### 5.4. `for`

`for` 语句如下。注意括号、空格和大花括号的位置。

~~~php
<?php
for ($i = 0; $i < 10; $i++) {
    // 循环体
}
~~~

### 5.5. `foreach`

`foreach` 语句如下。注意括号、空格和大花括号的位置。

~~~php
<?php
foreach ($iterable as $key => $value) {
    // foreach 代码体
}
~~~

### 5.6. `try`、`catch`

`try catch` 语句如下。注意括号、空格和大花括号的位置。

~~~php
<?php
try {
    // try 代码体
} catch (FirstExceptionType $e) {
    // catch 代码体
} catch (OtherExceptionType $e) {
    // catch 代码体
}
~~~

## 6. 闭包

装饰**必须**使用后面带有一个空格的 `function` 关键词声明，并且 `use` 关键词前后均要有一
个空格。

左大花括号**必须**与声明在同一行上，右大花括号**必须**在代码体的下一行。

参数和变量列表的左括号后**不得**有空格，右括号前也**不得**有空格。

参数和变量列表的逗号前**不得**有空格，逗号后**必须**有一个空格。

带有默认值的闭包方法参数**必须**在参数列表的最后。

闭包声明如下。注意括号、空格、逗号和大花括号的位置:

~~~php
<?php
$closureWithArgs = function ($arg1, $arg2) {
    // 代码体
};

$closureWithArgsAndVars = function ($arg1, $arg2) use ($var1, $var2) {
    // 代码体
};
~~~

参数列表和变量列表**可以**拆成多行，后续的每行要有一个缩进。这样做的时候，列表的第一个元素**必须**在
下一行上，每个参数或变量**必须**单独占一行。

如果参数列表或变量列表拆成多行时，右括号**必须**和左大花括号在同一行上，并且用一个空格分隔开。

下面是带有和不带有参数列表和變量列表，列表拆分成釣多行的示例：

~~~php
<?php
$longArgs_noVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) {
    // 代碼體
};

$noArgs_longVars = function () use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // 代碼體
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
    // 代碼體
};

$longArgs_shortVars = function (
    $longArgument,
    $longerArgument,
    $muchLongerArgument
) use ($var1) {
    // 代碼體
};

$shortArgs_longVars = function ($arg) use (
    $longVar1,
    $longerVar2,
    $muchLongerVar3
) {
    // 代碼體
};
~~~

当闭包直接用作函数或方法调用的参数时，这些格式化规则也适用。

~~~php
<?php
$foo->bar(
    $arg1,
    function ($arg2) use ($var1) {
        // 代码体
    },
    $arg3
);
~~~

## 7. 结论

本指南有意忽略了一些风格和实践要素。包括但不限于：

- 全局变量和全局常量声明

- 函数声明

- 运算符和赋值

- 行间对齐

- 注释和文档块

- 类名前缀和后缀

- 最佳实践

建议未来**可以**修订和扩充本指南，以处理这些和另外的风格与实践要素。

## 附录 A. 调查

在编写本风格指南时，小组对成员项目进行了调查，以确定常见做法。此项调查留在这儿，可以为后来者所用。

### A.1. 调查数据

    url,http://www.horde.org/apps/horde/docs/CODING_STANDARDS,http://pear.php.net/manual/en/standards.php,http://solarphp.com/manual/appendix-standards.style,http://framework.zend.com/manual/en/coding-standard.html,https://symfony.com/doc/2.0/contributing/code/standards.html,http://www.ppi.io/docs/coding-standards.html,https://github.com/ezsystems/ezp-next/wiki/codingstandards,http://book.cakephp.org/2.0/en/contributing/cakephp-coding-conventions.html,https://github.com/UnionOfRAD/lithium/wiki/Spec%3A-Coding,http://drupal.org/coding-standards,http://code.google.com/p/sabredav/,http://area51.phpbb.com/docs/31x/coding-guidelines.html,https://docs.google.com/a/zikula.org/document/edit?authkey=CPCU0Us&hgd=1&id=1fcqb93Sn-hR9c0mkN6m_tyWnmEvoswKBtSc0tKkZmJA,http://www.chisimba.com,n/a,https://github.com/Respect/project-info/blob/master/coding-standards-sample.php,n/a,Object Calisthenics for PHP,http://doc.nette.org/en/coding-standard,http://flow3.typo3.org,https://github.com/propelorm/Propel2/wiki/Coding-Standards,http://developer.joomla.org/coding-standards.html
    voting,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,no,no,no,?,yes,no,yes
    indent_type,4,4,4,4,4,tab,4,tab,tab,2,4,tab,4,4,4,4,4,4,tab,tab,4,tab
    line_length_limit_soft,75,75,75,75,no,85,120,120,80,80,80,no,100,80,80,?,?,120,80,120,no,150
    line_length_limit_hard,85,85,85,85,no,no,no,no,100,?,no,no,no,100,100,?,120,120,no,no,no,no
    class_names,studly,studly,studly,studly,studly,studly,studly,studly,studly,studly,studly,lower_under,studly,lower,studly,studly,studly,studly,?,studly,studly,studly
    class_brace_line,next,next,next,next,next,same,next,same,same,same,same,next,next,next,next,next,next,next,next,same,next,next
    constant_names,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper,upper
    true_false_null,lower,lower,lower,lower,lower,lower,lower,lower,lower,upper,lower,lower,lower,upper,lower,lower,lower,lower,lower,upper,lower,lower
    method_names,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel,lower_under,camel,camel,camel,camel,camel,camel,camel,camel,camel,camel
    method_brace_line,next,next,next,next,next,same,next,same,same,same,same,next,next,same,next,next,next,next,next,same,next,next
    control_brace_line,same,same,same,same,same,same,next,same,same,same,same,next,same,same,next,same,same,same,same,same,same,next
    control_space_after,yes,yes,yes,yes,yes,no,yes,yes,yes,yes,no,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes,yes
    always_use_control_braces,yes,yes,yes,yes,yes,yes,no,yes,yes,yes,no,yes,yes,yes,yes,no,yes,yes,yes,yes,yes,yes
    else_elseif_line,same,same,same,same,same,same,next,same,same,next,same,next,same,next,next,same,same,same,same,same,same,next
    case_break_indent_from_switch,0/1,0/1,0/1,1/2,1/2,1/2,1/2,1/1,1/1,1/2,1/2,1/1,1/2,1/2,1/2,1/2,1/2,1/2,0/1,1/1,1/2,1/2
    function_space_after,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no,no
    closing_php_tag_required,no,no,no,no,no,no,no,no,yes,no,no,no,no,yes,no,no,no,no,no,yes,no,no
    line_endings,LF,LF,LF,LF,LF,LF,LF,LF,?,LF,?,LF,LF,LF,LF,?,,LF,?,LF,LF,LF
    static_or_visibility_first,static,?,static,either,either,either,visibility,visibility,visibility,either,static,either,?,visibility,?,?,either,either,visibility,visibility,static,?
    control_space_parens,no,no,no,no,no,no,yes,no,no,no,no,no,no,yes,?,no,no,no,no,no,no,no
    blank_line_after_php,no,no,no,no,yes,no,no,no,no,yes,yes,no,no,yes,?,yes,yes,no,yes,no,yes,no
    class_method_control_brace,next/next/same,next/next/same,next/next/same,next/next/same,next/next/same,same/same/same,next/next/next,same/same/same,same/same/same,same/same/same,same/same/same,next/next/next,next/next/same,next/same/same,next/next/next,next/next/same,next/next/same,next/next/same,next/next/same,same/same/same,next/next/same,next/next/next

### A.2. 调查解读

`indent_type`：
缩进风格。 `tab` = "使用一个制表符"， `2` 或 `4` = "空格个数"

`line_length_limit_soft`：
行长的软边界，以字符为单位。 `?` = 无法识别或无响应， `no` 意为无限制。

`line_length_limit_hard`：
行长的硬边界，以字符为单位。 `?` = 无法识别或无响应， `no` 意为无限制。

`class_names`：
类如何命名。 `lower` = 仅有小写字母， `lower_under` = 小写字母加下载划间隔， `studly` = StudlyCase。

`class_brace_line`：
`same` 表示 左大花括号与 class 关键字在同一行，`next` 则表示另起新行。

`constant_names`：
常量如何命名？ `upper` = 大写并带有下划线间隔。

`true_false_null`：
`true`、`false` 和 `null` 关键字是用 `lower` （小写字母），还是全部 `upper` （大写字母）？

`method_names`：
方法如何命名？ `camel` = `camelCase`， `lower_under` = 小写字母并带有下划线间隔。

`method_brace_line`：
`same` 表示左大花括号与 method 在同一行，`next` 表示另起新行。

`control_brace_line`：
`same` 表示左大花括号与流程控制关键字同行，`next` 表示另起新行。

`control_space_after`：
流程控制关键字后面有没有空格？

`always_use_control_braces`：
流程控制总是使用大花括号？

`else_elseif_line`：
使用 `else` 或 `elseif` 的时候， `same` 表示和前面的右花括号同行，`next` 表示另起新行。

`case_break_indent_from_switch`：
相对于 `switch`，`case` 和 `break` 缩进几次？

`function_space_after`：
函数调用时，函数名后面和左括号前面是否有空格？

`closing_php_tag_required`：
仅包含 PHP 的文件，关闭标签 `?>` 是否必须？

`line_endings`：
使用哪种行结束风格？

`static_or_visibility_first`：
声明方法时，`static` 在前面，还是可见性在前面？

`control_space_parens`：
在流程控制表达式中，左括号后和右括号前有没有空格？`yes` = `if ( $expr )`, `no` = `if ($expr)`.

`blank_line_after_php`：
PHP 起始标签后有没有空行？

`class_method_control_brace`：
类、方法、流程控制的左大花括号在哪一行的统计。

### A.3. 调查结果

    indent_type:
        tab: 7
        2: 1
        4: 14
    line_length_limit_soft:
        ?: 2
        no: 3
        75: 4
        80: 6
        85: 1
        100: 1
        120: 4
        150: 1
    line_length_limit_hard:
        ?: 2
        no: 11
        85: 4
        100: 3
        120: 2
    class_names:
        ?: 1
        lower: 1
        lower_under: 1
        studly: 19
    class_brace_line:
        next: 16
        same: 6
    constant_names:
        upper: 22
    true_false_null:
        lower: 19
        upper: 3
    method_names:
        camel: 21
        lower_under: 1
    method_brace_line:
        next: 15
        same: 7
    control_brace_line:
        next: 4
        same: 18
    control_space_after:
        no: 2
        yes: 20
    always_use_control_braces:
        no: 3
        yes: 19
    else_elseif_line:
        next: 6
        same: 16
    case_break_indent_from_switch:
        0/1: 4
        1/1: 4
        1/2: 14
    function_space_after:
        no: 22
    closing_php_tag_required:
        no: 19
        yes: 3
    line_endings:
        ?: 5
        LF: 17
    static_or_visibility_first:
        ?: 5
        either: 7
        static: 4
        visibility: 6
    control_space_parens:
        ?: 1
        no: 19
        yes: 2
    blank_line_after_php:
        ?: 1
        no: 13
        yes: 8
    class_method_control_brace:
        next/next/next: 4
        next/next/same: 11
        next/same/same: 1
        same/same/same: 6


[^译者注1]:此处原文为**编码风格指南（Coding style guide）**，链接指向的是**基本编码标准（Basic coding standard）**
