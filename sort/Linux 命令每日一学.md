# Linux 命令每日一学

## sort 命令

sort 命令是　``管排序`` 的命令。

### sort 命令的工作原理

sort 将文件的每一行作为一个单位，相互比较，比较原则是从首字符向后，依次按照ASCII码值进行比较，最后将它们按升序输出。

cat 命令输出:
![Alt text](https://github.com/badcyc/dailyLinux/raw/master/sort/2018-10-31%2015-59-15%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

sort 命令输出：
![Alt text](https://github.com/badcyc/dailyLinux/raw/master/sort/2018-10-31%2016-00-53%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

### sort 的-u 选项

它的作用很简单，就是在输出行中去除重复行

![Alt text](https://github.com/badcyc/dailyLinux/raw/master/sort/2018-10-31%2016-04-17%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

### sort的-r选项

sort 默认的排序方式是升序，如果想改成降序，就加个``-r`` 搞定

![Alt text](https://github.com/badcyc/dailyLinux/raw/master/sort/2018-10-31%2016-07-14%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

### sort的-o选项

由于sort默认是把结果输出到标准输出，所以需要用重定向才能将结果写入文件，形如`sort filename > newfile`
但是，如果你想把排序结果输出到原文件中，用重定向可就不行了
![Alt text](https://github.com/badcyc/dailyLinux/raw/master/sort/2018-10-31%2016-08-48%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

就在这个时候，-o选项出现了，它成功的解决了这个问题，让你放心的将结果写入原文件。这或许也是-o比重定向的唯一优势所在

![Alt text](https://github.com/badcyc/dailyLinux/raw/master/sort/2018-10-31%2016-11-06%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

### sort的-n选项

使用-n选项，来告诉sort，“要以数值来排序”！

![Alt text](https://github.com/badcyc/dailyLinux/raw/master/sort/2018-10-31%2016-13-29%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)


### sort的-t选项和-k选项

sort -t选项，后面可以设定间隔符。
指定间隔符后，就可以用-k来指定列数

![Alt text](https://github.com/badcyc/dailyLinux/raw/master/sort/2018-10-31%2016-17-26%E5%B1%8F%E5%B9%95%E6%88%AA%E5%9B%BE.png)

我们使用冒号作为间隔符，并针对第二列来进行数值升序排序。

### 其他的 sort 常用选项

- `-f` 会将小写字母都转换为大写字母来进行比较，亦即忽略大小写
- `-c` 会检查文件是否已排好序，如果乱序，则输出第一个乱序的行的相关信息，最后返回1
- `-`C 会检查文件是否已排好序，如果乱序，不输出内容，仅返回1
- `-M` 会以月份来排序，比如JAN小于FEB等等
- `-b` 会忽略每一行前面的所有空白部分，从第一个可见字符开始比较
