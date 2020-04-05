# 基本编码标准

标准的这一部分包括应被视为确保在共享的 PHP 代码间保持高水平的技术互操作性所需的标准编码元素。

本文档中的 「必须（MUST）」、「不得（MUST NOT）」、「必须（REQUIRED）」、「必须（SHALL）」、
「不得（SHALL NOT）」、「应当（SHOULD）」、「不应（SHOULD NOT）」、「推荐（RECOMMENDED）」、
「可以（MAY）」和「可选（OPTIONAL）」等关键词应遵照照 [RFC 2119] 的说明进行解释。

[RFC 2119]: http://www.ietf.org/rfc/rfc2119.txt
[PSR-0]: https://github.com/liuxingwei/fig-standards/blob/master/accepted/PSR-0.md
[PSR-4]: https://github.com/liuxingwei/fig-standards/blob/master/accepted/PSR-4-autoloader.md

## 1. 概述

- 文件**必须**只使用 `<?php` 和 `<?=` 标签。

- PHP 代码文件**必须**只使用不带有 BOM 头的 UTF-8 格式。

- 文件**应当***只* 用于声明标识符（类、函数、常量等)，*或者*引起副作用（例如生成输出、改变
.ini 设置，等等），但**不应**两件事都做。

- 命名空间和类**必须**遵循「自动加载」规范：[[PSR-0]、[PSR-4]]。

- 类名**必须**使用 `StudlyCaps` 格式予以声明。

- 类常量**必须**使用全大写字母声明，并使用下划线间隔。

- 方法名**必须**使用 `camelCase` 格式予以声明。

## 2. 文件

### 2.1. PHP 标签

PHP 代码**必须**使用长的 `<?php ?>` 标签 或短的 `<?= ?>` 输出标签；**不得**使用其它标签格式。

### 2.2. 字符编码

PHP **必须**仅使用不带有 BOM 头的 UTF-8 编码。

### 2.3. 副作用

文件**应当**用于声明标识符（如类、函数、常量等）并且不引发副作用，或者**应当**执行带有副作用的逻辑，
但**不应**两件事都做。

「副作用」一词是指与类、函数、常量不直接相关的执行逻辑，例如：*仅来自包含文件*。

「副作用」包括但不限于：生成输出，显式使用 `require` 或 `include`，连接外部服务，修改 ini 设置，
引发错误或异常，修改全局或局部变量，读写文件，等等。

下面是一个同时进行声明和引发副作用的例子；
同时也是如何进行规避的示例：

~~~php
<?php
// 副作用：改变 ini 设置
ini_set('error_reporting', E_ALL);

// 副作用：加载文件
include "file.php";

// 副作用：生成输出
echo "<html>\n";

// 声明
function foo()
{
    // 函数体
}
~~~

下面是一个仅包含声明，不带有副作用的文件示例；
它也是一个可仿效的例子：

~~~php
<?php
// 声明
function foo()
{
    // 函数体
}

// 条件声明，*不是*副作用
if (! function_exists('bar')) {
    function bar()
    {
        // 函数体
    }
}
~~~

## 3. 命名空间和类名

命名空间和类**必须**遵循「自动加载」规范： [[PSR-0]、[PSR-4]]。

就是说每个类都要放在单独的文件中，并且位于至少一级命名空间（顶级供应商名称）中。

类名**必须**使用 `StudlyCaps` 格式予以声明。

使用 PHP 5.3 及更新版本写就的代码必须使用正规的命名空间。

例如：

~~~php
<?php
// PHP 5.3 及更新版本
namespace Vendor\Model;

class Foo
{
}
~~~

使用 5.2.x 及更旧的版本写就的代码**应当**在类名中使用带有 `Vendor_` 前缀的伪命名空间规约：

~~~php
<?php
// PHP 5.2.x 及更旧版本
class Vendor_Model_Foo
{
}
~~~

## 4. 类常量、属性和方法

术语「类」指所有的类、接口和    traits。

### 4.1. 常量

类常量**必须**全部使用大写字母命名，并使用下划线作为间隔符。
例如：

~~~php
<?php
namespace Vendor\Model;

class Foo
{
    const VERSION = '1.0';
    const DATE_APPROVED = '2012-06-01';
}
~~~

### 4.2. 属性

本指南有意避免推荐有关使用 `$StudlyCaps`、`$camelCase` 或 `$under_score` 属性命名的建议。

无论哪种命名规约都**应当**在合理的范围内使用。其范围可能是 供应商级别、包级别、类级别或者方法级别。

### 4.3. 方法

方法名必须使用 `camelCase()` 格式予以声明。
