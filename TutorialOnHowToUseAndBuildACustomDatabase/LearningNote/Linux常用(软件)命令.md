# 1. 常用软件命令

- Tree
```
tree -L                             #列出目录级别（后接数字1,2,3...）
##   tree乱码解决办法
alias tree='tree --charset ASCII'   #通过vim添加到.bashrc
```

- Conda常用命令
```
##安装命令
conda install
conda install -y         #全程同意 yes
conda list
conda search

##环境命令
conda info --env
conda create -n 环境名    #创建环境
conda create -n qiime2 --clone qiime     
conda env remove -n qiime      #重命名=clone+remove，两步
conda remove -n rcnn --all   #删除环境
conda env remove -n        qiime2-2020.11 
conda activate
conda deactivate

conda config --show-sources  ##镜像源查看

##conda无法安装软件解决办法
conda clean   #清空环境中的缓存
把 ~/.condarc 中的 https 改成 http
删除conda后重新下载一个
```
- conda安装常用软件
```
##安装qiime1
conda install biom-format
conda install bioconductor-microbiome
conda create -n qiime1 python=2.7 qiime matplotlib=1.4.3 mock nose -c bioconda
##安装qiime2
conda create -n qiime2 python=2.7 qiime matplotlib=1.4.3 mock nose -c bioconda
##bioconda频道安装seqtk，seqkit，taxonkit
conda install  -c bioconda seqtk seqkit taxonkit
##其他频道安装软件
conda install -c daler sratoolkit
conda install -y -c hcc aspera-cli
```

- vim常用设置
```
set number      #设置行号

## 永久设置行号  
vim ~/.vimrc
输入  set number   
wq            #保存并退出
source ~/.vimrc    #更新

##设置查找高亮 
```

- rsync文件迁移
```rsync -aP -e "ssh -p 8888" data.zip t03022@biotrainee.vip:/home/data/t0203/
rsync -aP -r -e "ssh -p 8888" * t0203@biotrainee.vip:~/ncbi_db/taxonkit_test    ## -r带文件夹
--exclude='*.txt'   排除某些目录或文件
```
- Wget -小技巧
```
-c                     #断点下载
-i 文件名              #批量下载，文件里按行放多个url
-b                    #后台下载
-r                    #下载文件夹
tail -f wget-log      #可以实时查看下载进度
cat wget-log          #查看当前进度
```

- Apache 命令行操作（windows系统下，以管理员运行cmd）
```
F：    #切换盘符
cd F:\Server\apache2.2\bin #进入apache的bin目录
httpd.exe #看httpd.exe是否能运行（命令是否有效），必须在bin目录下
httpd.exe -M # 查看使用的模块，得到如下结果
    httpd.exe: Could not reliably determine the server's fully qualified domain name, 
    using 192.168.11.1 for ServerName
    Loaded Modules:
     core_module (static)            #static表示静态加载：apache启动就加载好了
     actions_module (shared)         #shared表示动态加载，使用到才会加载
        Syntax Ok
 ```

- Sort与Uniq
```
sort testfile  -o outfile    #输出文件
sort testfile -k 2     #按第二列值进行重排

uniq testfile    #删除重复行，只保留一行
uniq -c testfile    #检查文件并删除文件中重复出现的行，并在行首显示该行重复出现的次数
-u或--unique 仅显示出一次的行列
sort  testfile1 | uniq    #常用
sort testfile1 | uniq -c   #统计各行在文件中出现的次数
sort testfile1 | uniq -d    #在文件中找出重复的行
```

- Csvtk
```
##根据带表头的csv文件提取信息
cat 3.C.m.z.acc.taxid |csvtk -t grep -f accession.version -P 3xia.genus.nt.acc > 3xia.genus.nt.acc.taxid2
```

- Cut
```
cut OPTION... [FILE]...
选项：
-f : 通过指定哪一个字段进行提取。cut命令使用“TAB”作为默认的字段分隔符。
-d : “TAB”是默认的分隔符，使用此选项可以更改为其他的分隔符。
--complement : 此选项用于排除所指定的字段。
--output-delimiter : 更改输出内容的分隔符。
cut -d ':' -f 1 /etc/passwd   #指定分隔符为：
cut -d ':' -f 1,6     #指定分隔符为：，并提取第1和6列
/etc/passwd|cut -d ':' -f 1-4,6,7
cut  --complement -f 2   #打印除第二列的内容
```

- passwd修改密码
```
passwd  ##修改root用户密码
passwd hadoop  ##修改hadoop用户密码
```

# 2. 常用压缩解压命令
```
tar -zxvf #解压.gz文件
gzip -d   #解压.gz文件
gzip -dv * #解压文件，并列出详细信息
unzip  file.zip   #解压.zip文件
tar -jxvf  文件名 -C 存放的目录    #解压tar.bz2文件

zcat file.gz     #不解压缩文件的情况下，显示压缩包中文件的内容
zcat -l file.gz   #获取压缩文件的属性（压缩大小，未压缩大小，比率--压缩率
```

