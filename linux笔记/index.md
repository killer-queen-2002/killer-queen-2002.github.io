# Linux笔记


## 1.文件系统命令

```
pwd–显示当前工作目录的绝对路径

cd–更改工作目录路径

mkdir–创建目录

ls–列出目录和文件信息【 -l 详细信息展示 -a显示全部文件（包括隐藏）-d查看目录属性 -h与-l一起以易读格式输出 -1每一项单独显示在一行上】

touch–创建空文件、更改文件时间

cp–复制文件或目录 -a在复制目录时使用

mv–文件或目录改名、移动文件和目录

rm–通常与-rf一起使用删除文件或目录

通配符–

?——匹配任意一个字符

*——匹配任意多个字符

[]——相当于或

-——代表一个范围

find–查找文件或目录【-name 匹配文件名 -type 匹配文件类型（f为文件d为目录）-size 匹配文件大小（+50k查找＞50k文件，-50k查找<50k文件）】

cat—显示文件内容、连接文件内容

head–显示文件前若干行 【用法：head -n 行数值 [文件]】

tail–显示文件末尾数据 【用法：tail -n 行数值 [文件]】

grep–搜索与字符串匹配的行【-i 忽略大小写 -n 显示行号 -r 递归搜索子目录 -v 反找不符查找条件的】

tar–打包和解包命令【-c 产生.tar打包文件 -x 解包】

tar -czvf—打包文件名.tar.gz 源文件或目录（-xzvf 解包）

tar -cjvf—打包文件名.tar.bz2 源文件或目录（-xjvf 解包）

tar -cJvf—打包文件名.tar.xz 源文件或目录（-xJvf 解包）

两种链接方式–

ln- s 产生符号链接，其相当于快捷方式。原文件被删除时，链接文件将失效。

ln 产生硬链接。硬链接文件对，删除哪个都不影响对方。
```



## 2.vim命令

```
i		插入
Esc		退出插入
:w		保存
:w /root/newfile另存为其他文件
:q		退出
:q!    	 	强制退出（不保存文件）
:wq		保存并退出
:e ~/install.log打开新的文件进行编辑
h,j,k,l 	左，下、上、右
w 		跳到下一个单词
e 		跳到下一个单词尾部
b 		跳到上一个单词（标点符号不包含在单词内）
B 		跳到上一个单词（标点符号也包含在单词内）
Ctrl+F 		向下整页
Ctrl+B 		向上整页
0 		跳转至行首
$ 		跳转至行尾
gg 		跳转到文件的首行
G 		跳转到文件的末尾行
#gg 		跳转到文件中的第#行
:set nu 	在编辑器中显示行号
:set nonu 	取消行号
x 		删除光标处的单个字符
dd 		删除当前光标所在行
#dd 		删除从光标处开始的#行内容
r 		替换单个字符
R 		替换多个字符
yy 		复制当前光标所在行内容
#yy 		复制从光标处开始的#行内容
p		粘贴内容到光标位置处的下一行
P		粘贴到光标位置处之前
/word		查找字符串";word";
n		定位下一个被查找的字符串
N		定位上一个被查找的字符串
u		撤销操作
:s/old/new	将当前行查找到的第一个字符串“old”替换成“new”
:s/old/new/g	将当前行查找到的所有字符串“old”替换成“new”
:#,#s/old/new/g	在行号";#,#";范围内替换所有的字符串“old”为“new”
:%s/old/new/g	在整个文件范围内替换所有的字符串“old”为“new”
:s/old/new/c	对每个替换动作作提示
```

## 3.重定向和管道

```
标准输入文件，代码为0，使用<或<<

标准输出文件，代码为1，使用>或>>

标准错误输出文件，代码为2，使用2>或2>>

重定向标准输出和标准错误，使用&>或&>> / >>log.txt 2>&1

|–管道可以将第一个命令输出作为第二个命令输入

wc–可以显示行数、单词数和字符数【-l 仅显示多少行 -w 仅显示多少字 -m 多少字符】

ls -l | wc -l 搭配可以统计目录下文件数目

xargs–将输入中的换行、多个连续空格替换成单个空格【xargs wc -l 统计文件行数】

-I用来指定替代字符串，后续出现替代字符串的地方都用标准输入的内容替代。对于标准输入的每一行，都会执行一次命令。需要注意的是，当xargs传送给一个命令的参数过长时，会自动将参数截断，分成多条命令执行。

例1：查找文件，显示每个文件加上.bak

find -name *.conf | xargs -I {} echo {}.bak

例2：查找文件，并复制到指定目录

find -name *.conf | xargs -I {} cp {} home/euler
```

