
# 1. 常用命令

```
##将lab文件转换成acc,即取第一列，同时将'>'替换为''
cat nt.lab | awk '{print $1}' | sed -e 's/>//' > nt.acc
##提取acc.taxid.tax中的acc.tax并将空格分隔转换为Tab分隔
awk '{print $1,$3}' 3xia.genus.Eu.acc.taxid.tax2 |sed 's/ /\t/g' > 3xia.genus.Eu.acc.tax

```

# 2. grep

- 统计fasta文件中序列的条数；可分列显示文件名与行数，推荐使用

```
grep -c ">"  gene.ffn

```

- 输出满足条件的序列；

```
grep -A 2 "3 gi 29732 34486" lastz.axt

```

- 筛选出不满足条件的内容；

```
ps -fx | grep -v "S"         #不输出进程状态为S的任务。

```

- 模糊搜索，输出单词而不是行

```
cat COI.grep |grep -oh '\w*mito\w*'  

```

- 按列表文件搜索，相当于grepLabByList.sh，速度更快

```
grep -f list.file  lab.file > grep.file
grep  -F -f list.file  lab.file > grep.file   ##-F 可以忽略正则表达式字符，直接用原始字符进行匹配，类似R中的fixed =T，速度极快

```

# 3. sed

- 输出固定的行

```
sed -n '1307p'  seq.fna       #输出文件第1307行；
sed -n '100,200' seq.fna      #输出文件第100到200行；

```

- 替换操作

```
sed -e 's/gi/GI/' seq.fna                    #将文件中gi全部替换为大写GI；
sed -i 's/gi/GI/g' seq.fna                   #在原文件上进行替换，并且进行全部替换；
sed -i.bak 's#GI#gi#' seq.fna                #在原文件上进行替换，并进行备份；
sed -e 's/gi/GI/2；s/ref/REF/2' seq.fna      #只将第二次出现的gi和ref进行替换；
sed -f sed.list cds.list                     #根据文件中的模式进行替换，可同时进行多条件替换；
sed -n 's/gi/GI/p' seq.fna                   #打印发生替换的行；

sed 's/ //g' filename >filename_new      #将linux文件中的tab更换为空格
sed 's/ /\t/g' filename >filename_new      #将linux文件中的空格更换为Tab分隔

```

- 删除空白行；

```
sed -e '/^\s*$/d'  seq.fna     #删除文件中的空白行；

```

- 行寻址

```
sed -n '/ref/p' seq.fna                #输出文件中包含ref关键字的行；
sed  '100,2000s/GI/gi/g' seq.fa        #则只替换100行到2000行的内容；
sed  '100,2000！s/GI/gi/g' seq.fa      #加感叹号取反，在这个范围之外的执行操作；

```

- 删除操作+删除特定行

```
sed '3d' a.txt ##删除第三行
sed '1,3d' a.txt ## 删除1到3行
sed '1d;3d' a.txt ## 删除第一行和第三行
sed '$d' a.txt  ## 删除最后一行

sed -e '/sssss/d' seq.fna     #删除包含s的行；

sed '/^5/d' a.txt        ## 删除以5开头的行
sed '/^[35]/d' a.txt     ## 删除以3或者5开头的行
sed '/h$/d' a.txt        ## 删除以h结尾的行
sed '/[hw]$/d' a.txt    ## 删除以h或者w结尾的行
sed '/^2.*g$/d' a.txt   ## 删除以2开头，同时以g结尾的行

sed -e 's/:.*//g' seq.fna         #删除冒号之后的所有内容；

```

- 对应替换，类似于tr的功能

```
sed -e 'y/ATCG/atcg/' seq.fna            #修改大小写
sed -e '/>/!y/ATCG/atcg/' seq.fna        #DNA序列反向互补配对，并修改大小写

```

- 通常要求序列名字行简单为好，而且一般加chr作为开头

