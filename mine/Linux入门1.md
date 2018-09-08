#Linux
[youtube视频](https://www.youtube.com/watch?v=wBp0Rb-ZJak&t=1039s)
##系统安装
* [desktop版本ubuntu](https://www.ubuntu.com/index_kylin )
* [virtualbox](https://www.virtualbox.org/)

##linux系统分支介绍

* "The terminal is the translation between sotfware and hardware"

##Package manage
###time 1h42M--1h58m
* "How to install package,how to uninstal package on Ubuntu "
* apt-get means to intall applications,"apt-get install bluefish".  "apt-get remove bluefish"
* apt-cache serch name ---- 搜索软件关键字
* apt-cache policy name ---- 是否安装？是否candidate？
* sudo dpkg -i *.deb ---- 安装deb文件application 
* sudo apt-get upgrade ---- upgrade all packages that has new versions and resprotoies on the system
*

##文件权限管理
###time 1h58M--2h10m
* chown ----  change ownership of the file
* chome ----  change permisson of the file , 4 means readble ,6 means readable and writable , 7 means readable writeable executable
* rm file.name ---- remove file
* mkdir directoryname ---- make ditectory
* touch file.name ---- create file.name

##文件操作：remove、copy
###time 2h10m--2h15m
* rm dir/* -- remove all file in dir
* rm dir/*.cpp -- remove all *.cpp file in dir
* rm rf dir -- remove dir dierctory 
* cp -- copy files
* mv -- move file

##mv cp 细节操作
###time 2h15 m--2h24m
* mv oldDirectory newDirectory -- renam directory
* cp oldFile newFile -- copy file

##find command in terminal
###time 2h24m--2h36m
* find . -type f -iname "\*.php" -- 当前目录找“*.php”的文件
* find . -type f -not -iname "\*.php" -- 当前目录找不是“*.php”的文件
* find . -type d -iname "dir" -- 当前目录找“dir”的目录
* find /etc -type f -iname "\*.conf" -- /etc目录下所有的配置文件
* find . -type f -perm 0664 -- 当前目录权限为0664的文件吗
* find . -size +1M -- 当前目录size大于1M的文件 
* /etc文件见contain basic config file for applications 
* find . -maxdepth 1 -type f -iname "*.conf" -- 查找文件深度为1

##grep command to find things in files
###time 2h36m--2h45m
* grep -i "function" file.php -- find "function" in file.php ,-i -- mean ignre case
* find . -type f -size -10k -iname "*.php" -exec grep -i -n "function" 

##查看、杀死进程 top、ps aux、pgrep、kill
###time 2h45m--2h52m
* top -- to see processes
* ps aux -- print all the processes on the moment you run the command
* ps aux | grep liri-browser -- print all the precesses that isrunning which contain liri-browser
* pgrep lisr-browser
* kill -9 pid -- kill the process with id pid
* killall processname --  

##services
###time 2h52m--2h59m
* elasticsearch


##services
###time 2h59m--3h4m
* crontab

##ide
###time 3h4m--3h13m
* ides intructions 
* install eclipse

##jetbrains ide python pycharm
###time 3h13m--3h17m
* install jebrains ides python pycharm

##vcs git
###time 3h17m--3h49 m
* 1、git init
* 2、在github创建仓库
* 3、git remote add origin url
* git config --global user.name "hanhongyun"
* git config --global user.email "xuzhouhhy@126.com"
* git add -A    添加全部搭配vcs 

##meteor
###time 3h49m--3h55m
*virtual box 无法翻墙，安装失败
 
##meteor
###time 3h55m--4h42m
* One project with react on meteor


##Apache2 php5 
###time 4h42m--4h51m
* install apache2 php5 mysql
* write index.php


##Apache config
###time 4h51m--5h16m
* try apache2
* 


##php sql
###time 5h16m--5h24m
* php sql

##account manage -- add user
###time 5h57m--6h5m
* sudo adduser username -- add user
* sudo add username sudo -- adduser to sudo

##account manage -- delete user
###time 6h5m--6h6m
* sudo deluser username -- delete user


##account manage -- change user password 
###time 6h6m--6h7m
* sudo passwd username -- change password

##account manage -- create new groups 
###time 6h7m--6h11m
* sudo group add groupname -- add new group

##linux network 
###time 6h11m--6h40m
* sudo 
* netstat