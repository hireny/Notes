# 主页

Julia 1.5 中文文档
欢迎来到 Julia 1.5 中文文档(PDF版本)!

请先阅读 Julia 1.0 正式发布博文 以获得对这门语言的总体概观。我们推荐刚刚开始学习 Julia 语言的朋友阅读中文社区提供的 Julia入门指引，也推荐你在中文论坛对遇到的问题进行提问。

> 关于中文文档 <br>
> Julia 语言相关的本地化工作是一个由社区驱动的开源项目 JuliaZH.jl，旨在方便 Julia 的中文用户。我们目前使用 Transifex 作为翻译平台。翻译工作正在进行，有任何疑问或建议请到社区论坛文档区反馈。若有意参与翻译工作，请参考翻译指南。
> 


## 简介

科学计算对性能一直有着最高的需求，但目前各领域的专家却大量使用较慢的动态语言来开展他们的日常工作。 偏爱动态语言有很多很好的理由，因此我们不会舍弃动态的特性。 幸运的是，现代编程语言设计与编译器技术可以大大消除性能折衷（trade-off），并提供有足够生产力的单一环境进行原型设计，而且能高效地部署性能密集型应用程序。 Julia 语言在这其中扮演了这样一个角色：它是一门灵活的动态语言，适合用于科学计算和数值计算，并且性能可与传统的静态类型语言媲美。

由于 Julia 的编译器和其它语言比如 Python 或 R 的解释器有所不同，一开始你可能发现 Julia 的性能并不是很突出。 如果你觉得速度有点慢，我们强烈建议在尝试其他功能前，先读一读文档中的提高性能的窍门。 在理解了 Julia 的运作方式后，写出和 C 一样快的代码对你而言就是小菜一碟。

Julia 拥有可选类型标注和多重派发这两个特性，同时还拥有很棒的性能。 这些都得归功于使用 LLVM 实现的类型推导和即时编译（JIT）技术。Julia 是一门支持过程式、函数式和面向对象的多范式语言。 它像 R、MATLAB 和 Python 一样简单，在高级数值计算方面有丰富的表现力，并且支持通用编程。 为了实现这个目标，Julia 以数学编程语言（mathematical programming languages）为基础，同时也参考了不少流行的动态语言，例如 Lisp、Perl、Python、Lua、和 Ruby。

Julia 与传统动态语言最重要的区别是：

- 核心语言很小：标准库是用 Julia 自身写的，包括整数运算这样的基础运算
- 丰富的基础类型：既可用于定义和描述对象，也可用于做可选的类型标注
- 通过多重派发，可以根据类型的不同，来调用同名函数的不同实现
- 为不同的参数类型，自动生成高效、专用的代码
- 接近 C 语言的性能

尽管人们有时会说动态语言是“无类型的”，但实际上绝对不是这样的：每一个对象都有一个类型，无论它是基础的类型（primitive）还是用户自定义的类型。 大多数的动态语言都缺乏类型声明，这意味着程序员无法告诉编译器值的类型，也就无法显式地讨论类型。 另一方面，在静态语言中，往往必须标注对象的类型。但类型只在编译期才存在，而无法在运行时进行操作和表达。 而在 Julia 中，类型本身是运行时的对象，并可用于向编译器传达信息。

类型系统和多重派发是 Julia 语言最主要的特征，但一般不需要显式地手动标注或使用：函数通过函数名称和不同类型参数的组合进行定义，在调用时会派发到最接近（most specific）的定义上去。这样的编程模型非常适合数学化的编程，尤其是在传统的面向对象派发中，一些函数的第一个变量理论上并不“拥有”这样一个操作时。 在 Julia 中运算符只是函数的一个特殊标记——例如，为用户定义的新类型添加加法运算，你只要为 + 函数定义一个新的方法就可以了。 已有的代码就可以无缝接入这个新的类型。

Julia 在设计之初就非常看重性能，再加上它的动态类型推导（可以被可选的类型标注增强），使得 Julia 的计算性能超过了其它的动态语言，甚至能够与静态编译语言竞争。对于大型数值问题，速度一直都是，也一直会是一个重要的关注点：在过去的几十年里，需要处理的数据量很容易与摩尔定律保持同步。

