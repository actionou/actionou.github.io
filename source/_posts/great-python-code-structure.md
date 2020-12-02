---
title: 写出优雅的Python代码之—仓库结构（译）
date: 2020-01-10 19:55:22
tags: [python]
---

我们对于“结构化”的定义是您关注于怎样使您的项目最好地满足它的对象性，我们需要去考虑如何更好地利用Python的特性来创造简洁、高效的代码。在实践层面， “结构化”意味着通过编写简洁的代码，并且正如文件系统中文件和目录的组织一样，代码应该使逻辑和依赖清晰。

哪个函数应该深入到哪个模块？数据在项目中如何流转？什么功能和函数应该组合或独立？要解决这些问题，您可以开始做一个计划，大体来说，即是您的最终产品看起来会是怎样的。

在这一章节中，我们更深入地去观察Python的模块和导入系统，因为它们是加强您的项目结构化的关键因素，接着我们会从不同层面去讨论如何去构建可扩展且测试可靠的的代码。

## 仓库结构很重要
在一个健康的开发周期中，代码风格、API设计和自动化是非常关键的。同样的，对于项目架构，仓库的结构也是关键的一部分。

当一个潜在的用户和代码贡献者访问您的代码仓库时，他们会看到这些:
- 工程的名字
- 工程的描述
- 一系列的文件

只有当他们滚动到目录下方时才会看到您工程的README。

如果您的仓库的目录一团糟，没有清晰的结构，他们可能要到处寻找才能发现您写的**漂亮**的文档。

> 为您渴望的事业而奋斗，而不仅仅只是为您现在的工作而工作。

当然，第一印象并不决定一切。但是，您和您的同事会和这个仓库并肩战斗很长时间，会接触它的每一个角落和细节。拥有良好的布局，事半功倍。

## 仓库样例

请看这里: 这是 [Kenneth Reitz](http://kennethreitz.org/) 推荐的。
这个项目可以在GitHub上找到。

```
README.rst
LICENSE
setup.py
requirements.txt
sample/__init__.py
sample/core.py
sample/helpers.py
docs/conf.py
docs/index.rst
tests/test_basic.py
tests/test_advanced.py
```

让我们再继续看一些细节。

## 真正的模块

| 布局 | 作用 |
| :------ | :------: |
| `./sample/` or `./sample.py` | 核心代码 |

您的模块包是这个仓库的核心，它不应该隐藏起来:

```
./sample/
```

如果您的模块只有一个文件，那么您可以直接将这个文件放在仓库的根目录下:

```
./sample.py
```

这个模块文件不应该属于任何一个模棱两可的src或者python子目录。

## License

| 布局 | 作用 |
| :------ | :------: |
| ./LICENSE | 许可证 |

除了源代码本身以外，这个毫无疑问是您仓库最重要的一部分。在这个文件中要有完整的许可说明和授权。
如果您不太清楚您应该使用哪种许可方式，请查看 [choosealicense.com](http://choosealicense.com/).
当然，您也可以在发布您的代码时不做任何许可说明，但是这显然阻碍潜在的用户使用您的代码。

## Setup.py

| 布局 | 作用 |
| :------ | :------: |
| `./setup.py` | 打包和发布管理 |

如果您的模块包在您的根目录下，显然这个文件也应该在根目录下。

## Requirements File

| 布局 | 作用 |
| :------ | :------: |
| `./requirements.txt` | 开发依赖 |

一个 [pip requirements file](https://pip.pypa.io/en/stable/user_guide/#requirements-files) 应该放在仓库的根目录。它应该指明完整工程的所有依赖包: 测试, 编译和文档生成。

如果您的工程没有任何开发依赖，或者您喜欢通过`setup.py`来设置，那么这个文件不是必须的。

## Documentation

| 布局 | 作用 |
| :------ | :------: |
| `./docs/` | 包的参考文档 |

没有任何理由把这个放到别的地方。

## Test Suite

想了解关于编写测试的建议，请查阅 [Testing Your Code](https://docs.python-guide.org/writing/tests/).

| 布局 | 作用 |
| :------ | :------: |
| `./test_sample.py` or `./tests` | 包的集合和单元测试 |

最开始，一组测试例子只是放在一个文件当中:

```
./test_sample.py
```

当测试例子逐步增加时，您会把它放到一个目录里面，像下面这样:

```
tests/test_basic.py
tests/test_advanced.py
```

当然，这些测试例子需要导入您的包来进行测试，有几种方式来处理:

- 将您的包安装到site-packages中。
- 通过简单直接的路径设置来解决导入的问题。

我极力推荐后者。如果使用`setup.py develop`来测试一个持续更新的代码库，需要为每一个版本的代码库设置一个独立的测试环境.太麻烦了。

可以先创建一个包含上下文环境的文件`tests/context.py`。 file:

```python
import os
import sys
sys.path.insert(0, os.path.abspath(os.path.join(os.path.dirname(__file__), '..')))

import sample
```

然后，在每一个测试文件中，导入:

```python
from .context import sample
```

这样就能够像期待的那样工作，而不用采用安装的方式。

一些人会说应该把您的测试例子放到您的模块里面。我不同意。这样会增加您用户使用的复杂度；而且添加测试模块将导致需要额外的依赖和运行环境。

## Makefile

| 布局 | 作用 |
| :------ | :------: |
| `./Makefile` | 常规的管理任务 |

如果您看看我的项目或者其他开源项目，您都会发现有一个Makefile。为什么？这些项目也不是用C写的啊。。。简而言之，make对于定义常规的管理任务是非常有用的工具。

**样例 Makefile:**

```
init:
    pip install -r requirements.txt

test:
    py.test tests

.PHONY: init test
```

一些其他的常规管理脚本（比如`manage.py`或者`fabfile.py`），也放在仓库的根目录下。

## 关于 Django Applications

从Django 1.4开始，有这样一个现象：很多开发者错误地使用Django自带的应用模板创建项目，导致他们的仓库结构非常糟糕。

这是怎么回事呢? 原来, 他们在创建一个新的仓库后通常都这样操作：

```bash
$ django-admin.py startproject samplesite
```

这样子操作生成的仓库结构是这样的:

```
README.rst
samplesite/manage.py
samplesite/samplesite/settings.py
samplesite/samplesite/wsgi.py
samplesite/samplesite/sampleapp/models.py
```

亲，不要这样做。

相对路径会让您的工具和您的开发者都很疑惑。没有必要的嵌套对任何人都没有好处（除非您怀念庞大的SVN仓库）。

让我们这样来做:

```bash
$ django-admin.py startproject samplesite .
```

注意末尾的"`.`"。

生成的结构是这样的:

```
README.rst
manage.py
samplesite/settings.py
samplesite/wsgi.py
samplesite/sampleapp/models.py
```

> END

---

翻译自 [https://docs.python-guide.org/writing/structure/](https://docs.python-guide.org/writing/structure/).