# 3. 创建、删除、复制、文件或文件夹
```
mkdir -p        #同时创建多个文件夹，*mkdir -p biosoft scr bin
                #若文件存在，也不会报错
rmdir 
rmdir -rf
cp a/* b       #复制a文件夹下全部文件（不含文件夹）到b文件夹
cp -rvf a/* b  #复制a文件夹下全部文件（含文件夹）到b文件夹
```

- linux 文件取交集、并集
```
（1）两个文件的交集，并集
前提条件：每个文件中不得有重复行
cat file1 file2 | sort | uniq -d > file3  #取出两个文件的交集（只留下同时存在于两个文件中的文件）
cat file1 file2 | sort | uniq > file3   #取出两个文件的并集（重复的行只保留一份）
cat file1 file2 | sort | uniq -u > file3    #删除交集，留下其他的行

（2）两个文件合并
cat file1 file2 > file3   #一个文件在上，一个文件在下
paste file1 file2 > file3  #一个文件在左，一个文件在右

（3）一个文件去掉重复的行
sort file | uniq        #注意：重复的多行记为一行，也就是说这些重复的行还在，只是全部省略为一行！
sort file | uniq -u      #把重复的行全部去掉，也就是文件中的非重复行！

（4）join 按两个文件的相同字段合并   
 join  option  file1  file2  ##默认情况是把输入的第一个字段作为连接字段，字段间用空格隔开
 ##参数说明
 -a 文件号   #输出文件中不匹配的行，文件号可选值1或2，1代表文件1,2代表文件2
 -i         #比较字段忽略大小写
 -1 字段     #以第1个文件的指定字段为基础进程合并
 -2 字段     #以第2个文件的指定字段为基础进程合并
```

# 4. 文件传输
- Xshell文件传输
```
rz               #传入到服务器
sz 文件名        #从服务器传出文件
```

- SCP文件传输
```
scp -P 6654 all.sra.gz  gmb14@81.70.205.254:/home/data/gmb14/weixy 
## scp挂后台步骤
#1 输入scp命令，Ctrl+z暂停
#2 jobs查看任务号
#3 bg %5，将任务号为5的scp命令挂到后台
#4 jobs继续查看任务
```

- 奶牛快传-cowtransfer-uploader
```
wget https://github.com/Mikubill/cowtransfer-uploader/archive/master.zip
... #省略过程
cp cowtransfer-uploader /usr/bin/

cowtransfer-uploader
cowtransfer-uploader -s #上传文件夹并生成一个链接
```

# 5. 查看磁盘，进程，系统信息
- 进程、任务、后台
```
killall httpd           #杀掉所有httpd的进程
kill                    #删掉进程
pkill [选项]  name      #杀掉所以name的进程
pkill -u gmb14          #杀掉所有gmb14用户的进程
killall -u gmb14        #杀掉所有gmb14用户的进程

ps -ef | grep httpd     #查看httpd进程
pstree -p 4007250 |wc -l   #查看进程pid：4007250所用线程数
ps -ux    #查看当前用户运行的进程

##cpulimit限制cpu使用
nohup cpulimit -i -l 5000 qiime dada2 denoise-paired --i-demultiplexed-seqs..... &设置5000%的cpu使用率，即50个线程？  

top                     #查看进程，可按字母C、I、Q、U等
&            #程序放到后台
jobs         #查看后台任务
bg、fg   %数字   #前后台任务切换
nohup        #不挂起
nohup command &

screen      #让任务永不掉线，在后台运行
screen -S 终端名    #创建终端
screen -ls        #查看正在后台运行的终端
screen -r  终端名   #登录某一个在后台运行的screen终端：
-d   <作业名称> 　将指定的screen作业离线。

disown      #后台挂载  
-h 	#标记每个作业标识符，这些作业将不会在shell接收到sighup信号时接收到sighup信号
-a  	#移除所有的作业
-r 	#移除运行的作业

ctrl+z      #暂停任务
ctrl+c      #终止任务
ctrl+d      #终止信号
exit        #退出登录
```

- 磁盘信息
```
fdisk
fdisk -i
fdisk -l

df     #列出不同分区的概要信息、挂载点、已用的和可用的空间。
df -H
df -a
du -sh ~    #查看剩余大小

free      #查看系统中使用的、闲置的和 RAM 的总体数量
free -m   #查看内存

quota -uvs  #查看磁盘大小
quota -uvs gmb14    ##查看用户配额

ncdu    ##交互式查看文件占用内存
```

- 系统信息
```
lscpu    #够查看 CPU 和处理单元的信息
uname
```

 - 系统语言
```
Linux怎么查看设置系统语言包：
查看当前系统语言    登陆linux系统打开操作终端之后，输入  # echo $LANG
查看安装的语言包    查看是否有中文语言包可以在终端输入   # locale
如果没有中文语言    可以通过网上下载安装中文语言包       # yum groupinstall chinese-support
如何修改系统语言为中文    临时更换语言           输入    # LANG="en_US.UTF-8"   中文为（"Zn_CN.UTF-8"）
修改系统默认的语言       # vi /etc/sysconfig/i18n
```

