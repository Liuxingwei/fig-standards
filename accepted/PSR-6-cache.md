# 缓存接口

缓存是提高项目性能的通用方法，建立骑在缓存库是许多框架和库的常见特性之一。这就导致了一种情况：许多库使用不同级别的功能迭代自己的缓存库。
这些差异导致开发者不得不学习多个具有或不具有他们所需功能的系统。另外，缓存库的开发者也要面对在仅支持部分框架或创建一个大量适配器类之间做选择的难题。

缓存系统的通用接口能解决上述问题。当缓存库的开发者仅仅实现单一的一组接口，而不是完全不同的适配器时，库和框架开发者就能够指望缓存系统以他们期望的方式工作。

本文档中的 「必须（MUST）」、「不得（MUST NOT）」、「必须（REQUIRED）」、「必须（SHALL）」、
「不得（SHALL NOT）」、「应当（SHOULD）」、「不应（SHOULD NOT）」、「推荐（RECOMMENDED）」、
「可以（MAY）」和「可选（OPTIONAL）」等关键词应遵照照 [RFC 2119] 的说明进行解释。

[RFC 2119]: http://tools.ietf.org/html/rfc2119

## 目标

本 PSR 的目标是允许开发者创建无需定制开发就能集成到现有框架和系统中缓存感知库。

## 定义

* **调用库** - 需要缓存服务的库或代码。「调用库」利用实现了标准接口的缓存服务，但无需知道这些缓存服务的实现技术。

* **实现库** - 负责实现本标准、用于为「调用库」提供缓存服务的库。「实现库」**必须**提供实现了 Cache\CacheItemPoolInterface 和 Cache\CacheItemInterface 接口的类。
「实现库」最低**必须**支持下文描述的秒级精确度的 TTL 功能。

* **TTL** - 元素的生存时间（TTL）是从元素存储到过期的总时长。TTL 通常用整数秒或 DateInterval 对象定义。

* **过期** - 项目的过期时间。其值为 TTL 加上对象的存储时间，也可能是明确的 DateTime 对象设置。一个 1:30:00 存储的带有 300 秒 TTL 的元素将在 1:35:00 过期。
「实现库」**可以**在元素请求的过期时间之前让它过期，但是在它到期时**必须**使其过期。如果调用库请求保存一个元素但没有指定过期时间，或者指定空的过期时间或 TTL，
「实现库」**可以**使用默认配置持续时间。如果没有设定默认持续时间，「实现库」**必须**视请求为永久缓存，或者在底层实现支持的时间内缓存该请求。

* **键** - 由至少一个字母构成的唯一标识一个缓存元素的字符串。「实现库」**必须**支持由字母 `A-Z`、`a-z`、`0-9`、`_` 和 `.` 以及任何 UFT-8 编码的字符构成的长度在 64 个字符以内的「键」。
「实现库」**可以**支持附加的字符和编码，或者更长的长度，但对上述规则的支持是必须的。库负责根据需要转义键字符串，但**必须**能够返回原始的未修改的键。
「实现库」**不得**支持诸如 `{}()/\@:` 这此为未来扩展保留的字符。

* **命中** - 缓存命中发生在如下情形：「调用库」通过键请求了一个元素，并且了与这个键匹配的值，该值没有过期且不符合其它使其无效的条件。「调用库」**应该**确保在所有的 get() 调用上验证 isHit()。

* **未命中** - 与缓存命中相对的是缓存未命中。缓存未命中发生在如下情形：「调用库」通过键请求了一个元素，但元素未找，或者值已过期，又或者值符合某个无效条件。过期值**必须**总被视缓存未命中。

* **延迟** - 延迟缓存保存指的是缓存池不能立即持久化缓存元素。「池」对象**可以**延时持久化延迟缓存元素以获得一些存储引擎的批量设置操作支持。
「缓存池」**必须**确保延迟缓存元素最终被持久化且数据不丢失，还**可以**在「调用库」请求缓存之前进行缓存。在「调用库」詷用 commit() 方法时所有未完成的延迟元素**必须**被缓存。
「实现库」**可以**使用任意它认为适当的逻辑持久化缓存元素，例如对象析构方法，调用 save() 方法时持久化，最大过期时间检测，或者其他适用逻辑。针对已经被延迟的缓存元素的请求**必须**返回已延迟但尚未持久化的元素。

## 数据

「实现库」**必须**支持所有的可序列化 PHP 元素类型，包括：

* **字符串** - PHP 兼容编码的任意长度字符串。
* **整数** - PHP 支持的任意长度的整数（上限为 64 位有符号整数）。
* **浮点数** - 所有有符号浮点数。
* **布尔值** - True 和 False。
* **Null** - null 值。
* **数组** - 索引、关联和任意维数的多维数组。
* **对象** - 任意支持无损序列化和反序列货摊对象，比如 `$o == unserialize(serialize($o))`。对象**可以**利用 PHP 的 Serializable 接口、 `__sleep()` 或 `__wakeup()` 魔术方法以及与此类似的其他适用的语言功能。

