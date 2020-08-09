# PSR-4 元文档

## 1. 概述

本文的目的是说明将命名空间映射到文件系统路径，并与其它 SPL 注册的自动装载器共存的可协作的 PHP 自动装载器规则。它是对 PSR-0 的补充，而不是要取代之。

## 2. 问题何在

### PSR-0 历史

PSR-0 类命名和自裁加载标准源于在 PHP 5.2 及以前版本限制下被广泛接受的 Horde/PEAR 约定。该标准倾向于将所有的类源文件放入单个主目录中，在类名中使用下划线表示伪命名空间，就像这样：

    /path/to/src/
        VendorFoo/
            Bar/
                Baz.php     # VendorFoo_Bar_Baz
        VendorDib/
            Zim/
                Gir.php     # Vendor_Dib_Zim_Gir

随着 PHP 5.3 发布和命名空间正式可用，PSR-0 被引入以同时允许旧的 Horde/PEAR 下划线模式*与*使用新的命名空间符号。仍然允许在类名中使用下划线以便于从旧的命名空间命名向新的命名转换，进而鼓励命名空间的广泛采用。

    /path/to/src/
        VendorFoo/
            Bar/
                Baz.php     # VendorFoo_Bar_Baz
        VendorDib/
            Zim/
                Gir.php     # VendorDib_Zim_Gir
        Irk_Operation/
            Impending_Doom/
                V1.php
                V2.php      # Irk_Operation\Impending_Doom\V2

PEAR 安装器将源文件从 PEAR 包移动到单个中心目录中这一事实，充分解释了这一结构。

### Composer 来了

使用 Composer 时，源码包不再被复制到单一全局位置。它们在安装位置使用，不再到处移动。这意味着 Composer 没有你 PEAR 那样的 PHP 源“单一主目录”。取而代之的是多个目录，每个项目的包放在分隔的目录中。

为满足 PSR-0 的要求，Composer 包就得长成这样：

    vendor/
        vendor_name/
            package_name/
                src/
                    Vendor_Name/
                        Package_Name/
                            ClassName.php       # Vendor_Name\Package_Name\ClassName
                tests/
                    Vendor_Name/
                        Package_Name/
                            ClassNameTest.php   # Vendor_Name\Package_Name\ClassNameTest

"src" 和 "tests" 目录不得不包含 vendor 和包目录名。这是屈从 PSR-0 的结果。

这个结构有许多远超所需的深度和重复。此建议推荐使用附加或替代 PSR 以使我们能援用如下格式的包：

    vendor/
        vendor_name/
            package_name/
                src/
                    ClassName.php       # Vendor_Name\Package_Name\ClassName
                tests/
                    ClassNameTest.php   # Vendor_Name\Package_Name\ClassNameTest

这将包含一个最初被称为“面向包的自动加载”（与传统的“直接的类到包的自动加载相对）的实现。

### 面向包的自动加载

借助扩展或修正 PSR-0 来实现面向包的自动加载很难，因为 PSR-0 不允许在类名的任何部分之间调制路径。这意味着面向包的自动加载器比 PSR-0 更复杂。而且它还要允许干净的包。

起初推荐的是如下规则：

1. 该实现**必须**使用至少两级命名空间：供应商名称和该工作方法商的包名。（这个顶级双名组合，在后面被称为供应商-包信或供应商-包命名空间。）

2. 该实现**必须**允许在供应商-包命名空间和余下的完整例规的类名之间插入路径。

3. 供应商-包命名空间**可以**映射到任意目录。完整合规的的剩余部分**必须**映射到命名空间的同名目录，并且类名**必须**映射到以 .php 结尾的同名文件。

注意，这意味着不再在类名中使用下划线作为目录间隔符。人们可能会认为下划线应该可以像它们在 PSR-0 下那样使用，但是它们在文档中被指为从 PHP 5.2 或更早版本的伪命名空间的过渡，因此移除这种用法也是可以接受的。

## 3. 范围

### 3.1 目标

- 保持 PSR-0 规则，该实现**必须**使用最少丙级命名空间：供应商名称、供应商包名。

- 允许在供应商-包命名空间和剩余的完整例规的类名之间插入路径。

- 供应商-包命名空间**可以**映射到任意目录，可能是多个目录。

- 类名中的下划线不再作为目录分隔符。

### 3.2 目标之外[^译者注]

- 为非类资源提供通用转换算法

## 4. 方案

### 4.1 既定方案

这种方法包含 PSR-0 的关键特性，除了其包含的较深的目录结构。此外，它还指定了一些附加规则，这些规则使得实现更具可操作性。

如果没能关联到目录映射，最终方案还指明了自动加载器应如何处理错误。需要注意，禁止抛出异常或引发错误。这个条件有两重意思。

1. PHP 自动加载器被设计成可叠加的，如果一个加载器没能载入一个类，其它的加载器还有机会完成这一动作。允许自动加载器触发中断错误条件会破坏协作性。

