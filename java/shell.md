# shell

## 1、输出 hello world

1. 创建 hello.sh 脚本文件
2. 编辑 hello.sh

```shell
#!/bin/bash

echo "hello"
```

3. 运行

```shell
sh ./hello.sh

# 或者直接输入脚本名字运行
# 直接输入脚本运行之前需要开启权限。 
# chmod +x ./hello.sh
./hello.sh
```

## 2.变量

### 2.1、全局变量

```shell
# $HOME $PWD $SHELL $USER 等
# 查看值
echo $HOME
echo $PWD
echo $SHELL
echo $USER
```

### 2.2、自定义变量

```shell
# 定义局部变量
name=zhangsan

# 定义全局变量
export name

# 定义只读变量
readonly haha=10

# 输出
echo $name
```

### 2.3、参数变量

```shell
#!/bin/bash

# 定义参数变量
echo "hello, $1"
# 定义参数变量
echo "hello, $2"

# 执行脚本
./helloname.sh zhangsan lisi
```

### 2.4、特殊变量

```shell
# 得到参数的个数
$#
# 得到所有参数，把所有的参数看出一个整体
$*
# 得到所有参数，把每个参数区分对待
$@
# 判断上一个脚本执行是否成功 0为成功，非0为失败 , 可以 echo $? 直接输出测试
$?
```

## 3、运算符

```shell
# 运算 1 + 1
echo $[1+1]

# 运行 1 + 1 并且赋值给变量
a=$[1+2]
echo $a
```

## 4、条件判断

```shell
# 判断是否相等
[ 2 = 2 ]

# 判断是否有可读权限
[ -r hello.sh ]

# 判断是否有可写权限
[ -w hello.sh ]

# 判断是否有可执行权限
[ -x hello.sh ]

# 判断文件是否存在
[ -e hello.sh ]

# 判断文件是否存在是一个常规的文件（file）
[ -f hello.sh ]

# 判断文件是否存在是一个目录（directory）
[ -d hello.sh ]

# 得到结果
echo $?
```

## 5、流程控制

### 5.1、if

#### 5.1.1、语法

```shell
if [ 条件判断式 ];then

程序

fi
```

#### 5.1.2、demo

```shell
#!/bin/bash

name=zhangsan

if [ $name = zhangsan ];then

echo "名字是张三哈!"

fi
```

### 5.2、if else

#### 5.2.1、语法

```shell
if [ 条件判断式 ];then

程序

else

程序

fi
```

#### 5.2.2、demo

```shell
#!/bin/bash

name=zhangsan

if [ $name = zhangsan ];then

echo "名字是张三哈!"

else

echo "名字不是张三！！！"

fi
```

### 5.3、if else if

#### 5.3.1、语法

```shell
#!/bin/bash

name=lisi

if [ 条件判断式 ];then

程序

elif [ 条件判断式 ];then

程序

fi
```

#### 5.3.2、demo

```shell
#!/bin/bash

name=lisi

if [ $name = zhangsan ];then

echo "名字是张三哈!"

elif [ $name = lisi ];then

echo "名字是李四！"

fi

```

### 5.3、case

#### 5.3.1、语法

```shell
case $变量名 in
"值1")
	如果变量的值等于值1，则执行程序1
;;
"值2")
	如果变量的值等于值2，则执行程序2
;;
...省略其他分支...
*)
	如果变量的值都不是以上的值，则执行次程序
;;
esac
```

#### 5.3.2、demo

```shell
#!/bin/bash

name=lisi

case $name in

"zhangsan")
  echo "名字是张三哈!"
  ;;
"lisi")
  echo "名字是李四哈!"
  ;;
*)
  echo "默认值"
  ;;
esac
```

## 6、循环

### 6.1、for 循环

> 语法1

#### 6.1.1、语法

```shell
for (( 初始值;循环控制条件;变量变化 ))
do
	程序
done
```

#### 6.1.2、demo 1到10的和

```shell
#!/bin/bash

sum=0

for (( i=1;i<=10;i++ ))
do
  sum=$[ $sum + $i ]
done

echo 1到10的和是：$sum
```

> 语法2

#### 6.1.3、语法

```shell
for 变量 in 值1 值2 值3...
do
	程序
done
```

#### 6.1.4、demo

```shell
#!/bin/bash

for name in zhangsan lisi wagnwu zhaoliu
do
echo $name
done
```



### 6.2、while 循环

#### 6.2.1、语法

```shell
while (( 条件判断式 ))
do
	程序
done
```

#### 6.2.2、demo

```shell
#!/bin/bash

count=0
while (( $count < 10 ))
do
count=$[ $count +1 ]
echo 当前count：$count
done
```

#### 6.2.3、注意点

while 条件判断式可以用双括号括起来，也可以用一个单中括号：

不过用 [ ] 的 不能用运算符号

```shell
while [ 条件判断式 ]
do
	程序
done
```



## 7、read 读取控制台输入

### 7.1、基本语法

```shell
read (选项)（参数）
1. 选项：
	-p: 指定读取值时的提示符：
	-t: 指定读取值时等待的时间（秒）如果不加 -t 表示一直等待
2. 参数
	变量: 指定读取值变量名字
```

### 7.2、demo 读取控制台输入的名字

```shell
#!/bin/bash

read -p "请输入您的姓名：" name
echo "欢迎：" $name
```



## 8、系统函数

### 8.1、basename

#### 8.1.1、在命令行中使用

```shell
# 打印文件名字
basename /Users/cuiyue/workspase/systemmethod.sh
# 输出：
systemmethod.sh
```

#### 8.2.2、在脚本中使用

```shell
#!/bin/bash

currentFileName=$( basename $0 )
echo "当前文件目录：" $currentFileName
```

### 8.2、dirname

#### 8.2.1、在命令行中使用

```shell
# 打印文件所在路径
dirname /Users/cuiyue/workspase/systemmethod.sh
# 输出：
/Users/cuiyue/workspase
```

#### 8.2.2、在脚本中使用

```shell
#!/bin/bash

currentDirName=$( dirname $0 )
echo "当前文件目录：" $currentDirName
```

### 8.3、注意:

在脚本中使用系统函数 语法为：$(    )



## 9、自定义函数

### 9.1、语法

```shell
# 定义函数 
# 其中 function 可以不用写，可以没有返回值
function 函数名字(){
	函数体
  返回值
}

# 调用函数
变量接收函数的返回值=$(函数名字 实参1 实参2 ...)
```

### 9.2、demo 

```shell
#!/bin/bash

# 定义个两个数字加法函数
add(){
  s=$[ $1 + $2 ]
  echo $s
}
# 调用函数
sum=$(add 2 2)
# 控制台输出
echo "和:" $sum
```

