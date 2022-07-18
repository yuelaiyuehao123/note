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

