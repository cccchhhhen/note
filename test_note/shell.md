[**练习网站**](https://www.hackerrank.com/dashboard)

**输入：**`read`

**输出：**`echo`

```bash
read a
read b
echo $((a + b))
# 或者
echo $(expr $a + $b)
```

**注释：**

* 单行注释： `#`

* 多行注释：

  ```bash
  : "   
  注释内容
  "
  ```

  > **注：** 
  >
  > * ":" 和 "#" 之间有空格
  > * 不要用中文注释，防止乱码

**编辑**  `bash`

添加`#!/bin/bash`(脚本解释器)后，可以通过`sh file_name` 来执行，声明使用 `Bash` 解释器，称为 `Shebang`，确保脚本在不同环境中行为一致

**判断** `if-elif-else-fi`

```bash
read a
read b
if [ "$a" -gt "$b" ];then    # 注：[]前后要有空格
	echo "$a is greater than $b"
elif [ "$a" -lt "$b" ];then 
	echo "$a is less than $b"
else
	echo "$a is equal to $b"
fi   # 注：要有结束符
```

>Read in one character from STDIN.If the character is 'Y' or 'y' display "YES".If the character is 'N' or 'n' display "NO".No other character will be provided as input.

```bash
# method 1
read character
if [ $character == 'Y' ] || [ $character == 'y' ];then
    echo 'YES'
elif [ $character == 'N' ] || [ $character == 'n' ];then
    echo 'NO'
fi
#method 2
read character
case "$character" in
    [Yy])
        echo "YES"
        ;;
    [Nn])
        echo "NO"
        ;;
esac
```

>Given three integers (, , and ) representing the three sides of a triangle, identify whether the triangle is scalene, isosceles, or equilateral.
>
>- If all three sides are equal, output `EQUILATERAL`.
>- Otherwise, if any two sides are equal, output `ISOSCELES`.
>- Otherwise, output `SCALENE`.

```bash
#!/bin/bash
read a
read b
read c
if [ $a -eq $b ] && [ $c -eq $b ];then
    echo "EQUILATERAL"
elif [ $a -eq $b ] || [ $c -eq $b ] || [ $c -eq $a ];then
    echo "ISOSCELES"
else
    echo "SCALENE"
fi
```

### 语法

1. 指定`shell`解析器

   ```bash
   #!/bin/bash
   ```

   > **#!用来声明脚本由什么shell解释，否则使用默认shell**

   sh是最原始的shell，而bash不完全兼容sh

   查看系统可用的shell终端：`vim /etc/shells`

2. 注释

   * 单行：`#`

   * 多行：

     ```bash
     :<<EOF
     注释内容
     EOF
     ```

     > `EOF`可替换成其他任意字符串

3. 运行

   * 作为可执行文件：`./***.sh`

     添加执行权限：`chmod +x ***.sh`

     按照文件中`#!`指定的解析器解析，否则用默认的解析器

   * 用解析器执行：`bash xxx.sh`

     `bash`不存在才用默认解析器

   * `.***.sh`

     直接用默认解析器，`#!`还是要写的

4. 变量

   * 定义变量：变量名=变量值  `eg:num=1`

   * 只读变量：`readonly`或者`declare`  

     `readonly num` 或者 `declare -r num`

   * 引用变量：`$变量名` 或 `${变量名}`  `eg:$num` 或 `${name}`

   * 清除变量：`unset`  `eg:unset num`

   * 变量类型

     1. 自定义变量（局部变量）：子进程不能访问的变量

     2. 环境变量（全局变量）：子进程可以访问的变量

        ```bash
        # 自定义变量 -> 环境变量
        num=10
        export num # 方法一
        declare -x num # 方法二
        ```

        ```bash
        # 环境变量 -> 自定义变量
        export num=10 # 定义环境变量
        declare +x num # 改为自定义变量
        ```

   * 字符串

     * 字符串可以用单引号、双引号或者不加引号

       > 单引号与双引号的区别：
       >
       > 单引号中的内容会原样输出，不会执行、不会取变量；
       >
       > 双引号中的内容可以执行、可以取变量

     * 获取字符串长度

       ```bash
       str="chen"
       echo ${#str}
       ```

     * 提取子串

       ```bash
       echo ${str:0:2} # 提取从0开始的2个字符 => ch
       ```

     > **Tips:**
     >
     > 1. 定义变量时，等号两边不能有空格
     > 2. 定义变量的时候变量都是字符串，但当变量需要是整数时，会自动把变量转换成整数
     > 3. 被声明为只读的变量无法被unset删除
     > 4. 字符串中，不加引号和双引号效果相同
     > 5. 如果一个变量不存在的话，他的值是空字符串
     > 6. 默认变量是局部变量（自定义变量）
     > 7. 左闭右开取子串

5. 默认变量（位置参数传递）

   * `$n`是第n个参数；`$0`是文件名，包含路径；超过一位数，用`{} eg:echo ${10}`

   * 其他参数相关变量

     1. `$#`：传入的参数个数

     2. `$@`：每个参数分别用双引号括起来的字符串  `eg:"a" "b"`

     3. `$*`：由所有参数构成的用空格隔开的字符串  `eg:"a b"`

     4. `$0`：获取当前执行的进程名

     5. `$$`：获取当前执行的进程号

     6. `$?`：获取命令执行后返回的状态，0表示执行成功，非0表示执行失败(不是`stdout`，而是`exit code`)

     7. `$(command)`：返回command这条命令的`stdout`（可嵌套）

        * （可嵌套） 和 (不可嵌套) 区别是：可嵌套就是可以实现多个命令

          ```bash
          # test.sh
          #!/bin/bash
          
          name="hello"
          echo $(echo $name
          name="world"
          echo $name
          )
          # 结果 ： hello world
          ```

6. 数组

   * 数组中可以存放多个不同类型的值，只支持**一维**数组，初始化时不需要指明数组大小

   * 数组下标从**0**开始

   * **定义：**数组用小括号表示，元素之间用空格隔开  `eg:array=(a 1 chen)`

   * **读取数组中某个元素的值：**`echo ${array[index]}`

   * **读取整个数组：**`echo "${array[@]}"`或`echo "${array[*]}"`

   * **数组长度：**`echo "${#array[@]}"`或`echo "${#array[*]}"`

     > 注：`echo "${#array}"`是返回数组第一个元素的长度

7. `expr`命令

   * `expr表达式`命令用于求表达式的值

     > 1. 用空格隔开每一项
     > 2. 用反斜杠放在`shell`特定的字符前面（发现表达式运行错误时，可以试试转义）
     > 3. 对包含空格和其他特殊字符的字符串要用引号括起来
     > 4. `expr`会在`stdout`中输出结果。如果为逻辑关系表达式，则结果为真时，`stdout`输出1，否则输出0
     > 5. `expr`的`exit code`：如果为逻辑关系表达式，则结果为真时，`exit code`为0，否则为1

   * 字符串表达式

     * `length str`：返回`str`的长度

     * `index xxx yyy`：`yyy`中任意单个字符在`xxx`中首次出现的位置，**下标从1开始**，如果`xxx`中找不到`yyy`中的任意字符，返回0

     * `substr str index1 len`：返回`str`字符串从`index`开始，最大长度为`len`的子串。如果`index`或`len`为负数，0或非数值，则返回空字符串

       ```bash
       #!/bin/bash
       
       str="hello world"   
       echo $(expr length "$str")  #  11
       echo $(expr index "$str" "wo")  #  5
       echo `expr substr "$str" 7 7`  #  world
       ```

   * 整数表达式

     * `expr`支持普通的算术操作

     *  优先级：字符串表达式 > 算术表达式 > 逻辑关系表达式

       >`+ - `：加减运算。两端参数会转换为整数，如果转换失败则报错。
       >
       >`* / %`：乘，除，取模运算。两端参数会转换为整数，如果转换失败则报错。
       >
       >`()`：可以改变优先级，但需要用反斜杠转义

       ```bash
       #!/bin/bash
       
       a=4
       b=3
       
       echo `expr $a + $b`  # 7
       echo `expr $a - $b`  # 1
       echo `expr $a \* $b`  # 12
       echo `expr $a "*" $b`  # 12
       echo `expr $a / $b`  # 1
       echo `expr $a % $b`  # 1
       echo $((($a+$b)*($a-$b)))  # 7
       ```

     * 逻辑关系表达式

       * `| & > >= < <= = == !=`

8. `read`命令

   * 命令用于从标准输入中读取**单行**数据。当读到文件结束符时，`exit code`为1，否则为0
   * `read data`：收尾的空格会被删除，去除`\`
   * 参数：
     * `-p`：后面接提示信息
     * `-t`：后面跟秒数，定义输入字符的等待时间，超时等待时间后会自动忽略此命令
     
   * 读取多个值

     `read data1 data2`输入时以空格作为分隔符

   * 读入数组  `read -a array`  将空格分割的结果存入数组

   * 保留整行内容：`IFS= read -r lines`

     * `-r`：防止反斜杠转义（例如`\n`会被当作两个字符）（保留`\`）
     * `IFS=`：禁用分词（`Internal Field Separator`），确保行首/行末空格不被删除 




9. `echo`命令

   * `echo`用于输出字符串

   * 显示普通字符串

     ```bash
     echo hello  # hello 
     echo "world"  # world
     ```

   * 显示转义字符

     ```bash
     echo "\"chen\""  # chen
     echo \"chen\"  # chen
     ```

   * 显示变量

   * 显示换行

     ```bash
     echo -e "hi\ndog"  # 输出两行
     echo -e "hello \c cat" # -e 开启转义 \c 不换行，且终止后续输出
     ```

   * 定向输出

     ```bash
     echo "hello world" > output.txt   # 不存在output.txt 默认创建（以覆盖的方式）
     ```

     > tips:`help echo`查看转义

### 命令

1. `ls` - 列出目录内容

### 题目

1. `HackerRank`

   > Given N lines of input, print the 3rd character from each line as a new line of output. It is guaranteed that each of the n lines of input will have a 3rd character.
   >
   > **Input Format**
   >
   > A text file containing N lines of [ASCII](https://en.wikipedia.org/wiki/ASCII) characters.
   >
   > **Constraints**
   >
   > - 1≤N≤100
   >
   > **Output Format**
   >
   > For each line of input, print its 3rd character on a new line for a total of N lines of output.
   >
   > **Sample Input**
   >
   > ```
   > Hello
   > World
   > how are you
   > ```
   >
   > **Sample Output**
   >
   > ```
   > l
   > r
   > w
   > ```

解法一：

```bash
#!/bin/bash

mapfile -t lines

for i in "${lines[@]}";do
        if [ ${#i} -ge 3 ];then
            echo "${i:2:1}"
        fi
done
```

> **工作原理：**
>
> 1. **`mapfile -t lines：`**
>
>    * 一次性读取所有输入行（以`Ctrl+D`结束）
>    * `-t` 选项移除行尾的换行符
>    * 将每行内容存储在数组`lines`中
>    * **高效处理**：
>      - `mapfile`直接读取到数组比逐行`read`更快
>      - 一次完成读取操作，避免循环中的多次IO
>
> 2. **`for line in "${lines[@]}"`**：
>
>    * 遍历数组中的每行内容
>
>    * `"${lines[@]}"` 确保正确处理包含空格的行
>
>      > **区分：`for line in ${lines[@]}`**
>      >
>      > 缺少双引号：
>      >
>      > * **单词分割（Word Splitting）**：当数组元素包含空格或制表符时，会分割成多个元素
>      > * **路径扩展（Pathname Expansion）**：`输入行："*.txt"`
>      >   - **有双引号**：`line="*.txt"`（按字面处理）
>      >   - **无双引号**：Bash 会尝试匹配当前目录下的 `.txt` 文件
>
> 3. **`${line:2:1}`**：
>
>    - 提取字符串中索引位置为2的单个字符
>
> 4. **长度检查**：
>
>    - `[ ${#line} -ge 3 ]` 确保行至少有3个字符
>    - 虽然题目保证每行都有第3个字符，但添加检查更健壮

解法二：

```bash
#!/bin/bash
# 使用while循环逐行处理
while IFS= read -r line || [ -n "$line" ]; do
    [ ${#line} -ge 3 ] && echo "${line:2:1}"
done
```

> **安全处理**：
>
> - `-r` 选项防止反斜杠转义
> - `IFS=` 保留前导/尾随空格
> - `|| [ -n "$line" ]` 处理不以换行符结尾的行     -n : 判断是不是非空的
>
> **兼容性**：
>
> - 适用于包含空格、特殊符号和各种字符的输入行
> - 正确处理空行（跳过长度不足3的行）

### **PUTTY**安装

1 **更新系统**

```bash
sudo apt update
sudo apt upgrade
```

2 **安装`OpenSSH`服务器**

```bash
sudo apt install openssh-server
```

3 **检查SSH服务状态**

```bash
sudo systemctl status ssh
```

如果未运行，启动服务并设置开机自启

```bash
sudo systemctl start ssh
sudo systemctl enable ssh
```

4 **(可选) 配置SSH**

```bash
sudo nano /etc/ssh/sshd_config
sudo systemctl restart ssh #restart
```

5 **检查防火墙设置**

```bash
sudo ufw allow ssh
# 或者指定端口，如果修改了端口
sudo ufw allow 2222/tcp
```

### Proj

1 创建项目结构

```bash
mkdir -p LoginAPIDBTest/{tests,utils,config,reports,logs}
cd LoginAPIDBTest
touch requirements.txt run_tests.sh
```

2 安装依赖`requirements.txt`

```bash
pytest==7.4.0
requests==2.31.0
PyMySQL==1.1.0
PyYAML==6.0.1
pytest-html==4.1.1
```

3 安装 `python3 pip3`

```bash
# ubuntu
sudo apt update
sudo apt install python3 python3-pip

# 验证安装
python3 --version
pip3 --version
```

4 安装 `requirements.txt` 中的包

```bash
pip3 install -r requirements.txt
# 问题：安装超时了，使用清华园
pip3 install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

5 配置文件 `config/config.yaml`





