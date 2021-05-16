---
title: Shell 基础语法
date: 2021/05/09
categories:
  - 编程语言
  - Shell
tags:
  - Shell
copyright: true
---

## 前言

本文只是对 shell 脚本语言中一些常用的基础语法进行汇总整理，如果你真的想系统的学习 shell 脚本编程，这里推荐两本电子读物：
1. [The Linux Command Line-中文版][10]
2. [Advanced Bash-Scripting Guide-中文版][9]

第一本相对来说更加平滑，适合零基础的人进行自学；
第二本相对来说更加全面，适合有一点基础的人进行自学；

另外，强烈建议你在学习了一定 shell 语法基础之后去找一个比较完善的编码规范进行阅读并严格按照规范进行脚本编辑，这里我个人推荐 [Google Shell 风格指南][5]。

无论是大的项目脚本还是小的工具脚本，严格的按照一个成熟的编码规范进行编辑能够帮助我们（在前期）更好的规划脚本以及（在后期）更快的 DEBUG。

**注：本文中所有测试代码均为 zsh 输出结果**
**注2：本文中所有测试代码均以 [Google Shell 风格指南][5] 作为编码规范**

## 变量相关

### 变量命名

1. 定义变量时不需要加美元符号（$）；
2. 变量名和等号之间不能有空格（分隔符）；
3. 命名只能使用英文、数字和下划线且首个字符不能是数字；
4. 不能使用关键字，可以使用 `bash -c help` 命令查看关键字；

### 使用变量

使用一个已经定义的变量只需要在变量名前加美元符号（$）即可，变量名两边的花括号（{}）可加可不加。

```shell
test_name="test"
echo $test_name
echo ${test_name}
```

注意无论您是否选择在变量名两边加入花括号请保持上下文编码规范的一致性。

### 只读变量

使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。

### 删除变量

可以使用 unset 命令删除变量，被删除的变量不能再次使用，unset 不能删除只读变量。

### 变量类型

shell 中存在三类变量：
1. 局部变量：在脚本或命令中定义的变量，仅在当前 shell 实例中有效，其他 shell 启动的程序不能访问局部变量；
2. 环境变量：所有程序（包括 shell 启动的程序）都能访问的变量，有些程序需要环境变量来保证正常运行；
3. shell 变量：由 shell 程序设定的特殊变量，有一部分是环境变量、一部分是局部变量，这些变量保证了 shell 程序的正常运行；

![shell 环境变量-引用自知乎@小晶][4]

## 数组相关

```shell
# 定义格式：array_name=(value1 value2 ... valuen)
a=1
my_array=("test" 2 "abc" ${a})
echo ${my_array[0]} # test
echo ${my_array[1]} # 2
echo ${my_array[2]} # abc
echo ${my_array[3]} # 1
echo ${my_array[*]} # test 2 abc 1 输出数组全部元素
echo ${my_array[@]} # test 2 abc 1 输出数组全部元素
echo ${#my_array}   # 4 输出数组长度

# 遍历数组
for i in ${my_array[*]}; do
  echo ${i}
done

for i in ${my_array[@]}; do
  echo ${i}
done

for (( i = 0; i < ${#my_array}; i++ )) do
  echo ${my_array[i]}
done
```

**注：这里仅罗列了集中常见用法，如果想要了解更多数组操作可以查看 [余子越：shell数组与字典总结][11]；**
**注2：该博文给出的特性本人并未全部测试，请先测试确定有效后再使用；**

## 字典相关

**本地测试失败，暂不整理。**

## 字符串相关

### 字符串变量定义时的单引号和双引号

```shell
first_name="test"
last_name1="test1 ${first_name}"
last_name2='test1 ${first_name}'

echo ${last_name1}  # test1 test
echo ${last_name2}  # test1 ${first_name}
```

在定义 shell 字符串变量时如果使用单引号包裹变量内容，则被包裹的部分会原样输出，如果使用双引号包裹变量内容，则在输出变量内容时会先解析变量内的变量、命令和转义字符。