## 4.用户权限管理

```
su–在任意用户之间进行切换

以下命令皆需要使用root用户

useradd–添加用户

passwd–设置用户密码

userdel–删除用户（-r 同时删除用户及主目录）

groupadd–添加组 groupdel–删除组

usermod–将用户添加到一个组 （-G选项，用于指定要将用户添加到哪个组，最好加上-a选项，表示添加，不会覆盖原有组）

文件权限：

可读（r）：允许查看文件内容、显示目录列表

可写（w）：允许修改文件内容，允许在目录中新建、移动、删除文件或子目录

可执行（x）：允许运行程序、切换目录

chmod 设置权限【-R选项可以递归设置目录的权限】

字符法（user，group，other，all）

例：chmod o+r cat.txt——对其他用户设置可读权限

数字法（r-4，w-2，x-1）

例：chmod 444 cat.txt——对文件所有者、文件所属组、其他用户权限全只设置可读

chown 更改文件和目录的所有者和所属组【-R选项可以递归设置目录的权限】

用法：chown [-R] [user] [:group] 文件或目录
```

## 5.进程管理及其他命令

```
ps aux查看所有进程信息

top命令提供了实时的对系统处理器的状态监视

kill用于终止一个进程（-9 选项用于强制终止）

crontab可以按照预先设置的时间周期执行计划任务

*——表示范围内任意时间

,——表示间隔的多个不连续时间

-——表示一个连续的时间范围

/——指定间隔的时间频率

df -h用于查看磁盘使用情况

du -h显示指定的目录或文件所占用的磁盘空间（-s只显示总计结果）

alias可以对命令设置别名（例如将reboot命令设置为输入r即可重启）

export设置或显示环境变量

time命令用来计算命令执行的时间，-p 选项不显示时间单位。后跟的重定向、管道符号，默认都是用于要计时的命令，如果需要用于time命令，需要对time命令进行分组。默认将结果打印到标准错误。
```

## 6.shell脚本编程

```
#–注释 $–变量符

‘一单引号引起来的内容，全部普通字符

“一双引号引起来的内容，可以包括特殊字符

\—转义符，echo命令需要加-e才会解析

`—倒引号，用于执行命令，并返回命令输出

$? 上一个命令的返回值（执行成功返回0，执行失败返回非0）

$n 第n个参数

数组创建：array_name=(value1 value2…value)

数组读取：${array_name[index]}