# 6. 命令行键盘操作

```
##  删除操作
ctrl + w    #往前删除一个单词，光标放在最末尾
ctrl + k    #删除到末尾，光标放在最前面（可以使用ctrl+a）
ctl + u     #删除光标以前的字符
ctl + k     #删除光标以后的字符
##  移动操作
ctl + a     #移动光标到字符头
ctl + e     #移动光标到字符尾
ctrl+方向键  #左右跳一个单词
ctl + l     #清屏
```

# 7. 元字符
```
双 && 符号，表示接下来的命令
./configure && make && make install #安装编译软件时可用
&& 逻辑与，和DOS批处理的用法一模一样只有前面执行成功才会执行后面的命令。
与此对应的是逻辑或||,只有前面执行失败才会执行后面的。
```

# 8. 软件安装相关
-    --prefix，编译的时候指定路径
```
Configure是一个可执行脚本，它有很多选项，在待安装的源码路径下使用命令
./configure –help    输出详细的选项列表。
prefix   选项是配置安装的路径
如果不配置该选项：
        安装后可执行文件默认放在/usr/local/bin，
        库文件默认放在/usr/local/lib，
        配置文件默认放在/usr/local/etc，
        其它的资源文件放在/usr/local/share，比较凌乱
如果配置prefix，如：
./configure --prefix=/usr/local/test可以把所有资源文件放在/usr/local/test的路径中，不会杂乱
卸载软件时可直接删除文件夹
```

# 9. 网络安全相关
- 网络与端口
```
ifconfig     #查看当前的网络配置
netstat -anp  #来查看哪些端口被打开
netstat -ntlp   //查看当前所有tcp端口
netstat -ntulp | grep 80   //查看所有80端口使用情况
netstat -ntulp | grep 3306   //查看所有3306端口使用情况
lsof -i:80 查看80端口是否被占用
```

- 防火墙相关
```
iptables防火墙
service iptables status   # 查看防火墙状态
service iptables stop  # 停止防火墙
service iptables start  # 启动防火墙
service iptables restart  # 重启防火墙
chkconfig iptables off  # 永久关闭防火墙
chkconfig iptables on  # 永久关闭后重启
```

# 10. 查找统计查看文件相关
- 程序应用查找
```
用如下查找命令，可以找到程序的路径：
find / -name xxx  
whereis mysql  用于程序名的搜索    which 用于命令的搜索
rpm -qa | grep mysql  查看是否安装该软件
rpm -ql  mysql | grep '/etc'  查看软件包安装的/etc相关的路径，不加过滤条件就是显示所有的 
```

- 统计、查找文件或文件夹
```
wc            #统计文件信息，行数、字数，以及字节数
wc -l         #统计行数
du -sh banzuige   #查看banzuige文件夹大小
ls -lht   #以M为单位显示文件大小
du -h –max-depth=1 *     #查看当前目录下各文件、文件夹的大小
du -h –max-depth=0 *    ##只显示直接子目录文件及文件夹大小统计值
```

- 查找文件内容
```
grep  -i "chr" OM_finna_chr.fa      #不区分大小写查找文件内容
grep -nr  -i "chr" OM_finna_chr.fa      #-n 显示行号
locate filename   #查找文件
zcat dead_nucl.accession2taxid.gz | grep 'HM853760.1'      ##查找.gz文件内容
```
- 查找大文件或目录
```
du -sh [dirname|filename]
   当前目录的大小：
　　du -sh .
　　当前目录下个文件或目录的大小：
　　du -sh *
　　显示前10个占用空间最大的文件或目录：
　　du -s * | sort -nr | head
  
  find . -type f -size +100M    #查找大于100M的文件
```

- 查看文件命令
```
less -S 文件    #行过长时间将超出部分舍弃
```

# 11. 目录栈命令
```
dirs	        #显示栈中目录名称（home目录显示为~)
dirs -l	        #显示栈中目录名称（home目录显示为完整路径名）
dirs -v	        #显示栈中目录名称（每行一个，并有数字标识）
pushd 目录名称	#将指定目录压入栈中，并改变工作目录（栈顶改变）
pushd +n	        #经目录#n移到栈顶，并改变工作目录（栈顶改变）
popd	        #弹出栈顶，并改变工作目录（栈顶改变）
popd +n	        #从栈中移除目录#n
dirs -c	        #除工作目录外，移除目录栈中所有目录  
ps：dirs -c         #会清空栈，但是永远不会看到一个完全空的栈，因为栈顶永远是工作目录。

cd -        #返回刚才那个目录
```

# 12. Shell编程基础
```
循环语句
l=COI.Eu; for i in {8,9} ;do qiime taxa barplot --i-table ../filtered-table.qza --i-taxonomy ${l}.B.${i}.taxonomy.qza \ 
--m-metadata-file ../sample-metadata.tsv  --o-visualization ${l}.B.${i}.taxa-bar-plots.qzv ;done
```   
    
