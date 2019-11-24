# PHP规范建议

每个 PSR 当前的状态源自 [PSR Workflow Bylaw][workflow]。一旦提案进入到投票阶段，将作为“草案”列在此处。在一项 PSR 被标记为 “接受（Accepted）” 之前，可能会发生改变。 草案可能会有较大变化，但审阅仅会有微小调整。

如 [PSR Workflow Bylaw][workflow] 所述。提案的作者基本上是 PSR 的主要贡献者或编写者，他们需要两个投票委员的支持。投票委员是负责管理评审阶段和投票的协调员，以及第二提案人。

## 按状态分组的索引

### 接受（Accepted）

| 序号 | 标题                | 作者                             | 协调人                     | 提案人                     |
|----|-------------------|--------------------------------|-------------------------|-------------------------|
| 1  | [基本代码规范][psr1]    | Paul M. Jones                  | _N/A_                   | _N/A_                   |
| 3  | [日志接口][psr3]      | Jordi Boggiano                 | _N/A_                   | _N/A_                   |
| 4  | [自动加载规范][psr4]    | Paul M. Jones                  | Phil Sturgeon           | Larry Garfield          |
| 6  | [缓存接口][psr6]      | Larry Garfield                 | Paul Dragoonis          | Robert Hafner           |
| 7  | [HTTP 消息接口][psr7] | Matthew Weier O'Phinney        | Beau Simensen           | Paul M. Jones           |
| 11 | [容器接口][psr11]     | Matthieu Napoli, David Négrier | Matthew Weier O'Phinney | Korvin Szanto           |
| 12 | [扩展编码风格指南][psr12] | Korvin Szanto                  | Alexander Makarov       | Chris Tankersley        |
| 13 | [超媒体链接][psr13]    | Larry Garfield                 | Matthew Weier O'Phinney | Marc Alexander          |
| 14 | [事件调度器][psr14]    | Larry Garfield                 | _N/A_                   | Cees-Jan Kiewiet        |
| 15 | [HTTP 处理器][psr15] | Woody Gilk                     | _N/A_                   | Matthew Weier O'Phinney |
| 16 | [简单缓存][psr16]     | Paul Dragoonis                 | Jordi Boggiano          | Fabien Potencier        |
| 17 | [HTTP 工厂][psr17]  | Woody Gilk                     | _N/A_                   | Matthew Weier O'Phinney |
| 18 | [HTTP 客户端][psr18] | Tobias Nyholm                  | _N/A_                   | Sara Golemon            |

### 草案

| 序号 | 标题                 | 作者            |
|----|--------------------|---------------|
| 5  | [PHPDoc 规范][psr5]  | Chuck Burgess |
| 19 | [PHPDoc 标签][psr19] | Chuck Burgess |

### 丢弃

| 序号 | 标题                  | 作者             |
|----|---------------------|----------------|
| 8  | [Huggable 接口][psr8] | Larry Garfield |
| 9  | [安全公告][psr9]        | Michael Hess   |
| 10 | [安全报告流程][psr10]     | Michael Hess   |

### 弃用

| 序号 | 标题             | 作者                      |
|----|----------------|-------------------------|
| 0  | [自动加载规范][psr0] | Matthew Weier O'Phinney |
| 2  | [代码风格指南][psr2] | Paul M. Jones           |

## 编号索引

| 序号 | 标题                  | 作者                             | 状态 |
|----|---------------------|--------------------------------|----|
| 0  | [自动加载规范][psr0]      | Matthew Weier O'Phinney        | 弃用 |
| 1  | [基本代码规范][psr1]      | Paul M. Jones                  | 接受 |
| 2  | [代码风格指南][psr2]      | Paul M. Jones                  | 弃用 |
| 3  | [日志接口][psr3]        | Jordi Boggiano                 | 接受 |
| 4  | [自动加载规范][psr4]      | Paul M. Jones                  | 接受 |
| 5  | [PHPDoc 规范][psr5]   | Chuck Burgess                  | 草案 |
| 6  | [缓存接口][psr6]        | Larry Garfield                 | 接受 |
| 7  | [HTTP 消息接口][psr7]   | Matthew Weier O'Phinney        | 接受 |
| 8  | [Huggable 接口][psr8] | Larry Garfield                 | 丢弃 |
| 9  | [安全公告][psr9]        | Michael Hess                   | 丢弃 |
| 10 | [安全报告流程][psr10]     | Michael Hess                   | 丢弃 |
| 11 | [容器接口][psr11]       | Matthieu Napoli, David Négrier | 接受 |
| 12 | [扩展代码风格指南][psr12]   | Korvin Szanto                  | 接受 |
| 13 | [超媒体链接][psr13]      | Larry Garfield                 | 接受 |
| 14 | [事件调度器][psr14]      | Larry Garfield                 | 接受 |
| 15 | [HTTP 处理器][psr15]   | Woody Gilk                     | 接受 |
| 16 | [简单缓存][psr16]       | Paul Dragoonis                 | 接受 |
| 17 | [HTTP 工厂][psr17]    | Woody Gilk                     | 接受 |
| 18 | [HTTP 客户端][psr18]   | Tobias Nyholm                  | 接受 |
| 19 | [PHPDoc 标签][psr19]  | Chuck Burgess                  | 草案 |

[workflow]: https://github.com/php-fig/fig-standards/blob/master/bylaws/002-psr-workflow.md
[psr0]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-0.md
[psr1]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-1-basic-coding-standard.md
[psr2]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-2-coding-style-guide.md
[psr3]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-3-logger-interface.md
[psr4]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-4-autoloader-meta.md
[psr5]: https://github.com/php-fig/fig-standards/blob/master/proposed/phpdoc.md
[psr6]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-6-cache.md
[psr7]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-7-http-message.md
[psr8]: https://github.com/php-fig/fig-standards/blob/master/proposed/psr-8-hug/
[psr9]: https://github.com/php-fig/fig-standards/blob/master/proposed/security-disclosure-publication.md
[psr10]: https://github.com/php-fig/fig-standards/blob/master/proposed/security-reporting-process.md
[psr11]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-11-container.md
[psr12]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-12-extended-coding-style-guide.md
[psr13]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-13-links.md
[psr14]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-14-event-dispatcher.md
[psr15]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-15-request-handlers.md
[psr16]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-16-simple-cache.md
[psr17]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-17-http-factory.md
[psr18]: https://github.com/php-fig/fig-standards/blob/master/accepted/PSR-18-http-client.md
[psr19]: https://github.com/php-fig/fig-standards/blob/master/proposed/phpdoc-tags.md
