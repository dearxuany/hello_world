# Python基础语法
## 编码
默认情况下，Python 3 源码文件以 UTF-8 编码，所有字符串都是 unicode 字符串。
```
# -*- coding: cp-1252 -*-
```
上述定义允许在源文件中使用 Windows-1252 字符集中的字符编码，对应适合语言为保加利亚语、白罗斯语、马其顿语、俄语、塞尔维亚语。

## 标识符
* 第一个字符必须是字母表中字母或下划线 _ 。
* 标识符的其他的部分由字母、数字和下划线组成。
* 标识符对大小写敏感。<br>
在 Python 3 中，非 ASCII 标识符也是允许的了。

## 保留字
保留字即关键字，我们不能把它们用作任何标识符名称。
keyword 模块，可以输出当前版本的所有关键字：
```
$ python
Python 2.7.5 (default, Apr 11 2018, 08:07:30) 
>>> import keyword
>>> keyword.kwlist
['and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'exec', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'not', 'or', 'pass', 'print', 'raise', 'return', 'try', 'while', 'with', 'yield']
```
## 注释
Python中单行注释以 # 开头
```
# description 1
print("hello,world")
# description 2
# description 3

#以下多行注释格式在linux CentOS7 vim编辑下无法正常使用，直接命令行执行就可以
...
description 4
description 5
...

"""
description 6
description 7
"""
```

## 行与缩进
使用缩进来表示代码块，不需要使用大括号 {} 。<br>
缩进的空格数是可变的，但是同一个代码块的语句必须包含相同的缩进空格数。<br>
```
if True:
    print ("True")else:
    print ("False")
```
以下代码最后一行语句缩进数的空格数不一致，会导致运行错误：
```
if True:
    print ("Answer")
    print ("True")else:
    print ("Answer")
  print ("False")    # 缩进不一致，会导致运行错误
```
以上程序由于缩进不一致，执行后会出现类似以下错误：
```
 File "test.py", line 6
    print ("False")    # 缩进不一致，会导致运行错误
                                      ^IndentationError: unindent does not match any outer indentation level
```

Python强制要求代码缩进来嵌套逻辑（据说是为了可读性以及培养编程者的良好习惯）。</br>
同一个代码块中，各行的代码缩进量必须相同。</br>
* 空格：标准也更好，使用4个空格。
* 制表符：一个制表符，相当于8个空格。</br>
根据PEP的规定，必须使用4个空格来表示每级缩进（不清楚4个空格的规定如何，在实际编写中可以自定义空格数，但是要满足每级缩进间空格数相等）。使用Tab字符和其它数目的空格虽然都可以编译通过，但不符合编码规范。支持Tab字符和其它数目的空格仅仅是为兼容很旧的的Python程序和某些有问题的编辑程序。以上两种符号都可以用于缩进，但是程序里不能两种混用，否则会造成判断错误。编辑器不同也会导致错误发生。
* ：冒号：冒号可用于使一个代码块里的所有代码行均有相同缩进。</br>

参考</br>
关于空格和制表符</br>
http://www.cnblogs.com/tt-0411/archive/2011/11/11/2245693.html</br>
关于冒号</br>
https://blog.csdn.net/pwc1996/article/details/50899411


## 多行语句
反斜杠(\\)来实现多行语句；<br>
在 [], {}, 或 () 中的多行语句，不需要使用反斜杠(\\)。

## 数字(Number)类型
python中数字有四种类型：整数、布尔型、浮点数和复数。
* int (整数), 如 1, 只有一种整数类型 int，表示为长整型，没有 python2 中的 Long。
* bool (布尔), 如 True。
* float (浮点数), 如 1.23、3E-2
* complex (复数), 如 1 + 2j、 1.1 + 2.2j<br>
Python将虚数视为实数为0的复数。

