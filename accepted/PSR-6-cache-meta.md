# PSR-缓存元文档

## 1. 概述

缓存是提高项目性能的通用方法，建立骑在缓存库是许多框架和库的常见特性之一。这就导致了一种情况：许多库使用不同级别的功能迭代自己的缓存库。
这些差异导致开发者不得不学习多个具有或不具有他们所需功能的系统。另外，缓存库的开发者也要面对在仅支持部分框架或创建一个大量适配器类之间做选择的难题。

## 2. 为什么这么麻烦

缓存系统的通用接口能解决上述问题。当缓存库的开发者仅仅实现单一的一组接口，而不是完全不同的适配器时，库和框架开发者就能够指望缓存系统以他们期望的方式工作。

而且，此处的实现是为未来的可扩展性设计的。它允许内部差异但 API 兼容的实现，并为之后的 PSR 或特殊实现提供了清晰的扩展路径。

优点：

* 缓存的标准接口允许独立库不费吹灰之力就支持中间数据的缓存；他们可以简单地（可选地）依赖和利用这些标准接口，且无需关心其具体实现。
* 由多个项目共享的通用缓存库，即使其接口被扩展，也可能比多个独立开发的实现更健壮。

缺点：

* 任何标准化的接口都有扼杀创新的风险，因为「它不是**这么做的**」。不过，我们相信缓存是一个十分商业化的问题领域，此处提供的扩展可以缓解任何潜在的停滞风险。

## 3. 范围

### 3.1 目标

* 一个初级和中级缓存的通用接口。
* 一种用于扩展规范以支持高级特性（无论是通过将来的 PSR 还是单独的实现）的明确机制。此机制必须允许多个独立扩展而不发生冲突。

### 3.2 目标之外[^译者注]

* 架构兼容所有已存在的缓存实现
* 少数用户使用的诸如命名空间和标签之类的高级功能。

## 4. 方案

### 4.1 既定方案

本规范使用「存储库模型」或「数据映射器」模型，而不是传统的「可过期键值对」模型。主要是因为灵活性。简单的键值对模型更难扩展。

此模型强制使用 CacheItem 对象（一种表示缓存实体的对象）和池对象（一种缓存数据的给定存储对象）。CatcheItem 对象从池中检索、与之交互并返回给自己。
虽然有时有点繁琐，但它提供了更好、更健壮、理灵活的缓存解决方案，尤其是在比简单地存取字符串更复杂的情况下。

大多数方法名都是基于对成员项目和其他流行的非成员项目的调查中常见实践和方法名来选择的。

优点：

* 灵活性和可扩展性
* 允许在不与接口相违背的前提下，在实现中包含大量的变化。
* 没有暗中将对象构造器暴露为伪接口。

缺点：

* 比内置方案更复杂

示例：

下面展示了一些常用的模式。这些都是不规范的，但能说明一些设计决策的应用。

~~~php
/**
 * 获取可用部件列表。
 *
 * 此处我们假定部件列表很少变动，我们想让列表在清楚前一直被缓存。
 */
function get_widget_list()
{
    $pool = get_cache_pool('widgets');
    $item = $pool->getItem('widget_list');
    if (!$item->isHit()) {
        $value = compute_expensive_widget_list();
        $item->set($value);
        $pool->save($item);
    }
    return $item->get();
}
~~~

~~~php
/**
 * 缓存可用部件列表。
 *
 * 此处我们假定部件列表已被计算且我们想缓存它，不理会我们已经缓存了什么。
 */
function save_widget_list($list)
{
    $pool = get_cache_pool('widgets');
    $item = $pool->getItem('widget_list');
    $item->set($list);
    $pool->save($item);
}
~~~

~~~php
/**
 * 清除可用部件列表。
 *
 * 此处我们仅想从缓存中移除部件列表。不关心它是否已缓存；后置条件仅为「非长期设置」。
 */
function clear_widget_list()
{
    $pool = get_cache_pool('widgets');
    $pool->deleteItems(['widget_list']);
}
~~~

~~~php
/**
 * 清除所有部件信息。
 *
 * 此处我们想要清空整个部件池。应用中可能还有其它的池，它们将不受影响。
 */
function clear_widget_cache()
{
    $pool = get_cache_pool('widgets');
    $pool->clear();
}
~~~

~~~php
/**
 * 载入部件。
 *
 * 我们想要取回部件列表，其中一些已被缓存，一些则没有。 这当然就表现为从缓存中载入要比非缓存载入机制要快。
 *
 * 此处，我们假设部件可能频繁变化，所以我们只允许它们被缓存一小时（3600秒）。我们还将新加载的对象全部缓存回池中。
 *
 * 需要留意，真实环境的实现可能还想要部件的多载入操作，但与此范例无关。
 */
function load_widgets(array $ids)
{
    $pool = get_cache_pool('widgets');
    $keys = array_map(function($id) { return 'widget.' . $id; }, $ids);
    $items = $pool->getItems($keys);

    $widgets = array();
    foreach ($items as $key => $item) {
        if ($item->isHit()) {
            $value = $item->get();
        } else {
            $value = expensive_widget_load($id);
            $item->set($value);
            $item->expiresAfter(3600);
            $pool->saveDeferred($item, true);
        }
        $widget[$value->id()] = $value;
    }
    $pool->commit(); // 如果没有元素延迟处理，此函数什么也不做。

    return $widgets;
}
~~~