### 字符串拼接

```shell
your_name="test"

# 单引号拼接
welcome1='hello, ${your_name} !'
welcome2='hello, '${your_name}' !'
echo ${welcome1} # hello, ${your_name} !
echo ${welcome2} # hello, test !
# 双引号拼接
welcome3="hello, ${your_name} !"
welcome4="hello, "${your_name}" !"
echo ${welcome3} # hello, test !
echo ${welcome4} # hello, test !
```

以上句式中：
welcome1 属于单引号字符串无法解析变量，
welcome2 属于字符串拼接，
welcome3 属于双引号解析字符串变量，
welcome4 属于字符串拼接。

### 字符串操作

```shell
string="abcdefghijklmn"
string2="这是一段中文字符串"
# 输出字符串长度
echo ${#string}     # 14
echo ${#string1}    # 0
# 截取字符串，从下表 1 截取到下表 4
echo ${string:1:4}  # bcde
echo ${string1:1:4} # 无输出
echo ${string1}     # 无输出
# 在我的测试系统环境下无法对中文字符串进行操作
# 中文字符串操作具体逻辑我没有详细查询，感兴趣可以自己查询一下，估计是字符集的问题
```

## 基础语法相关

### 特殊变量

```shell
# ${n}：$0 表示命令本身，$1-$9 代表第 1 到第 9 个参数，10 以上加花括号，个人建议全部加花括号
# ${*}：运行当前脚本时的所有参数，且把所有参数看做一个整体
# ${@}：运行当前脚本时的所有参数，且把每个参数区别对待
# ${#}：运行当前脚本时的所有参数个数
# ${$}：当前进程 PID
# ${!}：在当前脚本中后台运行的最后一个进程 PID
# ${?}：最后一次执行的命令返回状态，0 为执行正确，非 0 为执行错误
# 测试文件名称：test.sh
# 测试命令 ./test.sh 1 2 3 4 5 6 7 8 9
# 同目录下有一个输出 hello, world 的 hello.sh 文件

echo $(dirname ${0}) # .
echo ${0}            # ./test.sh
echo ${1}            # 1
echo ${2}            # 2
echo ${6}            # 6
echo ${99}           # 无输出
echo ${*}            # 1 2 3 4 5 6 7 8 9
echo ${@}            # 1 2 3 4 5 6 7 8 9
echo ${#}            # 9
echo ${$}            # 70149
./hello.sh           # 注意：这里不会立即输出结果
echo ${!}            # 70151
echo ${?}            # 0
                     # hello.sh 的输出结果 -> hello, world
```

### 特殊字符

#### # | 井号

注释符。如果一行脚本的开头是#（除了#!），那么代表这一行是注释，不会被执行。

#### ; | 分号

命令分隔符。允许在同一行内放置两条或更多的命令。

#### : | 冒号

空命令。它在 shell 中等价于 "NOP"（即no op，空操作）与 shell 内建命令 true 有同样的效果。它本身也是 Bash 的内建命令之一，返回值是 true（0）。

如果你学过 Python 的话，它很像 Python 中的 pass（个人感觉）。

#### () | 单组小括号

在**新的子 shell （环境）里**执行使用分号（;）隔开的一组命令，且最后一个命令**可以不用分号**。

```shell
# 测试文件路径 /root/test.sh

pwd            # /root
(cd /etc;pwd)  # /etc
pwd            # /root
```

#### {} | 花括号

在**当前 shell （环境）里**执行使用分号（;）隔开的一组命令，**最后一个命令也需要加分号**，且**左括号和第一个命令之间必须有空格（分隔符）**。

> 代码块，又被称作内联组（inline group）。
> 它实际上创建了一个匿名函数（anonymous function），即没有名字的函数。
> 但是，不同于那些“标准”函数，代码块内的变量在脚本的其他部分仍旧是可见的。
> 
> [Advanced Bash-Scripting Guide-中文版][9]

