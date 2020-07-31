自动加载标准
====================

> **弃用** - PSR-0 于 2014-10-21 被标记为禁用。现在推荐用 [PSR-4] 代替。

[PSR-4]: https://www.php-fig.org/psr/psr-4/

以下描述了为实现自动加载器的交互性所必须遵守的强制性要求。

要求
---------

* 一个完全限定的命名空间和类必须有如下结构 `\<Vendor Name>\(<Namespace>\)*<Class Name>`
* 每个命名空间必须有顶级命令空间（「Vendor Name」）。
* 每个命名空间可以有多个子命名空间。
* 当从文件系统载入时，每个命名空间间隔符被转换为 `DIRECTORY_SEPARATOR`。
* *类名*中的每个 `_` 符号被转换为 `DIRECTORY_SEPARATOR`。命名空间中的 `_` 符号没有特殊含义。
* 当从文件系统载入时，完全限定命名空间和类带有 `.php` 扩展名。
* vendor names、命名空间和类的名字可以是任意的大小写字母的组合。

示例
--------

* `\Doctrine\Common\IsolatedClassLoader` => `/path/to/project/lib/vendor/Doctrine/Common/IsolatedClassLoader.php`
* `\Symfony\Core\Request` => `/path/to/project/lib/vendor/Symfony/Core/Request.php`
* `\Zend\Acl` => `/path/to/project/lib/vendor/Zend/Acl.php`
* `\Zend\Mail\Message` => `/path/to/project/lib/vendor/Zend/Mail/Message.php`

命名空间和类名中的下划线转换
-----------------------------------------

* `\namespace\package\Class_Name` => `/path/to/project/lib/vendor/namespace/package/Class/Name.php`
* `\namespace\package_name\Class_Name` => `/path/to/project/lib/vendor/namespace/package_name/Class/Name.php`

此处设置的标准应该是自动加载器交互性的最低要求。可以通过能够加载 PHP 5.3 类的简单 SplClassLoader 实现来测试是否遵循了这些标准。

实现示例
----------------------

下面的示例函数简要地示范了上面描述的标准如何实现自动加载。

~~~php
<?php

function autoload($className)
{
    $className = ltrim($className, '\\');
    $fileName  = '';
    $namespace = '';
    if ($lastNsPos = strrpos($className, '\\')) {
        $namespace = substr($className, 0, $lastNsPos);
        $className = substr($className, $lastNsPos + 1);
        $fileName  = str_replace('\\', DIRECTORY_SEPARATOR, $namespace) . DIRECTORY_SEPARATOR;
    }
    $fileName .= str_replace('_', DIRECTORY_SEPARATOR, $className) . '.php';

    require $fileName;
}
spl_autoload_register('autoload');
~~~

SplClassLoader 实现
-----------------------------

下面是在遵循了上面建议的自动加载交互标准标准的情况下加载你的类的简单的 SplClassLoader 实现的要点。这是目前遵循上述标准以加载 PHP 5.3 类的推荐方法。

* [http://gist.github.com/221634](http://gist.github.com/221634)
