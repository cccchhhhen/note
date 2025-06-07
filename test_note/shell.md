

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

