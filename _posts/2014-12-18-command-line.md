---
layout:     post
title:      常用命令
category: blog
description: 使用命令行再配合着item在mac上能显著的提高工作效率，这些事常用的一些命令
---
* `ls` 列出当前目录下的所有文件
    > `ls -l` 列出可见的所有文件以及更多的文件信息
`ls -a`  列出所有文件（包括隐藏文件）
`ls test*\?.txt` 列出与之匹配的文件，（*|?都是元字符）*可以匹配0个或多个任意字符，？可以匹配一个字符

* `cat filename ` 查看文件内容
* `cat -b filename ` 查看文件内容并显示行号
* `wc filename ` 统计当前文件的行数，单词数和字符数
* `cp source_file destination_file` 复制文件
* `mv old_file new_file` 移动文件（如果操作是在一个文件夹，相当于重命名文件）
* `mv filename newfilename` 重命名文件
* `mv olddir newdir` 重命名目录  
* `rm filename` 删除文件
* `rm -rf dir` 删除文件夹内所有内容（谨慎操作）
* `rm filename1 filename2 filename3` 一次删除多个文件
* `cd ~` 进入主目录
* `cd ..` 返回上级目录（.指当前目录，..指上级目录）
* `cd (/)test` 进入test目录（/表示根目录，以/开头称为绝对路径，表示当前文件与根目录的关系，不以/开头称为相对路径，表示文件与当前目录的关系） 
* `pwd` 当前所在的目录
* `touch filename` 创建一个空文件
* `mkdir dirname` 创建目录（后跟多个目录名可以一起创建多个目录）
* `mkdir -p /tmp/dir/test` 增加-p选项可以一级一级创建所需目录，即使上级目录不存在
* `rmdir dirname` 删除目录（后跟多个目录名可以起删除）
* `grep pattern file(s)` 使用正则搜索文本
* `ls -l | grep "test"` 只显示包含“test”的文件（|把两个命令链接起来，前一个命令的输出作为下一个命令的输入，这叫做管道）
* `ls | grep -v "test"` 反转查询，输出不匹配的行
* `ls | grep -i "test"` 不区分大小写进行匹配
* `ls -l | grep "test" | sort -n` 按数字大小排序（-r 降序排序，-f 不区分大小写）
* `ls -l | grep "test" | sort -n | more` 分页显示（space可以查看下一屏，b键查看上一屏）
* `ls &` 创建后台进程在命令后加&即可
* `ps` 查看进程的运行状态（-f选项查看更多信息，f是full的意思，-a显示所有用户的所有进程，-x显示无终端的进程，-e显示所有进程）
* `kill pidnumber(pid编号)` 结束进程（Ctrl+c也是结束进程，Ctrl+z暂停进程）
* `top` 显示进程的更多信息（物理内存，虚拟内存，cpu使用率等）
* `uptime` 获取主机运行时间
* `jobs -l` 当前任务包含的进程
* `fg %jobnumber`  将后台任务调到前台
* `bg %jobnumber` 将前台任务调到后台