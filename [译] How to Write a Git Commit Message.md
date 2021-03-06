## [译] How to Write a Git Commit Message

如果你翻看某个git repo的log信息，你可能会发现它的commit messages是一团毛线，比如，看看我在早期在Spring项目中提交的「稀世珍宝」:



```csharp
$ git log --oneline -5 --author cbeams --before "Fri Mar 26 2009"

e5f4b49 Re-adding ConfigurationPostProcessorTests after its brief removal in r814. @Ignore-ing the testCglibClassesAreLoadedJustInTimeForEnhancement() method as it turns out this was one of the culprits in the recent build breakage. The classloader hacking causes subtle downstream effects, breaking unrelated tests. The test method is still useful, but should only be run on a manual basis to ensure CGLIB is not prematurely classloaded, and should not be run as part of the automated build.
2db0f12 fixed two build-breaking issues: + reverted ClassMetadataReadingVisitor to revision 794 + eliminated ConfigurationPostProcessorTests until further investigation determines why it causes downstream tests to fail (such as the seemingly unrelated ClassPathXmlApplicationContextTests)
147709f Tweaks to package-info.java files
22b25e0 Consolidated Util and MutableAnnotationUtils classes into existing AsmUtils
7f96f57 polishing
```

噫。比较一下最近提交的commit messages吧:



```kotlin
$ git log --oneline -5 --author pwebb --before "Sat Aug 30 2014"

5ba3db6 Fix failing CompositePropertySourceTests
84564a0 Rework @PropertySource early parsing logic
e142fd1 Add tests for ImportSelector meta-data
887815f Update docbook dependency and generate epub
ac8326d Polish mockito usage
```

你想读哪一种呢？

前一种又臭又长；后一种精准一致。前一种来自「不假思索」，而后一种来自「精心组织」。

