原文地址:[http://clang.llvm.org/docs/ClangFormatStyleOptions.html](http://clang.llvm.org/docs/ClangFormatStyleOptions.html)

Clang-Format Style Options

[Clang-Format Style Options](http://clang.llvm.org/docs/ClangFormatStyleOptions.html) 描述了可配置的格式样式选项,由[LibFormat](http://clang.llvm.org/docs/LibFormat.html) 和 [ClangFormat](http://clang.llvm.org/docs/ClangFormat.html)提供支持.

当在命令行下使用**clang-format**工具或代码中的`clang::format::reformat(...)`函数,可以使用已定义好的代码样式(LLVM, Google, Chromium, Mozilla, WebKit)或者通过配置特定的样式选项创建自定义样式.

##### clang-format的配置样式

**clang-format**支持两种自定义样式选项:通过`-style=`命令行直接指定样式配置,或者使用`-style=file`并将样式配置放在项目目录下的`.clang-format`或`_clang-format`文件中.

当使用`-style=file`时,clang-format会为每个输入文件尝试在输入文件最近的上级目录查找`.clang-format`文件.当使用标准输入时,查找从当前目录开始.

`.clang-format`使用YAML样式:
```
key1: value1
key2: value2
# A comment.
...
```

该配置文件由多个部分组成,每部分都有不同的`Language:`参数说明该部分针对的编程语言.参阅下面的语言选项描述,以获得支持的语言列表.第一部分可能没有语言集,将对所有语言使用默认格式选项.对特定语言的配置将覆盖默认的配置选项.

当`clang-format`格式化一个文件,它会通过文件名自动检测语言.当使用标准输入或一个没有相应其语言的扩展的文件,`-assume-filename=`选项可以用于覆盖文件名使`clang-format`自动获取语言.

一个针对多语言的配置文件样例

```
---
# We'll use defaults from the LLVM style, but with 4 columns indentation.
BasedOnStyle: LLVM
IndentWidth: 4
---
Language: Cpp
# Force pointers to the type for C++.
DerivePointerAlignment: false
PointerAlignment: Left
---
Language: JavaScript
# Use 100 columns for JS.
ColumnLimit: 100
---
Language: Proto
# Don't format .proto files.
DisableFormat: true
...
```

一种获取可用的`.clang-format`文件并囊括预定义风格的全部配置选项的简单方法:
```
clang-format -style=llvm -dump-config > .clang-format
```
     