~~~php
/**
 * 此例中的动向功能**没有**包含在规范中，但是例子中展示的一些功能**可能**被添加在扩展实现中。
 */

interface TaggablePoolInterface extends Psr\Cache\CachePoolInterface
{
    /**
     * 仅从池中清除有特定标签的元素。
     */
    clearByTag($tag);
}

interface TaggableItemInterface extends Psr\Cache\CacheItemInterface
{
    public function setTags(array $tags);
}

/**
 * 缓存带有标签的部件。
 */
function set_widget(TaggablePoolInterface $pool, Widget $widget)
{
    $key = 'widget.' . $widget->id();
    $item = $pool->getItem($key);

    $item->setTags($widget->tags());
    $item->set($widget);
    $pool->save($item);
}
~~~

### 4.2 备选方案：「弱元素」方案

一些较早的草案提供了简单的「过期键值对」方案，也被称为「弱元素」方案。在这个模型中，「缓存元素」只是一个带有方法的哑数组对象。
用户直接实例化它，然后传递给它一个缓存池。虽然更熟悉，但那些方案实际上阻止了扩展**缓存元素**的企图。它使**缓存元素**的构造函数成为隐式接口的一部分， 从而极大地限制了可扩展性和缓存元素位于智能存活区的能力。

2013年6月举行的一项调查中，多数参与者倾向于将更健壮但并不传统的「强元素」/存储库方案采纳为未来的发展方向。

优点：

* 更传统的方案。

缺点：

* 缺少扩展性和灵活性。

### 4.3 备选方案：「裸值」方案

关于缓存规范的早期讨论建议路过所有的**缓存元素**概念，而直接读取要缓存的原始值。虽然简单，但有人指出这种方法无法区分缓存未命中和被用于表示缓存未命中的原始值。
就是说，如果缓存查找返回了一个 NULL，它可能表示没有缓存值或者被缓存的值就是 NUll 本身（在很多情况下 NULL 是一个合法的缓存值）。

我们评估了很多更健壮的缓存实现（尤其是存储缓存库和 Drupal 使用的自建缓存库），在 `get` 上使用某种结构化对象，至少可以区分「未命中」和「哨兵值」。依据此前的经验，FIG 决定不能在 `get` 上使用裸值。

### 4.4 备选方案：数组访问池

有一个使用「池」实现「数组访问」的建议，即允许在缓存读写操作中使用数组语法。由于好处、灵活性有限（能够实现带有默认操作信息的简单读写），并且某一实现很容易将其添加为附加组件，导致该方案被拒绝。

## 5. 参与人员

### 5.1 编辑

* Larry Garfield

### 5.2 担保人

* Paul Dragoonis, PPI Framework (Coordinator)
* Robert Hafner, Stash

## 6. 投票

[接受投票的邮件列表](https://groups.google.com/forum/#!msg/php-fig/dSw5IhpKJ1g/O9wpqizWAwAJ)

## 7. 相关链接

_**注意** 按时间先后倒序排列。_

* [缓存实现纵览][1], by @dragoonis
* [强 vs. 弱 非正式调查][2], by @Crell
* [实现细节非正式调查][3], by @Crell

[1]: https://docs.google.com/spreadsheet/ccc?key=0Ak2JdGialLildEM2UjlOdnA4ekg3R1Bfeng5eGlZc1E#gid=0
[2]: https://docs.google.com/spreadsheet/ccc?key=0AsMrMKNHL1uGdDdVd2llN1kxczZQejZaa3JHcXA3b0E#gid=0
[3]: https://docs.google.com/spreadsheet/ccc?key=0AsMrMKNHL1uGdEE3SU8zclNtdTNobWxpZnFyR0llSXc#gid=1

## 8. 勘误表

### 8.1 在 expiresAt() 中处理错误「DataTime」值

`CacheItemInterface` 接口中 `CacheItemInterface::expiresAt()` 方法的 `$expiration` 参数未指定类型，
但是文档块中指明是 `\DateTimeInterface`。 即允许 `\DateTime` 和 `\DateTimeImmutable` 对象。但 `\DateTimeInterface` 和 `\DateTimeImmutable` 是在 PHP 5.5 引入的，且作者选择不在规范中强制要求必须是 PHP 5.5 语法。

尽管如此，实现**必须**仅允许 `\DateTimeInterface` 或其兼容类型（例如 `\DateTime` 和 `\DateTimeImmutable`）作为方法预期的输入类型。
（注意不同版本间的类型参数变化规则可能有所不同）。

模拟失败的类型检查，在不同的版本间有所不同，因此不推荐使用。取而代之的是，实现者**应当**抛出一个 `\Psr\Cache\InvalidArgumentException` 实例。下面的示例代码推荐用于在 expiresAt() 方法中强制类型检测：

~~~php

class ExpiresAtInvalidParameterException implements Psr\Cache\InvalidArgumentException {}

// ...

if (! (
        null === $expiration
        || $expiration instanceof \DateTime
        || $expiration instanceof \DateTimeInterface
)) {
    throw new ExpiresAtInvalidParameterException(sprintf(
        'Argument 1 passed to %s::expiresAt() must be an instance of DateTime or DateTimeImmutable; %s given',
        get_class($this),
        is_object($expiration) ? get_class($expiration) : gettype($expiration)
    ));
}
~~~

[^译者注]: 原文为 Non-Goals，意为「非目标」，即下面这段内容不是本标准的目标。