数组长度：${#array_name[*]}

分支控制语句：if / case 语句

循环控制语句：for / while / until 语句

条件测试 [ 条件表达式 ]

-eq（equal）＝ 等于

-ne（not equal）≠ 不等于

-lt（less than）＜小于

-le（less than or equal）≤ 小于或等于

-gt（greater than）＞大于

-ge（greater than or equal）≥ 大于或等于

逻辑运算符：

-a 逻辑与and

-o 逻辑或or

Shell命令顺序执行：

&& 逻辑与 前面的命令执行失败，则不执行后面的命令

|| 逻辑或 前面的命令执行成功，则不执行后面的命令
```

## 7.sed和awk

```
sed:

替换的一般格式：s/source/target/flag

【flag：g-表示行内全面替换 p-表示打印行 i-表示忽略大小写】

通配符：\ ( \ )用来分组。\1、\2、\3分别表示第1、2、3个分组

sed行命令格式：sed [选项] ‘[范围] [命令]’ 文件名

【-n 只显示处理后的结果 -i 直接修改文件的内容】

【范围：1,2 2,$(最后一行) /^Hello/,+1(^表示行首，+1表示下一行)】

p对匹配内容进行打印

d删除匹配内容

a新增一行

i插入一行（当前行之前）

c取代

awk:

awk列命令格式：awk [选项] ‘[匹配] {动作}’

$0表示整行,$1、$2分别表示第一、二个字段

-F 指定分隔符（默认空格 / tab）

动作中布尔表达式语法与C语言类似（例’$2<60 {print $0}’）

特殊模式BEGIN用于匹配第一个输入文件的第一行之前的位置

内置变量NR表示当前行号

END则用于匹配处理过的最后一个文件的最后一行之后的位置
```

## 8.系统管理

```
htop–比top更好用的进程查看器

Redis–key-value的内存数据库

ncdu–磁盘分析工具

以下为不同系统运行的命令，且需要sudo

管理软件

搜索软件（dnf yum apt） search htop

安装软件（dnf yum apt）install htop

卸载软件（dnf yum）remove htop / apt purge htop

更新软件（dnf yum apt）upgrade

查找命令属于哪个包（dnf yum）provides lsb_release / apt-file search lsb_release

防火墙软件（firewalld / ufw）

查看防火墙状态 firewall-cmd --list-all / ufw status

开放/取消服务

openeuler—firewall-cmd --add(/remove)-service=http --permanent + firewall-cmd --reload

Ubuntu—ufw allow http / ufw delete allow http

开放/取消端口

openeuler—firewall-cmd --add(/remove)-port=80/tcp --permanent + firewall-cmd --reload

Ubuntu—ufw allow 80 / ufw delete allow 80

服务管理

启动服务 systemctl start nginx

查看服务状态 systemctl status nginx

重启服务 systemctl restart nginx

停止服务 systemctl stop nginx

查看是否开机启动 systemctl is-enabled nginx

设置开机启动 systemctl enable nginx

取消开机启动 systemctl disable nginx
```

## 9.磁盘管理

```
磁盘分区

lsblk -f 查看磁盘信息

磁盘分区表类型MBR/GPT

需要使用root权限对磁盘进行分区，如新添加了一块磁盘，使用lsblk查看设备名称为/dev/sdb sudo parted /dev/sdb

创建新的分区表：mklabel gpt 删除分区：rm 查看分区表：print

创建分区1：mkpart part1 0% 10G

创建分区2：mkpart part2 10G 100%

创建并挂载文件系统：

mkfs -t ext4 /dev/sdb

mkdir /mnt/data

mount /dev/sdb /mnt/data

卸载文件系统：umount /mnt/data

自动挂载文件系统：

首先执行blkid命令查看UUID，后编辑/etc/fstab，添加一行

UUID=uuidnumber(查询到的uuid) mntpath(文件系统的挂载目录) fstype(文件系统的文件格式) defaults 0 0

非交互式磁盘分区：sudo parted --script /dev/sdb mklabel gpt

LVM(逻辑卷管理)

管理物理卷（PV，Physical Volumes）：

创建：pvcreate /dev/sdb

查看：pvs/pvdisplay

删除：pvremove /dev/sdb

管理卷组（VG，Volume Group）：

创建：vgcreate vg1 /dev/sdb

扩展：vgextend vg1 /dev/sdc

收缩：vgreduce vg1 /dev/sdb

删除：vgremove vg1

管理逻辑卷（LV，Logical Volumes）：

创建：lvcreate -L(指定逻辑卷大小) 10G -n(指定逻辑卷名称) lv1 vg1(卷组名称作为参数)

查看：lvs/lvdisplay

扩展：lvextend --resizefs(同时调整文件系统大小) -L(指定逻辑卷大小) +10G /dev/vg1/lv1

收缩：lvreduce --resizefs(同时调整文件系统大小) -L(指定逻辑卷大小) -10G /dev/vg1/lv1

删除：lvremove /dev/vg1/lv1

RAID(独立磁盘冗余阵列)

RAID 0—数据被分成几部分，分别同时写入成员磁盘中，提高IO性能，但不提供冗余

RAID 1—通过将相同数据写入阵列的每个磁盘来提供冗余，简单且数据高度可用，但是成本相对高

RAID 5—RAID 0和RAID 1的折中，如果某磁盘故障，可根据其他磁盘上的校验数据来重建损坏数据

创建RAID 1阵列

创建：sudo mdadm --create /dev/md0 --level=1 --raid-devices=2 /dev/sdd /dev/sde

检查：sudo mdadm --detail /dev/md0 / cat /proc/mdstat

使用RAID建立文件系统：sudo mkfs -t ext4 /dev/md0

模拟磁盘故障：sudo mdadm /dev/md0 --fail /dev/sdd

移除故障磁盘：sudo mdadm /dev/md0 --remove /dev/sdd

添加新磁盘：sudo mdadm /dev/md0 --add /dev/sdf

创建RAID 5阵列

创建：sudo mdadm --create /dev/md1 --level=5 --raid-devices=3 --spare-device=1 /dev/sdf /dev/sdg /dev/sdh /dev/sdi

磁盘配额

配额限制值分为两种：硬性限制值、软性限制值。硬性限制值：绝对不允许超过的值，软性限制值：在宽限期内可以暂时超过的一个限制值；超过宽限期不可超过这个限制值。

EXT4文件系统磁盘配额

创建文件系统时启用配额：sudo mkfs.ext4 -O(创建时添加指定的特性) quota /dev/vg1/lv1

启用配额强制：

sudo mkdir /mnt/data3 +sudo mount /dev/vg1/lv1 /mnt/data3+ sudo quotaon /mnt/data3

为用户分配配额：sudo edquota testuser

为软限制设置宽限期：sudo edquota -t

关闭文件系统配额：sudo quotaoff
```


