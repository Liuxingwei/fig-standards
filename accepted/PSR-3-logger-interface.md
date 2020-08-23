日志接口
================

本文档描述了日志库的通用接口。

主要目标是允许库接收 `Psr\Log\LoggerInterface` 对象并且用一种简单通用的方法将日志写入该对象。
有定制需求的框架和 CMS **可以**根据自己的需要扩展此接口，但是**应该**与本文档保持兼容。这可以确保应用程序使用的第三方库能够写入到集中应用程序日志。

本文档中的 「必须（MUST）」、「不得（MUST NOT）」、「必须（REQUIRED）」、「必须（SHALL）」、
「不得（SHALL NOT）」、「应当（SHOULD）」、「不应（SHOULD NOT）」、「推荐（RECOMMENDED）」、
「可以（MAY）」和「可选（OPTIONAL）」等关键词应遵照照 [RFC 2119] 的说明进行解释。

本文中的 `实现者` 一词指的是在日志相关库或框架中实现 `LoggerInterface` 接口的人。日志的用户被称为 `用户`。

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 1. 规范

### 1.1 基础

- `LoggerInterface` 对外暴露八个方法，分别写入相对应的八个[RFC 5424][]级别（debug、info、notice、warning、error、critical、alert、emergency）的日志。

- 第九个方法 `log` 接受日志级别作为其第一个参数。带一个日志常量参数调用此方法的结果**必须**和调用指定级别的方法相同。带有一个本规范未定义的级别调用此方法，
  而实现不知道此级别，则**必须**抛出 `Psr\Log\InvalidArgumentException` 异常。如果用户不知道当前实现是否支持自定义级别，就**不应**使用它。

[RFC 5424]: http://tools.ietf.org/html/rfc5424

### 1.2 消息

- 每个方法接收一个字符串或带有 `__toString()` 方法的对象作为消息内容。「实现者」**可以**对传入的对象做特殊处理。如果不是这种情况，实现者**必须**将其转换为字符串。

