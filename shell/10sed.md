# sed

* 什么是流编辑器 `sed`
* `sed` 地址定位方式
* `sed` 编辑命令

## 什么是流编辑器 `sed`

sed 是一个精简的、非交互式的流式编辑器，它在命令行中输入编辑命令和指定文件名，然后在屏幕上查看输出。

**逐行**读取文件内容存储在临时**缓冲区**，称为`模式空间`, 接着用`sed命令`处理缓冲区中的内容，处理完成后，把缓冲区中的内容送往屏幕。接着处理下一行，这样不断重复，知道文件末尾。原文件内容并没有改变

### sed 的简单用法

```sh
sed [options] sed cmds files
```

其中 `options`:

* -e 链接多个编辑命令
* -f 指定 `sed` 脚本文件名
* -n 阻止输入行自动输出

简单示例：

```sh
$ sed '4,$d' test.txt # 表示将文件中第4行到文件尾的内容删除，显示前3行到屏幕中
$ sed '3q' test.txt # 表示处理到文件中第3行的时候退出，所以也是显示前3行
$ sed 's/pulbic/PUBLIC/' test.txt # 表示将文件中每行的第一个 public 替换成 PUBLIC
$ sed 's/pulbic/PUBLIC/g' test.txt # 表示将文件中所有的 public 替换成 PUBLIC
$ sed -n 's/public/PUBLIC/p' test.in # -n 表示阻止输出，那么就不会显示文件中的任何一行，现在想要输出只包含被替换的行，那么可以在后面跟上 p
$
```

### 指定多个命令的几种方法

* 使用分号

```sh
$ sed 's/sourth/SOURTH/;s/north/NORTH/' test.txt
$
```

* 使用 -e 选项

```sh
$ sed -e 's/sourth/SOURTH/' -e 's/north/NORTH/' test.txt
$
```

* 使用分行命令功能，在输入单引号后按return键就会出现多行提示符>

```sh
$ sed '
$ > s/sourth/SOURTH/
$ > s/north/NORTH/' test.txt
$
```

* 将命令放在文本文件中使用 -f 选项指定

```sh
# command.txt 文件内容
s/sourth/SOURTH/
s/north/NORTH/

# 回到终端
$ sed -f command.txt test.txt
```

### sed 命令语法

`'[address]sed_edit_cmd'`,其中 `address` 为 `sed` 的行定位模式，用于指定将要被 `sed` 编辑的行。如果省略，`sed` 将编辑所有的行。`sed_edit_cmd` 为 `sed` 对被编辑的行将要进行的编辑操作。

#### address 的定位方式

* `n`: 表示第n行
* `$`: 表示最后一行
* `m,n`: 表示第m行到第n行
* `/pattern/`: 查询包含指定模式的行。如/disk/、/[a-z]/
* `/pattern/,n`: 表示包含指定模式的行到第n行
* `n,/pattern/`,: 表示从第n行到包含指定模式的行
* `/pattern1/,/pattern2/`: 表示从包含模式1到模式2的行
* `!`: 表示反向选择，如 m,n! 的结果与 m,n 相反

#### 常用的编辑命令

* p: 打印匹配行

  ```sh
  $ sed -n '3,6p' test.txt # 打印第3行到第6行
  $ sed -n '3,6!p' test.txt # 打印除了第3行到第6行的其它行
  $ sed -n '$p' test.txt # 打印最后一行
  $ sed -n '/north/p' test.txt # 打印包含 north 的行
  $
  ```

* n: 显示匹配的行号

  ```sh
  $ sed -n '/north/=' test.txt # 试试包含 north 的行的行号
  $
  ```

* d: 删除匹配的行

  ```sh
  $ sed '/north/d' test.txt # 删除包含 north 的行
  $
  ```

* a\: 在指定行后面追加一行或多行文本，并显示添加的新内容，该命令主要用于 sed 脚本中。

  ```sh
  $ sed '/north/a\AAA\
  >BBB\
  >CCC' test.txt # 包含north的行后面添加3行，分别为 AAA,BBB,CCC
  ```

* i\: 在指定行前插入一行或多汗，并显示添加的新内容，使用格式同 a\ 相同
* c\: 用新文本替换指定的行，使用格式同 a\相同
* l: 显示指定行中的所有字符，包括控制字符

  ```sh
  $ sed -n '/north/l' test.txt
  $
  ```

* s: 替换命令，使用格式为：`[address]s/old/new/[gpw]`
  * address: 如果省略表示所有行
  * g: 全局替换
  * p: 打印被修改后的行
  * w fname: 将被替换后的行的内容写入到指定文件中

  ```sh
    sed -n 's/north/NORTH/gp' test.txt
    sed -n 's/north/NORTH/w data' test.txt
    sed -n 's/[0-9][0-9]$/&.5/p' test.txt # & 表示要被替换的内容，这里表示将以两个数字结尾的内容替换为最后两个数字.5(就是在最后添加了.5)
  ```

* r: 读文件，将另一个文件中的内容附加到指定行后。

  ```sh
  sed '$r data' test.txt # 将 data 文件的内容追加到 test.txt 文件
  ```

* w: 写文件，将指定行写入到另外一个文件中。

  ```sh
  sed -n '/public/w data' test.txt # 将 test.txt 文件中包含 public 的行写入到 data 文件中
  ```

* n: 将指定行的下面一行读入编辑缓冲区。

  ```sh
  sed -n '/public/{n;s/north/NORTH/p}' test.txt # 将包含public的下一行行放入缓冲区，然后将缓冲区的内容中包含的north替换成NORTH。
  ```