```shell
# 测试文件路径 /root/test.sh

pwd                # /root
{ cd /etc; pwd; }  # /etc
pwd                # /etc

# 注意 { cd /etc; pwd; } 命令中，除了 { 和 c 之间 以及 cd 和 / 之间的空格是必须的其他空格都可省略
``` 

#### $() 和 ``

二者都可用于「命令替换」，所谓命令替换与前面变量章节提到的变量替换类似，即完成区域内部的命令并将结果替换出来和当前命令行重组。

```shell
echo "today is $(date +%Y-%m-%d)" # today is 2021-05-15
echo "today is `date +%Y-%m-%d`"  # today is 2021-05-15

# 执行逻辑是：先运行命令 date +%Y-%m-%d 然后将结果和原先命令所在的位置进行替换，程序执行时实际运行的是替换后的语句
```

比较推荐使用 $() 这种形式，理由有：
1. 反引号（``）容易与单引号（''）混淆；
2. 在复合命令替换中，使用反引号（``）需要进行额外的转义

```shell
a=`command1 \`command2\` `
b=$(command1 $(command2))
```

但是 $() 不能支持全部 shell（但 bash 中是可以用的），而反引号（``）基本上可以在所有 unix shell 中使用。

#### (()) 和 $(()) | 双组小括号

(()) 的特性：与 let 命令类似，允许对算术表达式的扩展和求值，是 let 命令的简化形式。

(()) 单独使用时的一些作用：
```shell
a=3
(( b = 10 + a ))  # C 语言风格变量赋值， = 前后都有空格
echo ${b}         # 13
(( b-- ))
echo ${b}         # 12
(( b++ ))
echo ${b}         # 13
(( ++b ))
echo ${b}         # 14
(( --b ))
echo ${b}         # 13

(( t = b < 45 ? 7 : 11 ))

echo ${t}         # 7
```

(()) 配合其他语句使用时的一些例子：
```shell
for (( i = 0; i < 5; i++)); do
  echo ${i}    # 依次输出 0 1 2 3 4
done

if (( 1 + 2 )); then
  echo "true"  # true
fi

if !(( 1 - 1 )); then
  echo "false" # false
fi

if (( 3 > 2 )); then
  echo "3 > 2" # 3 > 2 在 if 语句下可以使用双圆括号直接进行数值比较