2. `class_exists()` 和 `interface_exists()` 函数允许”找不到，但之后仍尝试加载“作为合法的、常规的用例。 抛出异常的加载器会使 `class_exists()` 不可用，对协作而言，这是不可接受的。
希望在找不到类的情况下提供附加的 debug 信息的加载器可以用记录日志来实现，与 PSR-3 兼容的或其他的日志工具。

优点：

- 较浅的目录结构

- 更灵活的文件位置

- 不再将类名中的下划线用作目录分隔符

- 使得实现更具可操作性

缺点：

- 与 PSR-0 类似，仅通过检查类名来确定其在文件系统中的位置并不总是有效的（类至文件的转换继承自 Horde/PEAR）。

### 4.2 备选方案：仅使用 PSR-0

仅使用 PSR-0 虽然合理，却会给我们带来更深的目录结构。

优点：

- 不需要改变任何人的习惯或实现

缺点：

- 带来更深的目录结构

- 类名中的下划线用作目录分隔符

### 4.3 备选方案：将自动加载和转换拆分开来

Beau Simensen 等人建议将转换算法从自裁加载建议中拆分出来，以使转换规则可以为其他建议所用。拆分了他们之后的调查和讨论显示，组合版本（即将转换规则嵌入在自动加载建议中）更受欢迎。

优点：

- 转换规则可被其他建议单独引用。

缺点：

- 不符合调查对象和部分合作者的意愿。

### 4.4 备选方案：使用更具命令性和叙述性的语言

一位发起者在听取了多位 +1 投票人关于他们支持这一想法但不同意（或不理解）提案的错词后，撤回了第二轮投票，之后有段时间，提案被使用更富有叙述性和更具命令性的语言扩展。
这种方法遭到了少数参与者的强烈谴责。一段时间之后，Beau Simensen 开始了一项基于 PSR-0 的实验性的改进；编辑和提案人赞同这种更简洁的方案，并指导了正在审议中的由 Paul M. 执笔、多人贡献的版本。

### PHP 5.3.2 及以下版本的兼容性说明

PHP 5.3.2 以前的版本不会去掉前导的命名空间分隔符，留意这一点的责任落在实现上。未能去除前导命名空间分隔符可能导致非预期的行为。

## 5. 人员

### 5.1 编辑

- Paul M. Jones, Solar/Aura

### 5.2 发起人

- Phil Sturgeon, PyroCMS (Coordinator)
- Larry Garfield, Drupal

### 5.3 投稿人

- Andreas Hennings
- Bernhard Schussek
- Beau Simensen
- Donald Gilbert
- Mike van Riel
- Paul Dragoonis
- Too many others to name and count

## 6. 投票

- **进入投票：** <https://groups.google.com/d/msg/php-fig/_LYBgfcEoFE/ZwFTvVTIl4AJ>

- **赞成投票+**

  - 首次尝试： <https://groups.google.com/forum/#!topic/php-fig/Ua46E344_Ls>，
    在新工作流之前提交；由于意外修改建议而中止

  - 第二次尝试: <https://groups.google.com/forum/#!topic/php-fig/NWfyAeF7Psk>,
    发起人自行决定取消 <https://groups.google.com/forum/#!topic/php-fig/t4mW2TQF7iE>

  - 第三次尝试： 待定

## 7. 相关链接

- [Autoloader, round 4](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/lpmJcmkNYjM)
- [POLL: Autoloader: Split or Combined?](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/fGwA6XHlYhI)
- [PSR-X autoloader spec: Loopholes, ambiguities](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/kUbzJAbHxmg)
- [Autoloader: Combine Proposals?](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/422dFBGs1Yc)
- [Package-Oriented Autoloader, Round 2](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/Y4xc71Q3YEQ)
- [Autoloader: looking again at namespace](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/bnoiTxE8L28)
- [DISCUSSION: Package-Oriented Autoloader - vote against](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/SJTL1ec46II)
- [VOTE: Package-Oriented Autoloader](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/Ua46E344_Ls)
- [Proposal: Package-Oriented Autoloader](https://groups.google.com/forum/#!topicsearchin/php-fig/autoload/php-fig/qT7mEy0RIuI)
- [Towards a Package Oriented Autoloader](https://groups.google.com/forum/#!searchin/php-fig/package$20oriented$20autoloader/php-fig/JdR-g8ZxKa8/jJr80ard-ekJ)
- [List of Alternative PSR-4 Proposals](https://groups.google.com/forum/#!topic/php-fig/oXr-2TU1lQY)
- [Summary of [post-Acceptance Vote pull] PSR-4 discussions](https://groups.google.com/forum/#!searchin/php-fig/psr-4$20summary/php-fig/bSTwUX58NhE/YPcFgBjwvpEJ)

[译者注]: 原文为 Non-Goals，意为「非目标」，即下面这段内容不是本标准的目标。
