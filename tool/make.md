# Make 命令

- [`Make` 命令教程](http://www.ruanyifeng.com/blog/2015/02/make.html)
- [使用 `Make` 构建网站](http://www.ruanyifeng.com/blog/2015/03/build-website-with-make.html)


写在一个叫做Makefile的文件中，Make命令依赖这个文件进行构建。Makefile文件也可以写为makefile， 或者用命令行参数指定为其他文件名。

```bash
make -f rules.txt
# 或者
make --file=rules.txt
```


## 规则

Makefile

```
<target> : <prerequisites>
[tab]  <commands>

```

- 第一行冒号前面的部分，叫做"目标"（target），冒号后面的部分叫做"前置条件"（prerequisites）；第二行必须由一个tab键起首，后面跟着"命令"（commands）
- "目标"是必需的，不可省略；"前置条件"和"命令"都是可选的，但是两者之中必须至少存在一个。

### 目标（target）

目标通常是文件名,除了文件名，目标还可以是某个操作的名字，这称为"伪目标"（phony target）[手册](https://www.gnu.org/software/make/manual/html_node/Special-Targets.html#Special-Targets)。

如果Make命令运行时没有指定目标，默认会执行Makefile文件的第一个目标。

```bash
# 代码执行Makefile文件的第一个目标。
make

```

### 前置条件（prerequisites）

前置条件通常是一组文件名，之间用空格分隔

### 命令（commands）

命令（commands）表示如何更新目标文件，由一行或多行的Shell命令组成。它是构建"目标"的具体指令，它的运行结果通常就是生成目标文件。

每行命令在一个单独的shell中执行。这些Shell之间没有继承关系.

```bash
# 一个解决办法是将两行命令写在一行，中间用分号分隔。
var-kept:
    export foo=bar; echo "foo=[$$foo]"

# 另一个解决办法是在换行符前加反斜杠转义。
var-kept:
    export foo=bar; \
    echo "foo=[$$foo]"

# 最后一个方法是加上.ONESHELL:命令。
.ONESHELL:
var-kept:
    export foo=bar; 
    echo "foo=[$$foo]"

```


## 语法
### 注释

```bash
# 这是注释
result.txt: source.txt
    # 这是注释
    cp source.txt result.txt # 这也是注释
```

### 回声（echoing）

正常情况下，make会打印每条命令，然后再执行，这就叫做回声（echoing）。
在命令的前面加上@，就可以关闭回声。
由于在构建过程中，需要了解当前在执行哪条命令，所以通常只在注释和纯显示的echo命令前面加上@。

```bash
test:
    @# 这是测试
    @echo TODO
```

### 通配符

Makefile 的通配符与 Bash 一致，主要有星号（*）、问号（？）和 [...] 。比如， *.o 表示所有后缀名为o的文件。

```bash
clean:
        rm -f *.o
```

### 模式匹配

Make命令允许对文件名，进行类似正则运算的匹配，主要用到的匹配符是%。
```bash
%.o: %.c
# 等同于下面的写法。

f1.o: f1.c
f2.o: f2.c
```
使用匹配符%，可以将大量同类型的文件，只用一条规则就完成构建。

### 变量和赋值符

调用时，变量需要放在 $( ) 或者 ${ }之中。

调用Shell变量，需要在美元符号前，再加一个美元符号，这是因为Make命令会对美元符号转义。

```bash
test:
    @echo $$HOME

```

```bash

ENV ?= default
REGION ?= D

dev:
	@echo ${ENV}
	@echo ${REGION}

```

```bash

# default
# D
make dev

# dev
# A
make ENV=dev REGION=A dev
```

```bash
四个赋值运算符 （=、:=、？=、+=），它们的区别请看StackOverflow。


VARIABLE = value
# 在执行时扩展，允许递归扩展。

VARIABLE := value
# 在定义时扩展。

VARIABLE ?= value
# 只有在该变量为空时才设置值。

VARIABLE += value
# 将值追加到变量的尾端。
```

### 内置变量（Implicit Variables）

[手册](https://www.gnu.org/software/make/manual/html_node/Implicit-Variables.html)

### 自动变量（Automatic Variables）

- `$@` 指代当前目标，就是Make命令当前构建的那个目标。比如，make foo的 $@ 就指代foo。
- `$< `指代第一个前置条件。比如，规则为 t: p1 p2，那么$< 就指代p1。
- `$?` 指代比目标更新的所有前置条件，之间以空格分隔。比如，规则为 t: p1 p2，其中 p2 的时间戳比 t 新，$?就指代p2。
- `$^` 指代所有前置条件，之间以空格分隔。比如，规则为 t: p1 p2，那么 $^ 就指代 p1 p2 。
- `$*` 指代匹配符 % 匹配的部分， 比如% 匹配 f1.txt 中的f1 ，$* 就表示 f1。
- `$(@D)` 和 `$(@F)` 分别指向 $@ 的目录名和文件名。比如，$@是 src/input.c，那么$(@D) 的值为 src ，$(@F) 的值为 input.c。
- `$(<D)` 和 `$(<F)` 分别指向 $< 的目录名和文件名。

### 判断和循环

Makefile使用 Bash 语法，完成判断和循环。

### 函数

[内置函数](https://www.gnu.org/software/make/manual/html_node/Functions.html)

- shell 函数
  - `srcfiles := $(shell echo src/{00..99}.txt)`
- wildcard 函数
  - `srcfiles := $(wildcard src/*.txt)`
- subst 函数
- patsubst函数
- 替换后缀名


### include
```
include Makefile.include.mk

```