fi
```

$(()) 的作用：整数运算（不支持浮点数）
```shell
# $(()) 中支持 + - * / % & | ^ !
# 此外，$(()) 还可用作不同进制的运算，但只能输出十进制结果
a=1
b=2
c=3
echo $(( a + b ))         # 3
echo $(( c - a ))         # 2
echo $(( a * c ))         # 3
echo $(( c / a ))         # 3
echo $(( c / b ))         # 1 可见这里的取整方式是直接抛弃小数点后面的数
echo $(( c % a ))         # 0
echo $(( (a + b) * c ))   # 9
echo $(( a & b ))         # 0
echo $(( a | b ))         # 3
echo $(( a ^ b ))         # 3
echo $(( !a ))            # 0
echo $(( 16#2a ))         # 42 这里是将 16 进制的 2a 转化成了十进制的 42
```

#### [] | 单组中括号

在 shell 中 `[ expr ]` **（注意：左右中括号和命令之间都有空格）**等同于命令 `test expr`，主要用于：数值判断、文件判断和字符串判断。

test 和 [] 中可用的比较运算符只有 = 和 !=，要比较大小则只能使用 `test 3 -eq 4` 或 `[ 3 -eq 4 ]` 这种形式

1. 数值判断

| 参数 | 功能 |
| :--: | :--: |
| A -eq B | 判断 A 是否等于 B | 
| A -ne B | 判断 A 是否不等于 B | 
| A -gt B | 判断 A 是否大于 B |
| A -ge B | 判断 A 是否大于等于 B |
| A -lt B | 判断 A 是否小于 B |
| A -le B | 判断 A 是否小于等于 B |

**注：A 和 B 为任意数值或数值变量**

2. 文件判断

| 参数 | 功能 |
| :--: | :--: |
| -e filename | 判断文件是否存在 | 
| -r filename | 判断文件是否可读 | 
| -w filename | 判断文件是否可写 |
| -x filename | 判断文件是否可执行 |
| -s filename | 判断文件是否存在且至少有一个字符 |
| -d filename | 判断文件是否存在且为目录文件 |
| -f filename | 判断文件是否存在且为普通文件 |
| -c filename | 判断文件是否存在且为字符型特殊文件 |
| -b filename | 判断文件是否存在且为块特殊文件 |

**注：filename 为完整（但可以是相对也可以是绝对）文件路径或文件路径变量**

1. 字符串判断

| 参数 | 功能 |
| :--: | :--: |
| stringA = stringB | 判断 stringA 是否等于 stringB | 
| stringA == stringB | 判断 stringA 是否等于 stringB | 
| stringA != stringB | 判断 stringA 是否不等于 stringB |
| -z stringA | 判断 stringA 长度是否为零 |
| -n stringA | 判断 stringA 长度是否不为零 |

**注：stringA 和 stringB 代表任意字符串或字符串变量**

#### [[]] | 双组中括号

[[]] 并非 shell 通用关键字，但大多数常用 shell 都支持，与 [] 相比，[[]] 更加常用。

[[]] 支持字符串的模式匹配，使用 == 操作符时还支持 shell 的正则表达式，字符串在比较的时候可以将等号右边的内容当做一个正则表达式的匹配模式，而不仅仅是一个字符串，例如 `[[ hello == hell? ]]` 的结果为 true，[[]] 中匹配字符串或通配符不需要引号。

在 if 语句中使用 [[]] 可以直接在判断语句中使用 && || 等操作符，但在 [] 中会报错，例如 `if [[ ${a} == 1 && ${a} != 2 ]]`，如果使用 [] 则需写成 `if [ ${a} -ne 1 ] && [ ${a} != 2 ]` 或者 `if [ ${a} -ne 1 -a ${a} != 2]`。

**注：在 [[]] 中使用 < > 是基于字符串进行判断的，例如 `[[ 321 > 1234 ]]` 输出为真，因为按照字符串来判断，3 大于 1**


在支持 [[]] 的 shell 中会把它内部的表达式当做一个单独的元素，并返回一个退出状态码（0 为真，1 为假）。 

### 用户输入

#### 基本读取

```shell
#!/bin/bash
echo -n "Enter your name: " # -n 的作用是不换行
read name
echo "hello ${name}, welcome to my program"
exit 0
```

#### read 参数

| 参数 | 说明 |
| :--: | :--: |
| -a | 后面跟一个变量，该变量会被认为是一个数组，通过 read 给其赋值，默认以空格为分隔符 |
| -d | 后面跟一个标志符，作为结束的标志 |
| -p | 后面根提示信息，在输入前打印提示信息 |
| -e | 输入的时候打开自动补全功能 |
| -n | 后面跟一个数字，定义输入文本长度 |
| -r | 屏蔽 \，如果没有该选项 \ 会被认为是转义符，如果有的话会被认为是正常字符 |
| -s | 安静模式，输入字符时不在屏幕上显示，常用于密码输入 |
| -t | 后面跟秒数，定义输入字符的等待时间 |
| -u | 后面跟 fd，从文件描述符中读入 |

## 函数语法相关

```shell
# 基础语法格式
function function_name() {
    command(s)...
    [ return value ] # 用 [] 框起来表示此语句可选
}

# 函数返回值，可以显式增加 return 语句；
# 如果不加，会将最后一条命令运行结果作为返回值。
# 函数在定义时无需在 function_name() 的 () 中写入具体参数，而是通过 ${1} ${2} 的形式对参数进行调用

name param1 param2 param3
```

## 流程控制相关

### if 语句

```shell
# 基本语法格式
if [ condition ]; then   
  command(s)...
elif [ condition ]; then 
  command(s)...
else
  command(s)...
fi
```

```shell
if [ ${1} -ge 60 ]; then
  echo "及格"
elif [ ${1} -lt 60 ]; then
  echo "不及格" 
fi
```

### case 语句

```shell
# 基本语法格式
case ${variable} in
  "condition1")
    command...
    ;;
  "condition1")
    command...
    ;;