```
# 给第一行添加chr标签，并去掉其他多余信息
# 下面的写法复杂了些，是为了避免给已经有chr信息的名字再加一次，帮助无脑操作
sed 's/^>\([^chr]\)/>chr\1/'  Homo_sapiens.GRCh38.dna.primary_assembly.fa |cut -f 1 -d ' ' > GRCh38.fa

```

# 4. awk

- 输出一个列表任意列

```
awk '{print $1}' blast_m8.out               #输出blast m8 格式结果的第一列；
awk -F ":" '{print $1,$NF}' passwd.list     #通过-F修改默认分隔符为冒号，输出第一列与最后一列；

```

- 删除特定列

```
awk '{$2 = "\b"; print $0}' a.txt     #不留空格

```

- 格式转换

```
#将短序列比对上的reads输出出来，生成fastq文件；
 awk '{print"@" $1"\n"$10"\n""+\n"$11""}' all.sam 

```

- 过滤blast结果

```
#过滤blast比对结果，将identity 大于80，并且比对长度大于100bp的结果输出；
awk '{if ($3>=80 && $4>=100) print $0}'  blast_m8.out  

```

- 比较

```
awk '$8>$10' input.txt #输出第8列大于第10列的行。

```

- 匹配输出

```
awk '$0~ /wang/{print $0}' passwd.list   #利用正则表达式，将秘密表中姓wang的账户都输出出来；

```

- 格式化输出

```
awk 'BEGIN{print "The Program Begin\n"}{if ($3>=80 && $4>=100) print $0}END{print " The Program End\n"}' input.txt  
#利用BEGIN和END关键字生成报告；

```

- 修改字段和记录分隔符

```
awk 'BEGIN{OFS="\t"}{print $2,$4,$5}' input.txt   #在BEGIN中设定字段分隔符和记录分隔符；
awk编程计算
awk '{x+=$3}END{print x/NR}' input.txt   #计算第三列的平均值，最后在END将其输出出来
awk编程比较大小
awk   'BEGIN { max=100 ;print "max=" max} {max=($1 >max ?$1:max); print $1,"Now max is "max}' input.txt  
#取得文件最后一个域的最大值

```

- awk编程求和

```
awk '{print $0,$3+$4}' input.txt  #计算第3列和第4列的和

```

- 输出固定行内容

```
awk 'NR>=20&&NR<=80' input.txt  #输出第20到第80行内容

```

- 合并文件

```
awk 'BEGIN{while((getline<"file1")>0)l[$1]=$0}$1 in l{print $0"\t"l[$1]}' file2
  #将两个文件按列合并起来，类似jion命令的功能。

```

- 去重复

```
awk '!($0 in a) {a[$0];print}' input.txt  # 打印不重复的行，类似uniq的功能;
awk '!($2 in l){print;l[$2]=1}' input.txt #计算第二列内容非冗余的次数，类似于uniq的功能;

```

- 统计字符

```
awk '{for(i=1;i!=NF;++i)c[$i]++}END{for (x in c) print x,c[x]}' input.txt 
#计算每个字符出现的次数，类似wc的功能。

```

- 替换

```
awk '{sub(/test/, "no", $0);print}' input.txt    #进行替换，类似sed的功能，
fastq转换为fasta
awk '{getline seq;getline plus;getline qual;sub("@",">",$0);print $0 "\n"seq}'  test.fastq
AWK处理gff文件常用命令
#awk统计gff文件第九列有哪些注释信息
awk 'BEGIN{FS=OFS="\t"} $3=="gene"{split($9, a, ";"); for(i in a){split(a[i], b, "="); if(++c[b[1]]==1) print b[1]}}' GCF_000001735.4_TAIR10.1_genomic.gff 
#awk取出第九列注释信息
awk -F "\t" '$3=="gene"{print $9}' GCF_000001405.33_GRCh38.p7_genomic.gff | cat -n | less
#提取ID，Name，gene_biotype, Dbxef等信息，以tab分隔输出到gff文件中，部分基因不包含某些注释信息，如LOC105379212基因没有deion信息，则在对应列为空字符。
awk 'BEGIN{FS=OFS="\t"} $3=="gene"{print $0}' GCF_000001735.4_TAIR10.1_genomic.gff | 
sed 's/;/\t/g' | 
awk 'BEGIN{FS=OFS="\t"} {for(i=1; i<=NF; i++){split($i, a, "="); b[a[1]]=a[2]}} {print b["ID"],b["Name"],b["gene_biotype"],b["Dbxef"]} {split("", b, ":")}' 
> tiqu.gff

```

