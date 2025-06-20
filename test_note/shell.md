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