...

  *)
    command...
    ;;
esac
```

```shell
# 实例
case ${1} in
  "1")
    echo 周一
    ;;
  "2")
    echo 周二
    ;;
  *)
    echo 其它
    ;;
esac

# 简写
case ${1} in
  "1") echo 周一 ;;
  "2") echo 周二 ;;
  *) echo 其它 ;;
esac
```

## 循环语法相关

循环是当循环控制条件为真时，一系列命令迭代执行的代码块。

### for 循环

```shell
my_array=("test" 2 "abc" ${a})

for i in ${my_array[*]}; do
  echo ${i}
done

for (( i = 0; i < ${#my_array}; i++ )) do
  echo ${my_array[i]}
done

for i in "test" 2 "abc" ${a}; do
  echo ${i}
done
```

### while 循环

while 循环结构会在循环顶部检测循环条件，若循环条件为真（退出状态为 0）则循环持续进行。
与 for 循环 不同的是，while 循环是在不知道循环次数的情况下使用的。

```shell
sum=0
i=0

while [[ ${i} -le 100 ]]; do
  sum=$(( sum + i ))
  (( i++ ))
done

echo ${sum} # 5050
```

### util 循环

与 while 循环相反，until 循环测试其顶部的循环条件，直到其中的条件为真时停止。

```shell
sum=0
i=0

until [[ ${i} -gt 100 ]]; do
  sum=$(( sum + i ))
  (( i++ ))
done

echo ${sum} # 5050
```

## 小结

本来就是想简单的整理一下 shell 的常用基础语法，没想到越整理接触到的概念越多，导致我要往这篇文章里塞入的东西也越来越多，当你读完以上文章内容，不过是接触到了 shell 语法的冰山一角。它还有很多基础语法是我没有整理到位的，也有很多进阶应用是这篇文章不应涉及但却十分重要的，毕竟 shell 非常接近 Unix 系统内核，因此如果你想要系统的学习 shell 还请根据自身情况参考我在 [前言](#前言) 中给出的书籍进行学习。

## 参考

1. [菜鸟教程：Shell 变量][1]
2. [菜鸟教程：Shell test 命令][7]
3. [菜鸟教程：Linux read 命令][12]
4. [Zhongbo Wang：Shell 十三问][8]
5. [梦想远航：shell中的(),{}几种语法用法][6]
6. [Locutus：Shell编程中的用户输入处理...][13]
7. [知乎-小晶：掌握Shell编程，一篇就够了][2]
8. [Advanced Bash-Scripting Guide-中文版][9]
9. [chengd：Linux—shell中$(( ))、$( )、``与${ }的区别][3]



[1]: https://www.runoob.com/linux/linux-shell-variable.html
[2]: https://zhuanlan.zhihu.com/p/102176365
[3]: https://www.cnblogs.com/chengd/p/7803664.html
[4]: https://img.blanc.site//wiki/img/20210514012900.jpg
[5]: https://zh-google-styleguide.readthedocs.io/en/latest/google-shell-styleguide/contents/
[6]: https://www.cnblogs.com/hkui/p/6423918.html
[7]: https://www.runoob.com/linux/linux-shell-test.html
[8]: https://wiki.jikexueyuan.com/project/13-questions-of-shell/
[9]: https://linuxstory.gitbook.io/advanced-bash-scripting-guide-in-chinese/
[10]: http://billie66.github.io/TLCL/index.html
[11]: https://note.yuchaoshui.com/blog/post/yuziyue/shell%E6%95%B0%E7%BB%84%E6%80%BB%E7%BB%93
[12]: https://www.runoob.com/linux/linux-comm-read.html
[13]: https://blog.csdn.net/yjk13703623757/article/details/79028738