PSR-4 的示例实现
================================

下面的例子展示了与 PSR-4 相符的代码：

闭包示例
---------------

~~~php
<?php
/**
 * 一个特定项目实现的示例。
 *
 * 先注册这个 SPL 自动加载函数，其后的行将引用此函数尝试从 /path/to/project/src/Baz/Qux.php 加载 \Foo\Bar\Baz\Qux 类。
 *
 *      new \Foo\Bar\Baz\Qux;
 *
 * @param string $class 完全限定类名。
 * @return void
 */
spl_autoload_register(function ($class) {

    // 特定项目的命名空间前缀
    $prefix = 'Foo\\Bar\\';

    // 命名空间前缀对应的基本目录
    $base_dir = __DIR__ . '/src/';

    // 类是否使用命名空间前缀？
    $len = strlen($prefix);
    if (strncmp($prefix, $class, $len) !== 0) {
        // 不，跳转到下一个注册的自动加载器
        return;
    }

    // 得到相对类名
    $relative_class = substr($class, $len);

    // 使用基本目录替换命名空间前缀，使用目录分隔符替换相对类名中的命名空间间隔符并附加 .php
    $file = $base_dir . str_replace('\\', '/', $relative_class) . '.php';

    // 如果文件存在，require 它
    if (file_exists($file)) {
        require $file;
    }
});
~~~

类示例
-------------

下面是处理多个命名空间的类实现示例：

~~~php
<?php
namespace Example;

/**
 * 一个单一命名空间前缀对应多个基本目录的包含可靠功能的常规实现示例。
 *
 * 给定一个在如下文件系统路径中的 foo-bar 类包 ...
 *
 *     /path/to/packages/foo-bar/
 *         src/
 *             Baz.php             # Foo\Bar\Baz
 *             Qux/
 *                 Quux.php        # Foo\Bar\Qux\Quux
 *         tests/
 *             BazTest.php         # Foo\Bar\BazTest
 *             Qux/
 *                 QuuxTest.php    # Foo\Bar\Qux\QuuxTest
 *
 * ... 为下面的 \Foo\Bar\ 命名空间前缀添加类文件路径：
 *
 *      <?php
 *      // 实例化加载器
 *      $loader = new \Example\Psr4AutoloaderClass;
 *
 *      // 注册加载器register the autoloader
 *      $loader->register();
 *
 *      // 为命名空间前缀注册基本目录
 *      $loader->addNamespace('Foo\Bar', '/path/to/packages/foo-bar/src');
 *      $loader->addNamespace('Foo\Bar', '/path/to/packages/foo-bar/tests');
 *
 * 下面这行代码将调用自动加载器尝试从 /path/to/packages/foo-bar/src/Qux/Quux.php 文件加载
 * \Foo\Bar\Qux\Quux 类：
 *
 *      <?php
 *      new \Foo\Bar\Qux\Quux;
 *
 * 下面这行代码将调用自动加载器尝试从 /path/to/packages/foo-bar/tests/Qux/QuuxTest.php 文件加载
 * \Foo\Bar\Qux\QuuxTest 类：
 *
 *      <?php
 *      new \Foo\Bar\Qux\QuuxTest;
 */
class Psr4AutoloaderClass
{
    /**
     * 一个键为命名空间前缀，值为命名被单中的类的基本目录的关联数组
     *
     * @var array
     */
    protected $prefixes = array();

    /**
     *注册带有 SPL 自动加载器栈的加载器。
     *
     * @return void
     */
    public function register()
    {
        spl_autoload_register(array($this, 'loadClass'));
    }

    /**
     * 为命名空间前缀添加基本目录
     *
     * @param string $prefix 命名空间前缀
     * @param string $base_dir 命名空间中的类文件的基本目录
     * @param bool $prepend 如果为真，将基本目录添加到栈顶而不是添加到栈底；结果是它将比其他的基本路径更早被搜索到。
     * @return void
     */
    public function addNamespace($prefix, $base_dir, $prepend = false)
    {
        // 标准化命名空间前缀
        $prefix = trim($prefix, '\\') . '\\';

        // 标准化带有尾部分隔符的基本目录
        $base_dir = rtrim($base_dir, DIRECTORY_SEPARATOR) . '/';

        // 初始化命名空间前缀数组
        if (isset($this->prefixes[$prefix]) === false) {
            $this->prefixes[$prefix] = array();
        }

        // 在数据中保存命名空间对应的基本目录
        if ($prepend) {
            array_unshift($this->prefixes[$prefix], $base_dir);
        } else {
            array_push($this->prefixes[$prefix], $base_dir);
        }
    }

