# shell 变量、函数、循环、追踪调试
## 变量
### shell script 中的默认变量
在执行脚本的同时输入变量
```
sh scriptname opt1 opt2 opt3
   $0         $1   $2 	$3
$#   变量个数
$@  各个独立变量
$*   $1c$2c$3c$4, c为分隔符，默认为空格
$0   scriptname
```
实例
```
#!/bin/bash
# Program
#       This program is used to test the default option of shell script.
# History
#       2018/05/04 Sharon scripttest05
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH
echo "scriptname: $0"
echo "the parameter number: $#"
echo "all parameters: $@"
echo "1st=$1, 2nd=$2, 3rd=$3"
exit 0
~         
```
执行脚本并输入变量值
```
$ sh test_option.sh 3 4 6
scriptname: test_option.sh
the parameter number: 3
all parameters: 3 4 6
1st=3, 2nd=4, 3rd=6

```
### 变量偏移
```
shift n
```
拿掉前面n个变量，同一个script中可叠加
```
#!/bin/bash
# Program
#       This program is used to test the use of shell script shift.
# History
#       2018/05/04 Sharon scripttest06

PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

echo "scriptname: $0"
echo "the parameter number: $#"
echo "all parameters: $@"
echo "1st=$1, 2nd=$2, 3rd=$3, 4th=$4, 5th=$5, 6th=$6"

shift
echo "the first shift"
echo "the parameter number: $#"
echo "all parameters: $@"
echo "1st=$1, 2nd=$2, 3rd=$3, 4th=$4, 5th=$5, 6th=$6"

read -p "Place input the shift number:" n
shift $(echo $n)
echo "the second shift"
echo "the parameter number: $#"
echo "all parameters: $@"
echo "1st=$1, 2nd=$2, 3rd=$3, 4th=$4, 5th=$5, 6th=$6"

exit 0
```
```
$ sh test_shift.sh a b c d e f
scriptname: test_shift.sh
the parameter number: 6
all parameters: a b c d e f
1st=a, 2nd=b, 3rd=c, 4th=d, 5th=e, 6th=f
the first shift
the parameter number: 5
all parameters: b c d e f
1st=b, 2nd=c, 3rd=d, 4th=e, 5th=f, 6th=
Place input the shift number:3 
the second shift
the parameter number: 2
all parameters: e f
1st=e, 2nd=f, 3rd=, 4th=, 5th=, 6th=
```

## function
shell script 中建立函数
```
function function_name() {
函数内容
}
```
注意：</br>
因为SHELL是逐行解释编译，所以被调用的函数必须写在调用代码的前面</br>
以用户输入作为判断条件时，应将变量写在被调用函数内，不然如果函数调用自己时很容易出现死循环</br>
function中的$0、$1、$2等变量和其他变量一样，要注意局部变量和全局变量的问题</br>

## 循环 loop
### 不成立-终止
```
while [condition]
do
程序主体
done
```
### 成立-终止
```
until [condition]
do
程序主体
done
```
实例：</br>
让使用者输入一个数字，程序可以由 1+2+3... 一直累加到使用者输入的数字为止。</br>
```
#!/bin/bash
# Program
#       This program let user input a number and add numbers from 1 to the number the user input.
# History
#       2018/05/20 Sharon 1st
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/usr/local/bin:/usr/local/sbin:/~bin/
export PATH

read -p "place input a number: " num

if (("$num" <  0)); then
        echo "Place enter an integer greater than 0."
        exit 1
else
        s=0
        t=0
        until (("$s" > "$num"))
        do
                t=$((t+s))
                s=$((s+1))
        done
        echo "add from 1 to the number you input: " $t
        exit 0
fi


$ sh math_plustest.sh
place input a number: 99
add from 1 to the number you input:  4950
$ sh math_plustest.sh
place input a number: -10
Place enter an integer greater than 0.
```

### 固定循环
#### 迭代
和 Python 中 for ... in ... 的迭代用法相同
```
for var in con1 con2 con3（或$var_all）
do
程序主体
done
第一次循环，var=con1
第二次循环，var=con2
第三次循环，var=con3
```
实例：</br>
/etc/passwd 里面以 : 来分隔，第一栏为账号名称。</br>
将 /etc/passwd 的第一栏取出，而且每一栏都以一行字符串『The 1 account is root 』来显示，那个 1 表示行数。</br>
```
#!/bin/bash

PATH=/bin:/sbin:usr/bin:usr/sbin:/usr/local/bin:/usr/local/sbin:~/bin
export PATH

account_all=$(cat /etc/passwd |cut -d ':' -f1)  #执行$()中的代码，取出第一列，即用户名 

t=1
for account in $account_all
do
        echo "The $t account is $account."
        t=$((t+1))
done
```
#### 条件循环
和 C 中的 for 条件循环相似
```
for ((i==1；i<=100；i++))
do
        程序段
done
```

## shell script 追踪测试
```
sh [-nvx] script.sh
-n 不执行script，仅查询语法，语法正确无输出
-v 先输出script再执行script
-x 边输出script内容，边执行script，走查
```