Julia 的目标是创建一个前所未有的集易用、强大、高效于一体的语言。除此之外，Julia 还拥有以下优势：

- 采用 MIT 许可证：免费又开源
- 用户自定义类型的速度与兼容性和内建类型一样好
- 无需特意编写向量化的代码：非向量化的代码就很快
- 为并行计算和分布式计算设计
- 轻量级的“绿色”线程：协程
- 低调又牛逼的类型系统
- 优雅、可扩展的类型转换和类型提升
- 对 Unicode 的有效支持，包括但不限于 UTF-8
- 直接调用 C 函数，无需封装或调用特别的 API
- 像 Shell 一样强大的管理其他进程的能力
- 像 Lisp 一样的宏和其他元编程工具

# 手册

## 入门

无论是使用预编译好的二进制程序，还是自己从源码编译，安装 Julia 都是一件很简单的事情。 请按照 https://julialang.org/downloads/ 的提示来下载并安装 Julia。

启动一个交互式会话（也叫 REPL）是学习和尝试 Julia 最简单的方法。双击 Julia 的可执行文件或是从命令行运行 julia 就可以启动：

```julia
$ julia

               _
   _       _ _(_)_     |  Documentation: https://docs.julialang.org
  (_)     | (_) (_)    |
   _ _   _| |_  __ _   |  Type "?" for help, "]?" for Pkg help.
  | | | | | | |/ _` |  |
  | | |_| | | | (_| |  |  Version 1.3.1 (2019-12-30)
 _/ |\__'_|_|_|\__'_|  |  Official https://julialang.org/ release
|__/                   |


julia> 1 + 2
3

julia> ans
3
```

输入 CTRL-D（同时按 Ctrl 键和 d 键）或 exit() 便可以退出交互式会话。在交互式模式中，julia 会显示一条横幅并提示用户输入。一旦用户输入了一段完整的代码（表达式），例如 1 + 2，然后按回车，交互式会话就会执行这段代码，并将结果显示出来。如果输入的代码以分号结尾，那么结果将不会显示出来。然而不管结果显示与否，变量 ans 总会存储上一次执行代码的结果，需要注意的是，变量 ans 只在交互式会话中才有。

在交互式会话中，要运行写在源文件 file.jl 中的代码，只需输入 include("file.jl")。

如果想非交互式地执行文件中的代码，可以把文件名作为 julia 命令的第一个参数：

```julia
$ julia script.jl arg1 arg2...
```

如这个例子所示，julia 后跟着的命令行参数会被作为程序 script.jl 的命令行参数。这些参数使用全局常量 ARGS 来传递，脚本自身的名字会以全局变量 PROGRAM_FILE 传入。注意当脚本以命令行里的 -e 选项输入时，ARGS 也会被设定（详见此页末尾列表）但是 PROGRAM_FILE 会是空的。例如，要把一个脚本的输入参数显示出来，你可以：

```julia
$ julia -e 'println(PROGRAM_FILE); for x in ARGS; println(x); end' foo bar

foo
bar
```

或者你可以把代码写到一个脚本文件中再执行它：

```julia
$ echo 'println(PROGRAM_FILE); for x in ARGS; println(x); end' > script.jl
$ julia script.jl foo bar
script.jl
foo
bar
```

可以使用 -- 分隔符来将传给脚本文件的参数和 Julia 本身的命令行参数区分开：

```julia
$ julia --color=yes -O -- foo.jl arg1 arg2..
```

有关编写 Julia 脚本的更多信息，请参阅 脚本。

使用选项 -p 或者 --machine-file 可以在并行模式下启动 Julia。 -p n 会启动额外的 n 个 worker，使用 --machine-file file 会为 file 文件中的每一行启动一个 worker。 定义在 file 中的机器必须能够通过一个不需要密码的 ssh 登陆访问到，且 Julia 的安装位置需要和当前主机相同。 定义机器的格式为 [count*][user@]host[:port] [bind_addr[:port]]。 user 默认值是当前用户； port 默认值是标准 ssh 端口； count 是在这个节点上的 worker 的数量，默认是 1； 可选的 bind-to bind_addr[:port] 指定了其它 worker 访问当前 worker 应当使用的 IP 地址与端口。

要让 Julia 每次启动都自动执行一些代码，你可以把它们放在 ~/.julia/config/startup.jl 中：

```julia
$ echo 'println("Greetings! 你好! 안녕하세요?")' > ~/.julia/config/startup.jl
$ julia
Greetings! 你好! 안녕하세요?

...
```

在你第一次运行 `Julia` 后，你一你应该多了一个 `~/.julia` 文件夹。 你还可以新建 `~/.julia/config` 文件夹和 `~/.julia/config/startup.jl` 文件来配置 `Julia`。

和 `perl` 和 `ruby` 程序类似，还有很多种运行 `Julia` 代码的方式，运行代码时也有很多选项：

```julia
julia [switches] -- [programfile] [args...]
```

选项 |	描述
:---|:---
`-v`, `--version`|	显示版本信息
`-h`, `--help`|	显示命令行参数
`--project[={<dir>|@.}]`|	将 `<dir>` 设置为主项目/环境。默认的 @. 选项将搜索父目录，直至找到 `Project.toml` 或 `JuliaProject.toml` 文件。
`-J`, `--sysimage <file>` |	用指定的系统镜像文件（`system image file`）启动
`-H`, `--home <dir>`|	设置 `julia` 可执行文件的路径
`--startup-file={yes|no}`|	是否载入 `~/.julia/config/startup.jl`
--handle-signals={yes|no}|	开启或关闭 Julia 默认的 signal handlers
--sysimage-native-code={yes|no}|	在可能的情况下，使用系统镜像里的原生代码
--compiled-modules={yes|no}|	开启或关闭 module 的增量预编译功能
-e, --eval <expr>|	执行 <expr>
-E, --print <expr>|	执行 <expr> 并显示结果
-L, --load <file>|	立即在所有进程中载入 <file>
-t, --threads {N|auto}|	开启 N 个线程：auto 将 N 设置为当前 CPU 线程数，但这个行为可能在以后版本有所变动。
-p, --procs {N|auto}|	这里的整数 N 表示启动 N 个额外的工作进程；auto 表示启动与 CPU 线程数目（logical cores）一样多的进程
--machine-file <file>|	在 <file> 中列出的主机上运行进程
-i|	交互式模式；REPL 运行且 isinteractive() 为 true
-q, --quiet|	安静的启动；REPL 启动时无横幅，不显示警告
--banner={yes|no|auto}|	开启或关闭 REPL 横幅
--color={yes|no|auto}|	开启或关闭文字颜色
--history-file={yes|no}|	载入或导出历史记录
--depwarn={yes|no|error}|	开启或关闭语法弃用警告，error 表示将弃用警告转换为错误。
--warn-overwrite={yes|no}|	开启或关闭“method overwrite”警告
-C, --cpu-target <target>|	设置 <target> 来限制使用 CPU 的某些特性；设置为 help 可以查看可用的选项
-O, --optimize={0,1,2,3}|	设置编译器优化级别(若未配置此选项，则默认等级为2；若配置了此选项却没指定具体级别，则默认级别为3)。
-g, -g <level>|	开启或设置 debug 信息的生成等级。若未配置此选项，则默认 debug 信息的级别为 1；若配置了此选项却没指定具体级别，则默认级别为 2。
--inline={yes|no}|	控制是否允许函数内联，此选项会覆盖源文件中的 @inline 声明
--check-bounds={yes|no}|	设置边界检查状态：始终检查或永不检查。永不检查时会忽略源文件中的相应声明
--math-mode={ieee,fast}|	开启或关闭非安全的浮点数代数计算优化，此选项会覆盖源文件中的 @fastmath 声明
--code-coverage={none|user|all}|	对源文件中每行代码执行的次数计数
--code-coverage|	等价于 --code-coverage=user
--track-allocation={none|user|all}|	对源文件中每行代码的内存分配计数，单位 byte
--track-allocation|	等价于 --track-allocation=user

> Julia 1.1 <br>
> 在 Julia 1.0 中，默认的 --project=@. 选项不会在 Git 仓库的根目录中寻找 Project.toml 文件。从 Julia 1.1 开始，此选项会在其中寻找该文件。

### 资源
除了本手册以外，官方网站还提供了一个有用的学习资源列表来帮助新用户学习 Julia。