## 字符串(String)
* python中单引号和双引号使用完全相同。
* 使用三引号('''或""")可以指定一个多行字符串。
* 转义符 '\\'
* 反斜杠可以用来转义，使用r可以让反斜杠不发生转义, 如 r"this is a line with \n" 则\n会显示，并不是换行。
* 按字面意义级联字符串，如"this " "is " "string"会被自动转换为this is string。
* 字符串可以用 + 运算符连接在一起，用 * 运算符重复。
* Python 中的字符串有两种索引方式，从左往右以 0 开始，从右往左以 -1 开始。
* Python中的字符串不能改变。
* Python 没有单独的字符类型，一个字符就是长度为 1 的字符串。
* 字符串的截取的语法格式如下：变量[头下标:尾下标]

```
>>> word="string"
>>> sentence="This is a sentence."
>>> paragraph="""This is a paragraph,
... with serve lines."""
>>> print(word)
string
>>> print(sentence)
This is a sentence.
>>> print(paragraph)
This is a paragraph,
with serve lines.
>>> 

>>> str='abcdefg123'
>>> print(str)			# 输出字符串
abcdefg123
>>> print(str[0:-1])	# 输出第一个到倒数第二个字符
abcdefg12
>>> print(str[0])		# 输出第一个字符
a
>>> print(str[2:5])		# 输出第三至五个字符，注意开头由0开始数，结尾由1开始数
cde
>>> print(str[2:])		# 输出第三至最后
cdefg123
>>> print(str*2)		# 输出字符串两次
abcdefg123abcdefg123
>>> print(str+"456789")	# 连接字符串
abcdefg123456789

>>> print('hello\nworld')
hello
world
>>> print(r'hello\nworld')	# r让\转义字符不产生作用
hello\nworld
>>> 

>>> print('let's go!')			# 字符串内容中出现’号时，应用””括起或使用\’转义
  File "<stdin>", line 1
    print('let's go!')
               ^
SyntaxError: invalid syntax
>>> print("let's go!")
let's go!
>>> print('let\'s go!')
let's go!
>>> 
```


## 空行
函数之间或类的方法之间用空行分隔，表示一段新的代码的开始。类和函数入口之间也用一行空行分隔，以突出函数入口的开始。<br>
空行与代码缩进不同，空行并不是Python语法的一部分。书写时不插入空行，Python解释器运行也不会出错。但是空行的作用在于分隔两段不同功能或含义的代码，便于日后代码的维护或重构。<br>
记住：空行也是程序代码的一部分。

## 获得用户输入
单纯输入
```
input(“letter show on the screen:”)
```
变量赋值
```
x=input(“place input the value of x:”)
y=input(“place input the value of y:”)
print(x*y)
```

   

## 同一行显示多条语句
语句之间使用分号(\;)分割

## 多个语句构成代码组
* 代码组：缩进相同的一组语句构成一个代码块。<br>
像if、while、def和class这样的复合语句，首行以关键字开始，以冒号( : )结束，该行之后的一行或多行代码构成代码组。
* 子句(clause)：首行及后面的代码组。

## print输出
```
>>> x=2
>>> y=3
>>> print(x*y)
6
>>> print("x*y")
x*y
>>> 
>>> print(x*y,"x*y")
(6, 'x*y')
```

## import与from...import
在 python 用 import 或者 from...import 来导入相应的模块。
* 将整个模块(somemodule)导入（除非特殊要求，一般建议用这种形式）
import somemodule
* 从某个模块中导入某个函数
from somemodule import somefunction
* 从某个模块中导入多个函数 
from somemodule import firstfunc, secondfunc, thirdfunc
* 将某个模块中的全部函数导入
from somemodule import *
```
>>> import math			    # 整个模块导入
>>> math.floor(32.9)		# 调用模块中floor函数，将数字转换为小于或等于自己的整数
32.0
>>> math.ceil(32.9)			# 调用模块中ceil函数，将数字转换为小于或等于自己的整数
33.0						
>>> from math import sqrt	# 直接调用模块中sqrt函数，开方
>>> sqrt(9)
3.0
>>> 
```

如果两个模块都包含同一个同名函数：
* 导入这两个模块，调用方法为 module1.function()、module2.function()。
* 在语句末尾添加as给它指定名字，可以给module起名，也可以给function起名。
```
import math as mathematics      #给模块命名
a=mathematics.sqrt(100)
print(a)

from math import sqrt as square_root    #给函数命名
b=square_root(10)
print(b)

import math		#不想上面的命名影响后面语句要重新导入模块
c=math.sqrt(4)	#没有import，只有这句会出错
print(c)

10.0
3.1622776601683795
2.0
```

## 命令行参数
### 使用-h参数查看各参数帮助信息
```
$ python -h
usage: python [option] ... [-c cmd | -m mod | file | -] [arg] ...
Options and arguments (and corresponding environment variables):
-B     : don't write .py[co] files on import; also PYTHONDONTWRITEBYTECODE=x
-c cmd : program passed in as string (terminates option list)
-d     : debug output from parser; also PYTHONDEBUG=x
-E     : ignore PYTHON* environment variables (such as PYTHONPATH)
-h     : print this help message and exit (also --help)
-i     : inspect interactively after running script; forces a prompt even
         if stdin does not appear to be a terminal; also PYTHONINSPECT=x
-m mod : run library module as a script (terminates option list)
-O     : optimize generated bytecode slightly; also PYTHONOPTIMIZE=x
-OO    : remove doc-strings in addition to the -O optimizations
-R     : use a pseudo-random salt to make hash() values of various types be
         unpredictable between separate invocations of the interpreter, as
         a defense against denial-of-service attacks
-Q arg : division options: -Qold (default), -Qwarn, -Qwarnall, -Qnew
-s     : don't add user site directory to sys.path; also PYTHONNOUSERSITE
-S     : don't imply 'import site' on initialization
-t     : issue warnings about inconsistent tab usage (-tt: issue errors)
-u     : unbuffered binary stdout and stderr; also PYTHONUNBUFFERED=x
         see man page for details on internal buffering relating to '-u'
-v     : verbose (trace import statements); also PYTHONVERBOSE=x
         can be supplied multiple times to increase verbosity
-V     : print the Python version number and exit (also --version)
-W arg : warning control; arg is action:message:category:module:lineno
         also PYTHONWARNINGS=arg
-x     : skip first line of source, allowing use of non-Unix forms of #!cmd
-3     : warn about Python 3.x incompatibilities that 2to3 cannot trivially fix
file   : program read from script file
-      : program read from stdin (default; interactive mode if a tty)
arg ...: arguments passed to program in sys.argv[1:]

Other environment variables:
PYTHONSTARTUP: file executed on interactive startup (no default)
PYTHONPATH   : ':'-separated list of directories prefixed to the
               default module search path.  The result is sys.path.
PYTHONHOME   : alternate <prefix> directory (or <prefix>:<exec_prefix>).
               The default module search path uses <prefix>/pythonX.X.
PYTHONCASEOK : ignore case in 'import' statements (Windows).
PYTHONIOENCODING: Encoding[:errors] used for stdin/stdout/stderr.
PYTHONHASHSEED: if this variable is set to 'random', the effect is the same
   as specifying the -R option: a random value is used to seed the hashes of
   str, bytes and datetime objects.  It can also be set to an integer
   in the range [0,4294967295] to get hash values with a predictable seed.
```

### Linux下启动python
```
$ python
Python 2.7.5 (default, Apr 11 2018, 08:07:30) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-28)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

### Linux下打开python程序
```
$ python python_program_name.py 
```

### Linux调用/usr/bin下的python解释器
```
#!/usr/bin/python3  # 定义使用的语法，windows下可以不写
```
## help() 查询内置函数说明文档
```
>>> help
Type help() for interactive help, or help(object) for help about object.
>>> help(round)

Help on built-in function round in module __builtin__:

round(...)
    round(number[, ndigits]) -> floating point number
    
    Round a number to a given precision in decimal digits (default 0 digits).
    This always returns a floating point number.  Precision may be negative.
```

## 函数
### 内置函数
编程语言中预先定义的函数
* round圆整
返回浮点数x的四舍五入值（区别向下圆整）
round( x [, n] )
x 待圆整数值
n 小数点后位数

## 扩展
### 制表符
键盘上的Tab键位于大小写键（ Caps Lock ）的上面，Tab 键是 tabulator key 的缩写，其含义是“作表的人，制表机，（打字机上为制表用的）跳格键”。它最基本的用法就是用来绘制无边框的表格。单词之间的间隔都是按下一次 Tab键来实现的。它一般等于 8 个空格的长度，当它的前面有单词的时候，和单词长度加起来刚好能被 8 整除。