    /**
     * 载入给定类名的类文件
     *
     * @param string $class 完全限定类名。
     * @return mixed 映射成功返回文件名，失败返回逻辑假。
     */
    public function loadClass($class)
    {
        // 当前命名空间前缀
        $prefix = $class;

        // 向后搜索完全限定命名类名的命名空间名称以找到映射文件
        while (false !== $pos = strrpos($prefix, '\\')) {

            // 在prefix变量中保存结尾的命名空间分隔符
            $prefix = substr($class, 0, $pos + 1);

            // 截取剩余的相对类名
            $relative_class = substr($class, $pos + 1);

            // 尝试加载对应的文件
            $mapped_file = $this->loadMappedFile($prefix, $relative_class);
            if ($mapped_file) {
                return $mapped_file;
            }

            // 为 strrpos() 的下一个迭代移去结尾的命名空间间隔符
            $prefix = rtrim($prefix, '\\');
        }

        // 未找到映射文件
        return false;
    }

    /**
     * 加载命名空间前缀和相对类名的映射文件
     *
     * @param string $prefix 命名空间前缀
     * @param string $relative_class 相对类名
     * @return mixed Boolean 如果没能加载映射文件返回假，或者返回载入的映射文件的文件名f
     */
    protected function loadMappedFile($prefix, $relative_class)
    {
        // 这个命名空间前缀有基本目录吗？
        if (isset($this->prefixes[$prefix]) === false) {
            return false;
        }

        // 为命名空间查找基本目录
        foreach ($this->prefixes[$prefix] as $base_dir) {

            // 使用基本目录替换命名空间前缀，使用目录分隔符替换相对类名中的命名空间间隔符并附加 .php
            $file = $base_dir
                  . str_replace('\\', '/', $relative_class)
                  . '.php';

            // 如果映射文件存在，require 它
            if ($this->requireFile($file)) {
                // 嗯，完成了
                return $file;
            }
        }

        // 没有找到
        return false;
    }

    /**
     * 如果文件存在，从文件系统 require 它
     *
     * @param string $file 被 require 的文件
     * @return bool 文件存在返回真，否则返回假
     */
    protected function requireFile($file)
    {
        if (file_exists($file)) {
            require $file;
            return true;
        }
        return false;
    }
}
~~~

### 单元测试

下面的例子是对上面的类加载进行单元测试的一种方法：

~~~php
<?php
namespace Example\Tests;

class MockPsr4AutoloaderClass extends Psr4AutoloaderClass
{
    protected $files = array();

    public function setFiles(array $files)
    {
        $this->files = $files;
    }

    protected function requireFile($file)
    {
        return in_array($file, $this->files);
    }
}

class Psr4AutoloaderClassTest extends \PHPUnit_Framework_TestCase
{
    protected $loader;

    protected function setUp()
    {
        $this->loader = new MockPsr4AutoloaderClass;

        $this->loader->setFiles(array(
            '/vendor/foo.bar/src/ClassName.php',
            '/vendor/foo.bar/src/DoomClassName.php',
            '/vendor/foo.bar/tests/ClassNameTest.php',
            '/vendor/foo.bardoom/src/ClassName.php',
            '/vendor/foo.bar.baz.dib/src/ClassName.php',
            '/vendor/foo.bar.baz.dib.zim.gir/src/ClassName.php',
        ));

        $this->loader->addNamespace(
            'Foo\Bar',
            '/vendor/foo.bar/src'
        );

        $this->loader->addNamespace(
            'Foo\Bar',
            '/vendor/foo.bar/tests'
        );

        $this->loader->addNamespace(
            'Foo\BarDoom',
            '/vendor/foo.bardoom/src'
        );

        $this->loader->addNamespace(
            'Foo\Bar\Baz\Dib',
            '/vendor/foo.bar.baz.dib/src'
        );

        $this->loader->addNamespace(
            'Foo\Bar\Baz\Dib\Zim\Gir',
            '/vendor/foo.bar.baz.dib.zim.gir/src'
        );
    }

    public function testExistingFile()
    {
        $actual = $this->loader->loadClass('Foo\Bar\ClassName');
        $expect = '/vendor/foo.bar/src/ClassName.php';
        $this->assertSame($expect, $actual);

        $actual = $this->loader->loadClass('Foo\Bar\ClassNameTest');
        $expect = '/vendor/foo.bar/tests/ClassNameTest.php';
        $this->assertSame($expect, $actual);
    }

    public function testMissingFile()
    {
        $actual = $this->loader->loadClass('No_Vendor\No_Package\NoClass');
        $this->assertFalse($actual);
    }

    public function testDeepFile()
    {
        $actual = $this->loader->loadClass('Foo\Bar\Baz\Dib\Zim\Gir\ClassName');
        $expect = '/vendor/foo.bar.baz.dib.zim.gir/src/ClassName.php';
        $this->assertSame($expect, $actual);
    }

    public function testConfusion()
    {
        $actual = $this->loader->loadClass('Foo\Bar\DoomClassName');
        $expect = '/vendor/foo.bar/src/DoomClassName.php';
        $this->assertSame($expect, $actual);

        $actual = $this->loader->loadClass('Foo\BarDoom\ClassName');
        $expect = '/vendor/foo.bardoom/src/ClassName.php';
        $this->assertSame($expect, $actual);
    }
}
~~~
