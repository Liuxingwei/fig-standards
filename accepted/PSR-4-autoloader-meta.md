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

### 3.2 目标之外

- 为非类资源提供通用转换算法

## 4. 方案

### 4.1 优选方案

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

### 4.4 备选方案：Alternative: Use More Imperative And Narrative Language

After the second vote was pulled by a Sponsor after hearing from multiple +1
voters that they supported the idea but did not agree with (or understand) the
wording of the proposal, there was a period during which the voted-on proposal
was expanded with greater narrative and somewhat more imperative language. This
approach was decried by a vocal minority of participants. After some time, Beau
Simensen started an experimental revision with an eye to PSR-0; the Editor and
Sponsors favored this more terse approach and shepherded the version now under
consideration, written by Paul M. Jones and contributed to by many.

### Compatibility Note with PHP 5.3.2 and below

PHP versions before 5.3.3 do not strip the leading namespace separator, so
the responsibility to look out for this falls on the implementation. Failing
to strip the leading namespace separator could lead to unexpected behavior.

## 5. People

### 5.1 Editor

- Paul M. Jones, Solar/Aura

### 5.2 Sponsors

- Phil Sturgeon, PyroCMS (Coordinator)
- Larry Garfield, Drupal

### 5.3 Contributors

- Andreas Hennings
- Bernhard Schussek
- Beau Simensen
- Donald Gilbert
- Mike van Riel
- Paul Dragoonis
- Too many others to name and count

## 6. Votes

- **Entrance Vote:** <https://groups.google.com/d/msg/php-fig/_LYBgfcEoFE/ZwFTvVTIl4AJ>

- **Acceptance Vote:**

    - 1st attempt: <https://groups.google.com/forum/#!topic/php-fig/Ua46E344_Ls>,
      presented prior to new workflow; aborted due to accidental proposal modification

    - 2nd attempt: <https://groups.google.com/forum/#!topic/php-fig/NWfyAeF7Psk>,
      cancelled at the discretion of the sponsor <https://groups.google.com/forum/#!topic/php-fig/t4mW2TQF7iE>

    - 3rd attempt: TBD

## 7. Relevant Links

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
