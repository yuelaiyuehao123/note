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

