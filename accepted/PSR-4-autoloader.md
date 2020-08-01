# 自动加载器

本文档中的 「必须（MUST）」、「不得（MUST NOT）」、「必须（REQUIRED）」、「必须（SHALL）」、
「不得（SHALL NOT）」、「应当（SHOULD）」、「不应（SHOULD NOT）」、「推荐（RECOMMENDED）」、
「可以（MAY）」和「可选（OPTIONAL）」等关键词应遵照照 [RFC 2119] 的说明进行解释。

## 1. 概述

此 PSR 描述了从文件路径 [自动加载][] 类的规范。它完全限定，并可加入到其它自动加载规范中，包括 [PSR-0][]。
此 PSR 还描述了将根据规范自动加载的文件位置。

## 2. 规范

1. 此处的「类」指的是类、接口、trait 和其它类似结构。

2. 一个完全限定类名遵循如下格式：

        \<NamespaceName>(\<SubNamespaceNames>)*\<ClassName>

    1. 完全限定类名**必须**有一个顶级命名空间，也称作「vendor namespace」。

    2. 完全限定类名**可以**有一个或多个子名称空间。

    3. 完全限定类名**必须**包含最终类名。

    4. 完全限定类名中的任意部分中的下划线没有特殊含义。

    5. 完全限定类名中的字母**可以**是任意大小写字母的组合。

    6. 所有类名**必须**以区分大小写的格式引用。

3. 在加载符合完全限定类名的文件时...

    1. 完全限定类名中的一个或多个前导连续的命名空间和子命名空间名称序列（不包括前导命名空间分隔符）对应至少一个「基本目录」。

    2. 「命名空间前缀」后面的连续的子命名空间名称对应「基本目录」中的一个子目录，其中的命名空间分隔符映射为目录分隔符。
    子目录名**必须**与子命名空间名称的大小写匹配。

    3. 最终类名对应以 `.php` 结尾的文件名。文件名**必须**与最终类名大小写匹配。

4. 自动加载实现**不得**抛出异常，**不得**引发任何级别的错误，也**不应**有返回值。

## 3. 示例

下表展示了与给定的完全限定类名、命名空间前缀和基本目录对应的文件路径。

| 完全限定类名    | 命名空间前缀  | 基本目录           | 对应文件路径
| ----------------------------- |--------------------|--------------------------|-------------------------------------------
| \Acme\Log\Writer\File_Writer  | Acme\Log\Writer    | ./acme-log-writer/lib/   | ./acme-log-writer/lib/File_Writer.php
| \Aura\Web\Response\Status     | Aura\Web           | /path/to/aura-web/src/   | /path/to/aura-web/src/Response/Status.php
| \Symfony\Core\Request         | Symfony\Core       | ./vendor/Symfony/Core/   | ./vendor/Symfony/Core/Request.php
| \Zend\Acl                     | Zend               | /usr/includes/Zend/      | /usr/includes/Zend/Acl.php

遵循本规范的自动加载器实现请参见[示例文件][]。示例实现**不得**被视为规范的一部分，并**可以**在任意时间修改。

[自动加载]: http://php.net/autoload
[PSR-0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[示例文件]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader-examples.md