很多的repo的log信息都是前一种风格，然而很多却不是。比如[Linux kernel](https://link.jianshu.com?t=https://github.com/torvalds/linux/commits/master) 和 [git 本身](https://link.jianshu.com?t=https://github.com/git/git/commits/master)都是很好的例子。再看看[Spring Boot](https://link.jianshu.com?t=https://github.com/spring-projects/spring-boot/commits/master)项目，以及所有[Tim Pope](https://link.jianshu.com?t=https://github.com/tpope/vim-pathogen/commits/master)领导的项目吧。

这些项目的贡献者都清楚，与项目开发者们(当然也包括了自己)沟通的最好方式是构建一个精巧的 commit messages 结构。diff 会告诉你哪些文件改动了，但只有commit message能告诉你其中的原因。Peter Hutterer 很好地抓住了[要点](https://link.jianshu.com?t=http://who-t.blogspot.co.at/2009/12/on-commit-messages.html)：

> 重构代码是很浪费时间的。这一点我们完全无法避免，所以我们应该尽可能地[减少它](https://link.jianshu.com?t=http://www.osnews.com/story/19266/WTFs_m)，commit messages 就是为了解决这个问题而诞生，而且，它能看出一个开发者是不是一个很好的合作者。

如果你还没有好好想过一个好的commit message是什么样子，那可能是因为你没在git log以及相关的工具上花很多时间。有一个这样的怪圈：因为commit的历史没有结构性、连续性，没有人使用或关注这个项目。而又因为没有人使用、关注这个项目，commit messages的历史一直保持着无结构、不连续的。

但一个细心维护的log具有美感，并且能起作用。git blame, revert, rebase, log, shortlog 以及其他的子命令来源于生活。审查别人的commits，pull request是很有价值的事情，现在却变成了独立的行为。理解几个月或几年前这些是怎么发生的不仅是有可能的，也是有作用的。

一个项目的长时期成功依赖于它的维护性，而项目维护者找不到比项目log更有用的工具了。花时间学习、针对性地实践是很有用的，万事开头难，但一旦成为了习惯，它会成为所有参与者荣耀和生产力的源泉。

在这篇博客中，我提出的是维护一个健康 commit 历史信息的基本要素： 怎么写一个commit message。这里我不会写其他的重要实践，比如commit squashing。我可能会在随后的博文中写这些。

大多数编程语言有固有的惯例，形成了惯用的风格，也就是命名，格式化这些。当然这些惯例中有很多变数，但大多数程序员都同意坚持一种风格比每个人干自己的、一片混乱要好得多。

一个团队 commit 的方法也是一样。为了维护一个有效的版本历史，团队首先要在commit message惯例上达成共识，最少要有下面的三项：

**风格**。 标记语法，折行长度， 语法，大写规则，标点符号。明确规定这些，不留下猜测空间，尽可能的简洁。最终的结果是极其一致的log，它让人乐于阅读，当然，也实现了基本的功能。

**内容**。commit message的内容应该包含何种信息，不应该包含何种信息。

**元数据**。 issue 的ID， pull request的数字应该怎么记录，索引等等。

幸运的是，有一些完善的惯例指导我们写 git commit 信息。当然，很多都是用git命令来解决的。你不需要重新造轮子。遵循下面的七条准则，你就可以像专业人士一样commit了。

## 七条准则，写给力的git commit message

> 记着， 这都是前人智慧

1. [用空行分开主题和正文](#1)
2. [限制主题在50个字母](#2)
3. [主题行首字母要大写](#3)
4. [不要用句号结束主题行](#4)
5. [主题行用祈使语气](#5)
6. [每行72个字](#6)
7. [在正文部分解释什么，为什么，以及怎么做的](#7)

举个例子



```bash
 用不超过50个字简述一下有哪些改变  

 如果必要的话，写更多的细节。每行不要超过72个字。第一行被视为 commit 信息以及余下正文的主题。空行分开概要和正文是非常必要的（除非你不写正文）；如果你把 log, shortlog, rebase这样的工具混着用会让人迷惑。

 解释一下这个commit解决了什么问题。专注于为什么这么做而不是怎么做的（代码已经解释了）。这个改变是否有副作用或其他不直观的后果。这里就是解释这些事情的地方。

 空行之后还有段落。

  - 要点符号也是可以的

  - 通常用连字符，星号来表示要点符号。用一个空格起头，用空行隔开，当然，惯例没有这么详细

如果你用了 issue tracker， 把这些引用放在底部，就像这样：

解决了：#123
参考： #456, #789
```

### <span id='1'>1. 用空行分开主题和正文</span>

来自git commit的帮助文档：

> 虽不强求，用一个单行（不超过50个字）概述一下改动，跟着一个空行，更多一点描述，这就很好。在第一个空行上的文字会被作为commit的标题，git中会使用到。举个例子，git-format-patch命令可以把commit转为邮件，标题就会作为邮件的主题，正文就会当做邮件的正文。

首先，并不是每一个commit都需要标题，正文。有时候一行也可以的，特别是改动比较小，不需要更多的信息的时候。举个例子：



```undefined
修改用户导读中的拼写错误
```

不需要说太多；如果读者想知道什么拼写错误，他可以自己看改动，即，用`git show`，`git diff` 或 `git log -p`

如果你在命令行终端中提交，用`git commit -m` 就很简单：



```bash
git commit -m"Fix typo in introduction to user guide"
```

但是，如果你的 commit 需要一点点解释文字，你就需要写正文了，比如：



```rust
Derezz the master control program

MCP turned out to be evil and had become intent on world domination.
This commit throws Tron's disc into MCP (causing its deresolution)
and turns it back into a chess game.
```

这个用 -m 就没那么容易了。你需要一个合适的编辑器，如果你还没有在终端中给 git 设置编辑器，读[Pro Git 的这一章](https://link.jianshu.com?t=http://git-scm.com/book/ch7-1.html#Basic-Client-Configuration)。

在任何情况下，看log的时候，主题和正文是分开的。这有个完整的log：



```ruby
$ git log
commit 42e769bdf4894310333942ffc5a15151222a87be
Author: Kevin Flynn <kevin@flynnsarcade.com>
Date:   Fri Jan 01 00:00:00 1982 -0200

 Derezz the master control program

 MCP turned out to be evil and had become intent on world domination.
 This commit throws Tron's disc into MCP (causing its deresolution)
 and turns it back into a chess game.
```

如果是`git log --oneline`， 只输出主题行：



```bash
$ git log --oneline
42e769 Derezz the master control program
```

或者，用`git shortlog`, 按照贡献者分组，按行输出主题：



```ruby
$ git shortlog
Kevin Flynn (1):
      Derezz the master control program

Alan Bradley (1):
      Introduce security program "Tron"

Ed Dillinger (3):
      Rename chess program to "MCP"
      Modify chess program
      Upgrade chess program

Walter Gibbs (1):
      Introduce protoype chess program
```

git中有一些命令可以将主题和正文区分开   ----但主题、正文没有空行可不行。

### <span id='2'>2. 限制主题在50个字母</span>

50个字不是硬性标准，只是一个大致的准则。确保主题行是可读的，让开发者一眼就知道有哪些改变。

> 小技巧：如果你概述的时候发现很困难。可能是因为你一次 commit 太多改动了。尽可能做到 [atomic commits](https://link.jianshu.com?t=http://www.freshconsulting.com/atomic-commits/)(每次post一个主题)

GitHub 的用户界面很注意这些惯例。如果你超过了50个字的限制它会警告你：

![img](https:////upload-images.jianshu.io/upload_images/228457-9148a0ad285dba08.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

github_commit_changes

超过69个字的主题就会用省略号截断：

![img](https:////upload-images.jianshu.io/upload_images/228457-8b81ba638e4b5bea.png?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

truncate_subject

所以，尽可能不要超过50个字，把69个字符当做上限。

### <span id='3'>3. 主题行首字母要大写</span>

就这么简单。主题行首字母大写。
 比如：

- Accelerate to 88 miles per hour

而不是：

- accelerate to 88 miles per hour

### <span id='4'>4. 不要用句号结束主题行</span>

主题行结尾的句号是不必要的。而且，要少于50个字符的话，空间是很宝贵的。

比如：

- Open the pod bay doors

而不是：

- Open the pod bay doors.

### <span id='5'>5. 主题行用祈使语气</span>

祈使语气意味着，以命令或指示的语气说话或写作。举几个例子：

- 打扫你的房间
- 把门关上
- 把垃圾倒了

你正在读的七条准则就是祈使语气（例如，正文部分每行72个字）

祈使语气听起来有点粗鲁；所以我们不常用。但 git commit 的主题行就很合适，一个理由是：**无论何时你创建commit，git 本身就是祈使语气的**

比如，`git merge` 创建的默认信息是：



```bash
 Merge branch 'myfeature'
```

当你用 `git revert` 时:



```bash
Revert "Add the thing with the stuff"

This reverts commit cc87791524aedd593cff5a74532befe7ab69ce9d.
```

在 GitHub 上pull request 点「Merge」按钮的时候:



```bash
Merge pull request #123 from someuser/somebranch
```

所以，当你以祈使语气 commit messages 的时候，你遵循的正是 git 自身的传统。 比如：

- Refactor subsystem X for readability (重构子系统X，增强可读性)
- Update getting started documentation (更新初始文档)
- Remove deprecated methods (移除将废弃的函数)
- Release version 1.0.0 (发布 1.0.0 版本)

用这种方式一开始可能有点不适应。我们更习惯于指示性的语气，像是报告事实一样。 因此，commit messages 经常像这样：

- Fixed bug with Y (用 Y 解决了bug)
- Changing behavior of X (改变了 X 的行为)

有时候 commit messages 只是在描述 commit 的内容:

- More fixes for broken stuff (更多修复)
- Sweet new API methods (优雅的新API)

为了消除困惑，这有一条简单的准则：

**一个合理格式化后的 git commit 主题行总能替换到下面的句子中: **

- 如果应用了，这个 commit 会<u>你的主题行</u>

比如：

- 如果应用了，这个 commit 会重构子系统X，增强可读性
- 如果应用了，这个 commit 会更新初始文档
- 如果应用了，这个 commit 会移除将废弃的函数
- 如果应用了，这个 commit 会发布 1.0.0 版本
- 如果应用了，这个 commit 会合并来自 user/branch 的pull request #123

注意，在非祈使的条件下就不适用了，如：

- If applied, this commit will fixed bug with Y
- If applied, this commit will changing behavior of X
- If applied, this commit will more fixes for broken stuff
- If applied, this commit will sweet new API methods

> 记着，在主题行中用祈使句很重要。在写正文的时候就不用在乎这个限制了。

### <span id='6'>6. 每行72个字</span>

Git 不会自动折行，当你写 commit message 的正文时，你必须考虑右边的长度，人工地折行。

推荐每72个字就折行，让 git 满足80个字的限制的时候有足够的空间缩进正文。

一个好的编辑器就起作用了。配置 Vim 是很简单的, 比如，设置折行为72个字。一般来说，IDE在智能折行上表现就很糟糕了（在近期的版本中，IntelliJ IDEA 终于有了[比较好的支持](https://link.jianshu.com?t=http://youtrack.jetbrains.com/issue/IDEA-53615#comment=27-448299)）

### <span id='7'>7. 在正文部分解释什么，为什么，以及怎么做的</span>

这一[Bitcoin Core 的commit](https://link.jianshu.com?t=https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6)很好的说明了什么改动了，以及为什么改动：



```php
commit eb0b56b19017ab5c16c745e6da39c53126924ed6
Author: Pieter Wuille <pieter.wuille@gmail.com>
Date:   Fri Aug 1 22:57:55 2014 +0200

   Simplify serialize.h's exception handling

   Remove the 'state' and 'exceptmask' from serialize.h's stream
   implementations, as well as related methods.

   As exceptmask always included 'failbit', and setstate was always
   called with bits = failbit, all it did was immediately raise an
   exception. Get rid of those variables, and replace the setstate
   with direct exception throwing (which also removes some dead
   code).

   As a result, good() is never reached after a failure (there are
   only 2 calls, one of which is in tests), and can just be replaced
   by !eof().

   fail(), clear(n) and exceptions() are just never called. Delete
   them.
```

看看[完整的 diff](https://link.jianshu.com?t=https://github.com/bitcoin/bitcoin/commit/eb0b56b19017ab5c16c745e6da39c53126924ed6)，想想这个 commit message 给队友以及后继的开发者节约了多少时间吧。如果他没有这样写，这些变动可能就永远都看不到了。

在大多数情况下，你可以忽略变动的细节，代码基本上是不言自明的（如果代码很复杂，就需要文字解释，这就需要注释了），首先说清楚改动的原因----改动前代码是怎么工作的（有什么问题），现在又是怎样的，你为什么要解决这个问题，又是怎么解决的。

未来感谢你的软件维护者可能就是你自己！

## 小技巧

### 享受命令行，远离IDE

git 有很多子命令是不无理由的，拥抱命令行是明智的选择。Git 太TM强大了；IDE 当然也是，但每一种有不同的方式。我每天都用IDE（IntelliJ IDEA），也用其他（如Eclipse）， 但我从没见过能与强大、简单的终端媲美的集成了git的IDE(你懂得)

诚然，集成了 git 的IDE 很难得，比如可以删除文件时调用`git rm`，重命名文件是调用相关的git命令。但要完成 commit, merge, rebase，或复杂的历史版本分析时，IDE就捉襟见肘了。

要想充分发挥 git 的威力，那就必须命令行满屏幕飞了。

记着，不管是用 Bash 或是 Z shell，[Tab 补全](https://link.jianshu.com?t=http://git-scm.com/book/en/Git-Basics-Tips-and-Tricks)能让你减轻记命令的痛苦。

### 读《Pro Git》

[Pro Git](https://link.jianshu.com?t=http://git-scm.com/book)是在线，免费的，真是极好的。好好利用！



作者：knarfeh
链接：https://www.jianshu.com/p/0117334c75fc
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。