- 消息**可以**包含占位符，实现都**可以**用上下文数据中的值替换它。

  占位符的名字**必须**与上下文数组的键相吻合。

  占位符的名字**必须**用单层大花括号包裹。在大花括号和占位符之间**不得**有空白。

  占位符的名称**应当**由字母 A-Z`、`a-z`、`0-9`、下划线`_` 和 句点`.` 构成。其它字符的使用保留给以后对占位符规范的修改。

  实现者**可以**利用占位符实现各种转义策略，并转换日志用于显示。用户**不应**预先转义占位符值，因为他们无法知道将在哪个上下文中显示。

  以下是占位符插值的实现示例，仅供参考：

  ~~~php
  <?php

  /**
   * 将上下文的值插入到消息占位符。
   */
  function interpolate($message, array $context = array())
  {
      // 为被括号包裹的上下文键构建替换数组
      $replace = array();
      foreach ($context as $key => $val) {
          // 检测值是否能被转换为字符串
          if (!is_array($val) && (!is_object($val) || method_exists($val, '__toString'))) {
              $replace['{' . $key . '}'] = $val;
          }
      }

      // 将替换值插入消息并返回
      return strtr($message, $replace);
  }

  // 带有占位符的消息
  $message = "User {username} created";

  // 占位符 => 替换值 上下文数组
  $context = array('username' => 'bolivar');

  // 输出“User bolivar created”
  echo interpolate($message, $context);
  ~~~

### 1.3 上下文

- 每个方法接收一个数组作为上下文数据，包含字符串无法表示的额外信息。数组能包含任何东西。
  「实现者」**必须**确保尽可能宽容地对待上下文数据。对上下文中给定的值**不得**抛出异常或引发 PHP 错误、警告和通知。

- 如果在上下文中传递一个 `异常` 对象，必须使用 `'exception'` 键。日志异常是通用模式，在日志后台支持的情况下，允许「实现者」从此异常中提取堆栈追踪。
  由于 `'exception'` 键**可以**包含任何内容。「实现者」在使用它之前**必须**确定其内容确实是 `异常`。

### 1.4 助手类和接口

- 通过继承 `Psr\Log\AbstractLogger` 类并实现通用的 `log` 方法的方式，可以非常容易实现 `LoggerInterface`。
  另外八个方法把消息和上下文转交给它。

- 类似地，使用 `Psr\Log\LoggerTrait` 可以让你只需实现通用的 `log` 方法。需要注意的是，由于 traits 不能实现接口，在使用 `Psr\Log\LoggerTrait` 时必须自己实现 `LoggerInterface`。

- `Psr\Log\NullLogger` 与接口一起提供。如果没有为用户提供日志记录器，接口的用户**可以**用它提供一个退化（fakk-back）的「黑洞（black hole）」实现。
  不过，如果上下文数据创建代价太大，有条件的记录日志会是比较好的备选方案。

- `Psr\Log\LoggerAwareInterface` 只包含 `setLogger(LoggerInterface $logger)` 一个方法，框架可以用它来自动装配带有日志记录器的任意实例。m

- `Psr\Log\LoggerAwareTrait` 用于便利地实现任意类的等价接口。它允许你访问 `$this->logger`。

- `Psr\Log\LogLevel` 类包含表示八个日志类别的常量。

## 2. 包

这些接口和类还描述了相关的异常类和用于检测实现的测试套件，都作为 [psr/log](https://packagist.org/packages/psr/log) 包的一部分被提供。

## 3. `Psr\Log\LoggerInterface`

~~~php
<?php

namespace Psr\Log;

/**
 * 描述日志记录器实例。
 *
 * 消息**必须**是字符串或实现了 __toString() 的对象。
 *
 * 消息**可以**包含形如 {foo} 的占位符，这里的 foo 将被 上下文数据中 「foo」 键的值替换。
 *
 * 上下文数据能够包含任意数据，「实现者」唯一能做的假设是，如果要提供一个用于生成堆栈追踪的异常，它**必须**使用命名为 「exception」 的键。
 *
 * 更详细的信息参见 https://githb.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md。
 */
interface LoggerInterface
{
    /**
     * 系统不可用。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function emergency($message, array $context = array());

    /**
     * 动作必须立即执行。
     *
     * 例如：整个站点瘫痪、数据库崩溃，等等。需要触发 SMS 警告并叫醒你。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function alert($message, array $context = array());

    /**
     * 紧急情况。
     *
     * 例如：应用程序组件不可用，出现不明异常。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function critical($message, array $context = array());

    /**
     * 立即执行动作之外的通常需要记录和监视的运行时错误。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function error($message, array $context = array());

    /**
     * 出现非错误类的异常。
     *
     * 例如：使用了不推荐使用的 API，不当的 API 使用，可以避免的不必要错误。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function warning($message, array $context = array());

    /**
     * 正常但重要的事件。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function notice($message, array $context = array());

    /**
     * 感兴趣的事件。
     *
     * 例如：用户登录、SQL 日志。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function info($message, array $context = array());

    /**
     * 详细的调试信息。
     *
     * @param string $message
     * @param array $context
     * @return void
     */
    public function debug($message, array $context = array());

    /**
     * 任意级别的日志记录。
     *
     * @param mixed $level
     * @param string $message
     * @param array $context
     * @return void
     */
    public function log($level, $message, array $context = array());
}
~~~

## 4. `Psr\Log\LoggerAwareInterface`

~~~php
<?php

namespace Psr\Log;

/**
 * 描述日志记录器感知实例。
 */
interface LoggerAwareInterface
{
    /**
     * 设置日志记录器实例对象。
     *
     * @param LoggerInterface $logger
     * @return void
     */
    public function setLogger(LoggerInterface $logger);
}
~~~

## 5. `Psr\Log\LogLevel`

~~~php
<?php

namespace Psr\Log;

/**
 * 描述日志级别。
 */
class LogLevel
{
    const EMERGENCY = 'emergency';
    const ALERT     = 'alert';
    const CRITICAL  = 'critical';
    const ERROR     = 'error';
    const WARNING   = 'warning';
    const NOTICE    = 'notice';
    const INFO      = 'info';
    const DEBUG     = 'debug';
}
~~~
