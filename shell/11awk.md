# awk

* 什么是awk
* awk的简单用法
* awk的脚本语法
* awk的执行过程

## 什么是awk

* awk 是一种用于处理数据和生成报告的编程语言
* awk 可以在命令行中进行一些简单的操作，也可以被写成脚本来处理较大的问题
* awk 与 grep、sed 结合使用，将使 shell 编程更加容易
* linux 下使用的 awk 是 gawk
* awk 逐行扫描输入(可以是文件或管道等), 按给定的模式查找出匹配的行，然后对这些行执行 awk 命令指定的操作
* 与 sed 一样，awk 不会修改输入文件的内容。(可以使用重定向将 awk 的输出保存到文件中)

## awk 的简单用法

```sh
awk [options] sed_script files
```

### options

* -F: 指定输入记录字段的分隔符，默认使用环境变量IFS的值
* -f: 从指定文件读取 awk_script
* -v: 为 awk 设置变量

```sh
$ awk -F: '{ print $1 }' /etc/passwd # 打印第1列
$ awk -F: '{ print $1, $3 }' /etc/passwd # 打印第1列和第3列,默认按空格分隔
$ awk -F: '{ print $1 "|" $3 }' /etc/passwd # 打印第1列和第3列，指定用 | 进行分隔
$ awk -F: '/root/{ print $1 "|" $3 }' /etc/passwd # 打印包含root的行的第1列和第3列
$ awk -F: '/^root/' /etc/passwd # 打印一root开头的行的第1列和第3列
$
```

## awk_script 语法

```sh
awk 'BEGIN {actions}
    /pattern1/{actions}
    ...
    /patternN/{actions}
    END {actions}' input_file
```

其中 `BEGIN` 和 `END` 都是大写字母。 `BEGIN{actions}`  和 `END{actions}` 是可选的

## awk 的执行过程

1. 如果存在 `BEGIN`，`awk` 首先执行它指定的 `actions`
2. `awk` 从输入中读取一行，称为一条输入记录
3. `awk` 将读入的记录分割成多个字段，并将第一个字段放入变量 `$1` 中，第二个放入变量 `$2` 中，以此类推。`$0` 表示整条记录。
4. 把当前输入记录一次与每一个 `awk_cmd` 中 `pattern` 比较:
    * 如果匹配，就执行对应的 `actions`
    * 如果不匹配，就跳过对应的 `actions`，直到完成所有的 `awk_cmd`

5. 当一条输入记录处理完毕后，`awk` 读取输入的下一行，重复上面的处理过程，直到所有的输入全部处理完毕。
6. `awk` 处理玩所有的输入后，若存在END，执行相应的 `actions`
7. 如果输入是文件列表，`awk` 将按顺序处理列表中的每个文件

```sh
$ ifconfig | awk '/inet addr/{ print $2 }' | awk -F: '{ print $2 }' # 列出本机ip地址
$ ifconfig | awk '/inet addr/{ print $2 }' | awk -F: 'BEGIN { print "begin..." } { print $2 } END { print "end..."}' # 列出本机ip地址
$
```

## 模式匹配

使用布尔表达式

   1. 表达是中可以使用变量 (如字段变量 `$1`, `$2`)
   2. 表达式中的运算符有
        * 关系运算符：< > <= >= == !=
        * 匹配运算符：~ !~
        x ~ /rexp/ 如果 x 匹配 /rexp/,则返回真
        x !~ /rexp/ 如果 x 不匹配 /rexp/,则返回真
        * 复合表达式：&& || !

```sh
# 创建测试文件 test.txt,内容如下
hello, world
# this is annotation
11, 22, 33
44, 55, 66
77, 88, 99

# 回到终端
$ awk '$1 > 20 {print $0}' test.txt # 输出第1列大于20的行，其中会输出 hello, world, 因为 hello，也算是大于20
$ awk '$1 ~ /^#/ { print $0 }' test.txt # 匹配第1列中以 # 号开头的行，将其输出
$ awk '($1 > 20) && ($2~/^5/){print $0}' # 匹配第1列大于20且第2列以5开头的，将其输出
```

## 字段分隔符、重定向和管道

### 字段分隔符

`awk` 中的字段分隔符可以用 -F 选项指定，缺省是空格(实际上是由IFS变量决定的)

```sh
$ awk '$1 > 20 {print $0}' /etc/passwd
$ awk -F: '$1 > 20 {print $0}' /etc/passwd
$ awk -F' [ :]' '{print $1}' /etc/passwd # 也可以使用空格或则:号进行分隔
$
```

### 重定向和管道

```sh
awk '{print $1, $2 > "output"}' test.txt # 输出 test.txt 中的第1列第2列重定向到output文件中
awk 'BEGIN{"cal" | getline a; print a}' # cal 是日历，获取日历的第1行赋值给a，打印出来
```
