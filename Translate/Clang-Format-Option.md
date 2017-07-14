原文地址:[http://clang.llvm.org/docs/ClangFormatStyleOptions.html](http://clang.llvm.org/docs/ClangFormatStyleOptions.html)

Clang-Format Style Options

[Clang-Format Style Options](http://clang.llvm.org/docs/ClangFormatStyleOptions.html) 描述了可配置的格式样式选项,由[LibFormat](http://clang.llvm.org/docs/LibFormat.html) 和 [ClangFormat](http://clang.llvm.org/docs/ClangFormat.html)提供支持.

当在命令行下使用**clang-format**工具或代码中的`clang::format::reformat(...)`函数,可以使用已定义好的代码样式(LLVM, Google, Chromium, Mozilla, WebKit)或者通过配置特定的样式选项创建自定义样式.

---

 #### clang-format的配置样式

**clang-format**支持两种自定义样式选项:通过`-style=`命令行直接指定样式配置,或者使用`-style=file`并将样式配置放在项目目录下的`.clang-format`或`_clang-format`文件中.

当使用`-style=file`时,clang-format会为每个输入文件尝试在就近的上级目录查找`.clang-format`文件.当使用标准输入时,查找从当前目录开始.

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
```sh
clang-format -style=llvm -dump-config > .clang-format
```

当使用`-style=`选项自定义配置时,将对所有输入文件以同样的样式生效. 自定义配置的格式如下:
```sh
-style='{key1: value1, key2: value2, ...}'
```

--- 

#### 对一段代码禁用格式化

Clang-format同样支持用特殊的注释对一段限定范围的代码切换样式.在注释`//clang-format off`(或`/* clang-format off */`)和`//clang-format on`(或`/* clang-format on */`)之间的代码将不会被格式化.注释本身依旧会被格式化(对齐).

```c++
int formatted_code;
// clang-format off
    void    unformatted_code  ;
// clang-format on
void formatted_code_again;
```

--- 

#### 在代码中配置样式

当使用` clang::format::reformat(...)`等函数时,格式样式将通过使用` clang::format::FormatStyle`来指定

--- 

#### 可配置的格式样式选项

该部分将列出支持的样式选项.每个选项可被指定值类型.对于枚举类型,值可能被指定为c++枚举成员(带前缀,比如LS_Auto),以及在配置中可用的值(不带前缀的Auto).

---


**BasedOnStyle**(`string`)

该样式用于除被专门设定的之外的选项样式.

该选项只在clang-format配置中支持(`-style={}`和`.clang-format`文件均可).

可取的值:

- **LLVM**      将使用LLVM代码标准的样式
- **Google**    将使用Google C++代码指南的样式
- **Chromium**  将使用Chromium代码指南的样式
- **Mozilla**   将使用Mozilla代码指南的样式
- **WebKit**    将使用WebKit代码指南的样式

---

**AccessModifierOffset**(`int`)

访问修饰符的缩进或凸出,如`public`

---

**AlignAfterOpenBracket** (`BracketAlignmentStyle`)

若真,在开括号后保持水平对齐.

这适用于圆括号(括号),尖括号和方括号.

可取的值:

- `BAS_Align` (在配置中为`Align`)参数根据开括号对齐,如下

```c++
someLongFunction(argument1,
                 argument2);
``` 

- `BAS_DontAlign` (在配置中为`DontAlign`)不对齐,使用`ContinuationIndentWidth`,如下

```c++
someLongFunction(argument1,
    argument2);
```

- `BAS_AlwaysBreak` (在配置中为`AlwaysBreak`) 若参数不适合一行,将在开括号换行,如下

```c++
someLongFunction(
    argument1, argument2);
```

---

**AlignConsecutiveAssignments** (`bool`)

若`true`,则对齐连续赋值运算符.

这将对齐连续多行的赋值运算符.效果如下:

```c++
int aaaa = 12;
int b    = 23;
int ccc  = 23;
```

---

**AlignConsecutiveDeclarations** (`bool`)

若`true`，则对齐连续声明.

这将对齐连续多行的声明变量名.效果如下:

```c++
int         aaaa = 12;
float       b = 23;
std::string ccc = 23;
```

---

**AlignEscapedNewlines** (`EscapedNewlineAlignmentStyle`)

换行符(\)的选项

可取的值:

- `ENAS_DontAlign` (在配置中为`DontAlign`) 换行符(\)不对齐

```c++
#define A \
  int aaaa; \
  int b; \
  int dddddddddd;
```

- `ENAS_Left` (在配置中为`Left`) 换行符(\)尽量左对齐

```c++
true:
#define A   \
  int aaaa; \
  int b;    \
  int dddddddddd;

false:
```

- `ENAS_Right` (在配置中为`Right`) 换行符(\)尽量右对齐

```c++
#define A                                                                      \
  int aaaa;                                                                    \
  int b;                                                                       \
  int dddddddddd;
```

---

**AlignOperands** (`bool`)

若`true`,则水平对齐二目运算符和三目运算符的操作数

具体地说,这将单行表达式拆分成多行对齐,如下:

```c++
int aaa = bbbbbbbbbbbbbbb +
          ccccccccccccccc;
```

---

**AlignTrailingComments** (`bool`)

若`true`,则对齐注释

```c++
true:                                   false:
int a;     // My comment a      vs.     int a; // My comment a
int b = 2; // comment  b                int b = 2; // comment about b
```

---

**AllowAllParametersOfDeclarationOnNextLine** (bool)

在函数声明中允许将所有参数放下一行,除非为`false`.

```c++
true:                                   false:
myFunction(foo,                 vs.     myFunction(foo, bar, plop);
           bar,
           plop);
```

---

**AllowShortBlocksOnASingleLine** (`bool`)

允许将简单语句放置在一行中

样例:  `if (a) { return; }`将在一行

---

**AllowShortCaseLabelsOnASingleLine** (`bool`)

若`true`,则会将简单的case代码压缩在一行

```c++
true:                                   false:
switch (a) {                    vs.     switch (a) {
case 1: x = 1; break;                   case 1:
case 2: return;                           x = 1;
}                                         break;
                                        case 2:
                                          return;
                                        }
```

---

**AllowShortFunctionsOnASingleLine** (`ShortFunctionStyle`)

根据取值, `int f() { return 0; }` 可能在单独一行.

可取的值:

- `SFS_None` (在配置中为 `None`) 不将函数合并在一行

- `SFS_InlineOnly` (在配置中为 `InlineOnly`) 只合并在类内定义的函数和`inline`函数,不合并空函数: 举例,顶级空函数也不会被合并

```c++
class Foo {
  void f() { foo(); }
};
void f() {
  foo();
}
void f() {
}
```

- `SFS_Empty` (在配置中为 `Empty`) 只合并空函数

```c++
void f() {}
void f2() {
  bar2();
}
```

- `SFS_Inline` (在配置中为 `Inline`) 只合并类内函数及空函数

```c++
class Foo {
  void f() { foo(); }
};
void f() {
  foo();
}
void f() {}
```

- `SFS_All` (在配置中为 `All`) 将所有函数都合并在一行

```c++
class Foo {
  void f() { foo(); }
};
void f() { bar(); }
```

---

**AllowShortIfStatementsOnASingleLine** (`bool`)

若`true`, `if (a) return;`将在一行

---

**AllowShortLoopsOnASingleLine** (`bool`)

若`true`, `while (true) continue;`将在一行

---

**AlwaysBreakAfterDefinitionReturnType** (`DefinitionReturnTypeBreakingStyle`)

函数声明中返回类型单独一行.该选项是**弃用**的,并保留作为向后兼容.

可取的值:

- `DRTBS_None` (在配置中为 `None`) 返回类型后自动换行,但会受`PenaltyReturnTypeOnItsOwnLine`选项的影响.

- `DRTBS_All` (在配置中为 `All`) 返回类型后总是换行

- `DRTBS_TopLevel` (在配置中为 `TopLevel`)顶层函数的返回类型后总是换行

---

**AlwaysBreakAfterReturnType** (`ReturnTypeBreakingStyle`)

函数定义中返回类型的样式风格

可取的值:

- `RTBS_None` (在配置中为 `None`) 返回类型后自动换行,但会受`PenaltyReturnTypeOnItsOwnLine`选项的影响

```c++
class A {
  int f() { return 0; };
};
int f();
int f() { return 1; }
```

- `RTBS_All` (在配置中为 `All`) 返回类型后总是换行

```c++
class A {
  int
  f() {
    return 0;
  };
};
int
f();
int
f() {
  return 1;
}
```

- `RTBS_TopLevel` (在配置中为 `TopLevel`) 顶层函数的返回类型后总是换行

```c++
class A {
  int f() { return 0; };
};
int
f();
int
f() {
  return 1;
}
```

- `RTBS_AllDefinitions` (在配置中为 `AllDefinitions`) 函数定义中的返回类型后总是换行

```c++
class A {
  int
  f() {
    return 0;
  };
};
int f();
int
f() {
  return 1;
}
```

- `RTBS_TopLevelDefinitions` (在配置中为 `TopLevelDefinitions`) 顶层函数定义中的返回类型后总是换行

```c++
class A {
  int f() { return 0; };
};
int f();
int
f() {
  return 1;
}
```

---

**AlwaysBreakBeforeMultilineStrings** (`bool`)

若`true`,在多行字符串前总是换行

这个选项意味着在代码中有多个多行字符串的情况下视觉更一致.因此,它只在包装字符串时生效,并使其从行首缩进`ContinuationIndentWidth`个空格.

```c++
true:                                  false:
aaaa =                         vs.     aaaa = "bbbb"
    "bbbb"                                    "cccc";
    "cccc";
```

---

**AlwaysBreakTemplateDeclarations** (`bool`)

若`true`,总会在定义模板中的`template<...>`后换行

```c++
true:                                  false:
template <typename T>          vs.     template <typename T> class C {};
class C {};
```

---

**BinPackArguments** (`bool`)

若`false`,调用函数的实参不会在同一行或各自一行.

```c++
true:
void f() {
  f(aaaaaaaaaaaaaaaaaaaa, aaaaaaaaaaaaaaaaaaaa,
    aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa);
}

false:
void f() {
  f(aaaaaaaaaaaaaaaaaaaa,
    aaaaaaaaaaaaaaaaaaaa,
    aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa);
}
```

---

**BinPackParameters** (`bool`)

若`false`,函数定义的形参将不会同一行或各自一行

```c++
true:
void f(int aaaaaaaaaaaaaaaaaaaa, int aaaaaaaaaaaaaaaaaaaa,
       int aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa) {}

false:
void f(int aaaaaaaaaaaaaaaaaaaa,
       int aaaaaaaaaaaaaaaaaaaa,
       int aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa) {}
```

---