# 5. Seqtk软件使用

- 软件使用

```
seq    主要功能都在这个选项中，也是最常用的一项
sample    用于抽样
subseq    提取序列
fqchk    fastq质量评估
mergepe    合并pairend reads
trimfq    很明显是截取fastq
hety    计算某个区域杂合性，筛选杂合位点
gc    识别高低gc区域
mutfa    标记出高变区
mergefa    合并fastq或者fasta文件
famask    屏蔽fasta文件，比如将重复区用字母替换为X，这些区域不参与变异检测
dropse    丢掉不是pair end的reads
rename    修改序列ID，比如将ID中的chr全部去掉
randbase    随机选取碱基
cutN    根据N区域截取序列
listhet    提取杂合位点的位置，DNA序列中，可以用非ATCGN的字母表示杂合位点，listhet可以将这些位点位置列出来。

```

- 统计

```
seqkit stats Trinity.fasta

```

- 截取序列

```
# 切除reads的前5bp，以及后10bp：
seqtk trimfq -b 5 -e 10 in.fq > out.fq

```

- 格式化

```
#所有序列显示在一行
seqtk seq -l 0 gene.fna  > new.gene.fna
#每行显示50个碱基
seqtk seq -l 50 gene.fna
对fq文件中的reads进行随机抽样
seqtk sample -s 100 read1.fq 10000 > sub1.fq
seqtk sample -s 100 read2.fq 10000 > sub2.fq
-s后面跟随机seed，对于双端测序的reads，必须使用一样的seed，不然得到的sample无法正确pair
seqtk sample -s 10 test.fq 0.4   ##使用随机种子（-s，保证重复性）提取一定比例（0.4）的子序列

```

# 6. Seqkit软件使用

```
seqkit rmdup [flags]    ##移除重复序列 by id/name/sequence
Flags:
  -n, --by-name                by full name instead of just id
  -s, --by-seq                 by seq
  -D, --dup-num-file string    file to save number and list of duplicated seqs
  -d, --dup-seqs-file string   file to save duplicated seqs
  -h, --help                   help for rmdup
  -i, --ignore-case            ignore case

cat fastfile | seqkit rmdup -s > duped.fasta      #用法

```

# 7.  数据下载

```
efetch -db=nuccore -format=fasta -id=AF086833 > AF086833.fa

```

# 8. bioawk使用

- 统计GC含量

```
bioawk -c fastx '{print $name, gc($seq)}' input.fa
#结果得到 chrX 0.384356

```

- 将fa中的注释行和序列行分开处理

```
#将注释行和序列行添加分隔符tab即可，这样打开就是表格形式，注释行在第一列，序列行在第二列，导出为excel都不怕
bioawk -t -c fastx '{print $name,$seq}' input.fa #加上 > output.fa另存为

```

# 9. Fastq文件统计

```
# 计算read数
wc -l: 计算行数
bc -l: 计算器 (-l：浮点运算)
# 为什么除以4，又除以1000000，计算的是million值
echo "`zcat trt_N061011_1.fq.gz | wc-l` / (4*1000000)" | bc -l
#测序碱基数计算
zcat trt_N061011_1.fq.gz | awk'{if(FNR%4==0) base+=length}END{print base/10^9,"G";}'

```