所有传递级「实现库」的数据**必须**被原样返回，包括变量类型。就是说，保存整数 5 返回了字符串 5 是一个错误。「实现库」**可以**使用 PHP 内置的 `serialize()`/`unserialize()` 函数，但也可以不用。
与其保持兼容的底限是能接受对象值。

在不能精确返回已保存值的情况下，「实现库」**必须**返回缓存未命中，而不是返回有缺陷的数据。

## 关键概念

### 缓存池

「缓存池」表示缓存系统中的元素的集合，是它所包含的全部元素的逻辑存储库。所有能缓存的元素以「元素」对象的形式自「缓存池」中检索，与整个缓存对象环境进行的所有交互都是通过「缓存池」发生的。

### 元素

「元素」表示「缓存池」中单一的键/值对。键是「元素」的唯一标识，它**必须**是不可变的。「值」则**可以**在任意时刻被改变。

## 错误处理

虽然缓存常常是应用性能的重要组成部分，但它不应成为应用功能的决定性成分。缓存系统的错误**不应**引起应用的失败。
在这种情况下，「实现库」**不得**抛出与接口定义不同的异常，而**应当**捕获底层数据存储触发的错误和异常，禁止它们向上冒泡。

「实现库」**应当**鼗这些错误记入日志，或者根据需要将它们报告给管理员。

如果「调用库」请求一个或多个被删除的「元素」或者被清空的池，以致指定的键不存在，它**不得**将其视为一个错误条件。后置条件与造成这种结果的原因相同（键不存在或者池为空），但不是错误条件。

## 接口

### CacheItemInterface

CacheItemInterface 定义了缓存系统中的元素。每个「元素」对象**必须**与指定键关联，它能够由实现系统设置，且通常由 `Cache\CacheItemPoolInterface` 对象传递。

`Cache\CacheItemInterface` 对象封装了缓存元素的存储和检索。它由 `Cache\CacheItemPoolInterface` 对象生成，`Cache\CacheItemPoolInterface` 对象负责必要的设置并将 `Cache\CacheItemInterface` 对象与唯一键关联。`Cache\CacheItemInterface` 对象**必须**能够存储和检索本文档的「数据」一节定义的 PHP 值类型。

「调用库」**不得**自己实例化「元素」对象。它们仅可以使用 `getItem()` 方法调用向「缓存池」发起请求。「调用库」**不应**假定一个「实现库」创建的「元素」与来自另一「实现库」的「缓存池」兼容。

~~~php
<?php

namespace Psr\Cache;

/**
 * CacheItemInterface 定义了与缓存中的对象进行交互的接口。
 */
interface CacheItemInterface
{
    /**
     * 返回当前缓存元素的键。
     *
     * 将由「实现库」加载，但将在需要时为更高一级调用者所用。
     *
     * @return string
     *   当前缓存元素的键字符串。
     */
    public function getKey();

    /**
     * 自缓存中检索与此对象的键关联的元素的值。
     *
     * 返回的值必须与 set() 方法存储的原始值完全相同。
     *
     * 如果 isHit() 返回假，此方法**必须**返回 null。注意此处的 null 是原始缓存值，
     * 所以 isHit() 方法**应当**被用于区分「找到了 null值」和「没有值被找到」。
     *
     * @return mixed
     *   与缓存对象值对应的值，如果找不到则为 null。
     */
    public function get();

    /**
     * 确认缓存元素是否被命中。
     *
     * 注意： 在 isHit() 调用和 get() 调用之间**不得**有竞态条件。
     *
     * @return bool
     *   请求结果被命中返回 True，否则返回 False。
     */
    public function isHit();

    /**
     * 设置此缓存元素所表示的值。
     *
     * $value 参数可以是能被 PHP 序列化的任意元素，虽然使用哪种序列化方法由「实现库」决定。
     *
     * @param mixed $value
     *   被存储的可序列化的值。
     *
     * @return static
     *   调用对象。
     */
    public function set($value);

    /**
     * 设置缓存元素的过期时间。
     *
     * @param \DateTimeInterface|null $expiration
     *   **必须**将元素视为过期的时间点。
     *   如果显式地传递 null，就使用默认值。如果未做设置，值或者将被永久存储，或者在「实现库」的最长时限内存储。
     *
     * @return static
     *   调用对象。
     */
    public function expiresAt($expiration);

    /**
     * 设置缓存元素的过期时间。
     *
     * @param int|\DateInterval|null $time
     *   从当前时间算起，缓存元素**必须**被视为过期的时间段。整数参数表示以秒为单位的过期时间段。如果显式传递 null，就使用默认址。
     *   如果未做设置，值或者将被永久存储，或者在「实现库」的最长时限内存储。
     *
     * @return static
     *   调用对象。
     */
    public function expiresAfter($time);

}
~~~

### CacheItemPoolInterface

Cache\CacheItemPoolInterface 从「调用库」接收一个键，并返回与之关联的 Cache\CacheItemInterface 对象。
它还是与整个缓存集合交互的主要节点。「缓存池」的全部配置和初始化由「实现库」完成。

~~~php
<?php

namespace Psr\Cache;

/**
 * CacheItemPoolInterface 生成 CacheItemInterface 对象。
 */
interface CacheItemPoolInterface
{
    /**
     * 返回表示指定键的缓存元素。
     *
     * 此方法必须返回 CacheItemInterface 对象，即使在缓存未命中的情况下。**不得**返回 null 值。
     *
     * @param string $key
     *   想要返回对应缓存元素的键。
     *
     * @throws InvalidArgumentException
     *   如果键字符串非法，则**必须**抛出 \Psr\Cache\InvalidArgumentException 异常。
     *
     * @return CacheItemInterface
     *   与给定键对应的缓存元素。
     */
    public function getItem($key);

    /**
     * 返回一个可遍历的缓存元素集合。
     *
     * @param string[] $keys
     *   要检索的元素的键的索引数组。
     *
     * @throws InvalidArgumentException
     *   如果 $keys 数组的任意一个键非法，则**必须**抛出 \Psr\Cache\InvalidArgumentException 数组。
     *
     * @return array|\Traversable
     *   每个成员均由缓存键作为标识键的缓存元素的可遍历集合。总是为每个键返回一个缓存元素，即使该键没有找到。如果没有指定任何键，也**必须**返回一个空的可遍历集合。
     */
    public function getItems(array $keys = array());

    /**
     * 确认缓存是否包含指定的缓存元素。
     *
     * 注意： 由于性能原因，此方法**可以**不检索缓存值。这可能导致与 CacheItemInterface::get() 的竞态条件。可以使用 CacheItemInterface::isHit() 代替本方法来避免出现这种情况。
     *
     * @param string $key
     *   检测是否存在的键。
     *
     * @throws InvalidArgumentException
     *   如果 $key 值非法，则**必须**抛出 \Psr\Cache\InvalidArgumentException 异常。
     *
     * @return bool
     *   如果缓存中存在元素返回 True，否则返回 False。
     */
    public function hasItem($key);

    /**
     * 删除池中全部元素。
     *
     * @return bool
     *   成功清除缓存池返回 True，出错返回 False。
     */
    public function clear();

    /**
     * 从池中删除元素。
     *
     * @param string $key
     *   想要删除元素的键。
     *
     * @throws InvalidArgumentException
     *   如果 $key 值非法，则**必须**抛出 \Psr\Cache\InvalidArgumentException 异常。
     *
     * @return bool
     *   元素成功删除返回 True，出错返回 False。 
     */
    public function deleteItem($key);

    /**
     * 从池中删除多个元素。
     *
     * @param string[] $keys
     *   想要删除元素的键的数组。
     *
     * @throws InvalidArgumentException
     *   如果 $keys 中任意一个键非法，则**必须**抛出 \Psr\Cache\InvalidArgumentException 异常。
     *
     * @return bool
     *   成功删除元素返回 True，否则返回 False。
     */
    public function deleteItems(array $keys);

    /**
     * 实时持久化缓存元素。
     *
     * @param CacheItemInterface $item
     *   要保存的缓存对象。
     *
     * @return bool
     *   元素成功持久化返回 True，否则返回 False。 
     */
    public function save(CacheItemInterface $item);

    /**
     * 设置缓存元素延迟缓存。
     *
     * @param CacheItemInterface $item
     *   要保存的缓存对象。
     *
     * @return bool
     *   如果元素未能被加入队列，或者尝试提交失败返回 False，否则返回 True。 
     */
    public function saveDeferred(CacheItemInterface $item);

    /**
     * 持久化已延迟的缓存元素。
     *
     * @return bool
     *   所有未保存的元素成功保存，或者没有未保存元素返回 True，否则返回 False。 
     */
    public function commit();
}
~~~

### CacheException

此异常接口设计用于发生严重错误时，包括但不限于*缓存设置*（例如连接缓存服务或提供的凭证无效）。

被「实现库」抛出的异常**必须**实现本接口。

~~~php
<?php

namespace Psr\Cache;

/**
 * 实现库抛出的全部异常的接口。
 */
interface CacheException
{
}
~~~

### InvalidArgumentException

~~~php
<?php

namespace Psr\Cache;

/**
 * 无效缓存参数异常接口。
 *
 * 无论何时向方法传递无效参数都必须抛出实现了 Psr\Cache\InvalidArgumentException 接口的异常类
 */
interface InvalidArgumentException extends CacheException
{
}
~~~
