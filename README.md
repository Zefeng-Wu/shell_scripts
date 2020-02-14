# shell_scripts
过滤基因组测序数据trim_galore

    export PAHH=$PATH:/share/nas1/fansh/wuzefeng/softs/cutadapter/bin
    ./trim_galore --phred33  --fastqc  -a  AATGATACGGCGACCACCGAGATCTACACTATCCTCTTCGTCGGCAGCGTC  -a2 CAAGCAGAAGACGGCATACGAGATTTCTGCCTGTCTCGTGGGCTCGG -o /share/nas1/fansh/kp-genome/trim --paired -t /share/nas1/fansh/kp-genome/new_miseq_paired_samll_than_300bp/2783_5992_8703_H8PKBADXX_i2_AGGCAGA_TATCCTC_R1.fastq /share/nas1/fansh/kp-genome/new_miseq_paired_samll_than_300bp/2783_5992_8703_H8PKBADXX_i2_AGGCAGA_TATCCTC_R2.fastq 

    ./trim_galore --phred33  --fastqc  -a  AATGATACGGCGACCACCGAGATCTACACTAGATCGCTCGTCGGCAGCGTC  -a2 CAAGCAGAAGACGGCATACGAGATTCGCCTTAGTCTCGTGGGCTCGG -o /share/nas1/fansh/kp-genome/trim --paired -t /share/nas1/fansh/kp-genome/old_mix_two_genome_miseq_paired/2_S2_L001_R1_001.fastq  /share/nas1/fansh/kp-genome/old_mix_two_genome_miseq_paired/2_S2_L001_R2_001.fastq


对基因组进行拼接spades.py

    nohup python spades.py  -o /share/nas1/fansh/kp-genome/assembly -1 /share/nas1/fansh/kp-genome/trim/2783_5992_8703_H8PKBADXX_i2_AGGCAGA_TATCCTC_R1_val_1.fq -2  /share/nas1/fansh/kp-genome/trim/2783_5992_8703_H8PKBADXX_i2_AGGCAGA_TATCCTC_R2_val_2.fq -t 30 -m 128 &


对植物pif1的fasta运行meme

    meme.bin  /share/nas1/fansh/wuzefeng/softs/hmmer-3.0-linux-intel-x86_64/pif1p_hmm_out/phytozome/phytozome_pif1-1e_-10_fasta/pif1_-10plant.fasta -o meme_plant_pif1_50.out -p protein -nmotifs 50 -maxsize 700000 


搜索基因组翻译后的pif1-motif

    hmmsearch -E 1e-5 -o  pif1p_hmm_out/phytozome/genome_translation/Zea_mays/Zea_mays_trans_pif1_align.out pif1p_hmm_out/phytozome/genome_translation/Zea_mays/Zea_mays.AGPv3.21.dna.genome.fa

    hmmsearch -E 1e-5 --domtblout  pif1p_hmm_out/phytozome/genome_translation/arabidopsis/ara_pif1_domain_table_out a.hmm  /share/nas1/fansh/wuzefeng/softs/hmmer-3.0-linux-intel-x86_64/pif1p_hmm_out/phytozome/genome_translation/arabidopsis/translation.fasta 

Blast常用命令

    formatdb -i genome.fast -p F  -n database_bame
    blastall -i query.fasta -d database_name -p blastn -e 1 -m 9 -o oufie


blast+

    makeblastdb -in test.fa -input_type fasta -dbtype nucl(prot)  #构建数据库
    blastp -db dbname -query test1.fasta -out test.all.blot -outfmt 7 -num_threads 8 -evalue 1e-5

Usearch7.0
1.小内存

    usearch -sortbylength seqs.fasta -output seqs_sorted.fasta -minseqlength 64
    usearch -cluster_smallmem query.fasta -id 0.9 -centroids nr.fasta -uc clusters.uc
2.大内存

    usearch -cluster_fast query.fasta -id 0.9 -centroids nr.fasta -uc clusters.uc

qsub 提交命令:

    qsub -cwd -q middle.q tesh.sh

##给文件加上title or header

    for m in $(ls test*);do echo -e "name1\tname2" | cat - $m > /tmp/out && mv /tmp/out $m;done


###### tophat批处理命令 ####
    find . -name "*_1.fastq" | xargs basename -s "_1.fastq" | xargs -P 2 -I{} tophat -p 1 -g 1 -N 2 -G /data1/wuzefeng/genome.db/maize/gtf/Zea_mays.AGPv3.31.chr.gtf -o /data2/WZF/TEST/{} --library-type fr-unstranded -I 30000 --no-discordant --no-mixed --no-coverage-search {}_1.fastq {}_2.fastq


find . ­name "*.sh" | xargs ­I{} sh {}

find /directory -name "*pattern*" | xargs awk '{z=FILENAME".txt";print $8>z}'

find -name "accepted_hits.bam" | xargs -I{} du -lhs {} | sort -n  #查看文件大小

find  -name "*.abinitio.gtf.gz" | xargs -n 1000 rm -f  #快速删除大量文件

*********tesh.sh*********************************************************************************************************
#!/bin/sh														*
#PBS -N jobname														*
#PBS -o /share/nas1/fansh/wheat-genome/OUT      #输出路径								*
#PBS -e /share/nas1/fansh/wuzefeng/ERR	#错误输出路径									*
#PBS -l nodes=1:ppn=6				#需要的节点数和CPU个数							*
cd /share/nas1/fansh/wheat-genome/programe										*
python helitron.py -s ../1AS-ab-k71-contigs.fa.longerthan_200.fa -o ../outcome/1AS.helend				*
*********tesh.sh*********************************************************************************************************

ps: -cwd :output runing logs in current dir

du -h --max-depth=1 /share/nas1/fansh/..... #查看服务器大文件

du -hs /export/home/username 
*************************************************************************************************************************
Sed 流编辑器（pwds）														*
一、替换:（s）
1.多个替换：将file文件中每行首次出现old1和old2分别用new1、new2替代
sed -e' 回车														*
> s/old1/new1/														*
> s/old1/new2/ ' filename >outfile

2.单一替换：
sed 's/new/old/'

3.指定行号替换：

ps： 替换范围 ：每行所有的：sed 's/new/old/g' #替换每行的所有
		行号：	sed 'n1,n2/new/old/'  #替换n1-n2行之间的
二、删除（d）
1.删除空白行：														*
sed /^$/d filename
2.指定行号删除：
sed 'nd' filename 或sed 'n1,n1d'
3.删除匹配行号：
sed '/a/d'  #删除匹配a的行（只要每行有一个匹配就删除）
sed -e '1b' -e '/^abc/d' test 	#删除匹配行，保留第一行	
		     
4.对所有文件删除特定的行
for m in $(ls *.ht); do sed -i '/warning/d' $m; done
5. 删除多个空格
 sed -e 's/ \+ /\t/g' -e s'/,//g' Ara_GO.txt_out.txt | cut -f1,3,5 >aaa
三、写文件（w）

1.sed 'n1,n2 w newfile' oldfile		      #把oldfile里的n1-n2行内容写到newfile中,不可使用定向符(不好用)
2. sed -n '1,3p' filename >outfile             #输出filename 1-3行


四、匹配功能（p）
1.从头匹配
sed -n '/object/p' filename			#搜索匹配字符的行并打印（p）；-n参数不加的话会打印原行内容
2.指定每行匹配的位置（^和$）
sed -n '/^object/p' filename			#搜索匹配行首是object的行，并打印
3.匹配其他
sed -n '/.at/p'  filename			# .代表除了换行符以外的所有字符后紧跟at
sed -n '/[0,1,2,3....][0,1,2,3...]/p'	        # 匹配多位【a-b】数字
sed -n '/ie*k/p' filename			#匹配ieek，ieeek之类的，多个e
sed -n '1,3p' filename >out			#匹配1-3行输出,
#注意单引号和双引号的区别,双引号和$连用时,会阻止其替换作用
 for m in $(ls *.sra| awk 'BEGIN{FS="."}{print $1}'); do sed -i "/${m}/d" list; echo $m;done  #根据当前路径下产生的文件,对原来的文件进行修改并删除
 sed -n '4~4p' SRR037881.fastq $ 打印4的倍数行
#修改fasta文件header　名字
sed 's/^\(>.*\)$/\1 Brassica rapa/' infile
awk '/^>/ {$0=$0 " Brassica rapa"}1' in.fa >out.fa　　　#1条件为真时
awk '/^>/{$0=$1}1'  ara.cds >ara.cds2
awk '{for (m=1;m<=NF;m+=3){printf $m"\t"}}{print "\n"}' test | sed -e /^$/d -e s'/\t$//g'
awk '$11+0<1e-5' parent2nocoding.blat.txt  > p2n_1e-5.txt  # 科学计数法
**************************************************************************************************************************

Sort排序
sort -t "分隔符" -k3n filename >outfile		#按以三列按数字排序
sort test | uniq -c| awk '{OFS="\t"}{print $1,$2}' # 统计频率并输出
*************************************************************************************************************************
Grep正则匹配
grep 【-a（文本方式匹配），-c(次数)，-i（忽略大小写），-n（输出行号），v（反向匹配），-w（完全匹配）, -o 只输出匹配部分】'object' filename >outfile 

grep 't[ae]st' filename				#匹配test或tast
grep '[^g]oo' filename				#匹配oo钱没有g的行（^放到[]里表示排除，而放到[]外表示开始）
grep '^[^a-z]' filname				#匹配开头不是a-z

1.匹配后只显示行号：grep -n -w 'items' filename | cut -d：-f1
2.根据id 提取fasta序列：grep -A1 -w “id” filename  #只对奇偶行排列时成立
**************************************************************************************************************************
交集并集差集
1.交集
sort a.txt b.txt |uniq -d  
grep -F -f  a.txt b.txt                     
2.并集
sort a.txt b.txt |uniq
3.差集
sort a.txt b.txt b.txt|uniq -u
4.对称差
sort a.txt b.txt |uniq -u
*************************************************************************************************************************
AWK 编辑:内建变量：NR:处理的当前行号；NF:当前行的字段数，默认分割是空格；FS；分割字符
awk '条件类型1{动作1} 条件类型2{动作2}' filename

awk '$1>=2 && $2<=3' filename  >out	
awk '$1=="AT2G38610" && $7=="+"'  fimo.gff			#输出第一列大于2并且第二列小于3的行
awk 'BEGIN {FS=":"} $3 < 10 {print $1 "\t " $3}' filename	#从一行开始【begin】，用【：】分割每行；分割后第三字段小于10；打印第一字段和第三字段
awk '{print NF}' filename #打印tab文件每行的列数
awk '!(NF != 3) {print}' file #打印列数不等于3的行
awk '$3 + 0 == $3 && $3 <= 2' file  #打印出某列是数字的行
awk 'u=split($1,m,"r"){print m[1],$0}' test.bed #分割字符串
awk '{if(FNR%2==1)print$0;else print substr($0,10,50)}' ERR990453_1.fastq  | head #处理奇偶行数据

awk '$3=="gene"{OFS="\t";u=split($9,m,";");n=split(m[1],q,":");print $1,$3,$4,q[2],$7}' Arabidopsis_thaliana.TAIR10.31.gff3  #提取基因行


##两个文件查找并join到一起
awk 'NR==FNR{a[$2]=$1;} NR!=FNR{print $1,a[$2]}' a.txt b.txt
## 读取第一个文件a,把第二列作为字典key,值为第一列; 然后读取第二个文件b,输出第一列和字典里第二列作为key对应的值

for m in $(ls *RR*/*.txt); do x=$(sed -n "14p" $m | grep -o "[0-9.]\+[0-9]"); if [ `echo "$x > 50.0" | bc` -eq 1 ]; then echo $x ;echo $m;fi; done
*******************************************************************************************************************************

echo substr(string,5) #截取字符串第5位以后的字符
echo ${str:start:end} #截取字符串start到end之间的字符串

awk '$1=$1' ORS=, file_with_many_lines >newfile   #将换行符替换为","号
xargs < test

##############根据run id下载sra数据
awk 'type=substr($0,1,3){pre_id=substr($0,1,6)}split($1,m,"."){print "/sra/sra-instant/reads/ByRun/sra/"type"/"pre_id"/"m[1]"/"$1".sra"}' SRR_Acc_List_ara.pair.txt

############## awk 去掉非染色体信息的染色体
awk '$2 ~ /^(1?[0-9]|2?[0-9]|X|Y|M)$/' file.txt  #提取1-23号染色体以及XY染色体

#################批量按照目录名重命名文件
for i in *; do mv "$i/result.pdf" "$i/$i.pdf" ; done
rename s'/_.*/.fa/' *.fa

#按文件名重命名

for file in ls(*.fa); mv $file $(echo $file| awk 'BEGIN{FS="."}{print $1}')

####根据文件名修改fasta header
for m in $(ls *.fa); do a=$(basename -s .fa $m); sed "s/ .*/_${a}/" $m; done

###################比较两个文件夹下文件是否一致
for m in $(ls /data1/wuzefeng/sra/tair/2fastq/*.fastq); do if [ ! -e $(basename $m) ];then echo $m; fi;done

**********************************************************************************************************************************
Split 分割大文件
split -l 500  filename
split -b 500k filename
split -l 40000000 prediction_set.txt --additional-suffix=.txt

R 语言运行脚本.R
1.R CMD BATCH test.R
2.Rscipt test.R
3.

******************************************************************************************************************
删除linix下的mysql

sudo apt-get autoremove --purge mysql-server-5.0
sudo apt-get remove mysql-server
sudo apt-get autoremove mysql-server
sudo apt-get remove mysql-common 
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P

重装mysql
sudo apt-get autoremove --purge mysql-server-5.0
sudo apt-get remove mysql-server
sudo apt-get autoremove mysql-server
sudo apt-get remove mysql-common //这个很重要
dpkg -l |grep ^rc|awk '{print $2}' |sudo xargs dpkg -P

安装 mysql
sudo apt-get install mysql-client-core-5.5
sudo apt-get install mysql-server
sudo apt-get install mysql-client
sudo apt-get install php5-mysql 

******************************************************************************************************************
开启apache：
netstat -ntl /usr/local/apache2/bin/apachectl start(stop)

添加用户和组;
1.建用户：
adduser phpq
passwd phpq
2.建工作组：
groupadd test
3.添加用户同时加入工作组
usadd -g test phpq -d /home
4.给已有的用户增加工作组
usrmod -G groupname usrname

5.删除账户
userdel peter
groupdel peter
***************************************************************************************
win7设置wifi
netsh wlan set hostednetwork mode=allow ssid=yourname key=12345

**************************************************************************************************************************
cd-hit 聚类去冗余
/path/.../cd-hit-est -i muti_fasta_file -o outfile -c 0.9 -s 0.9 -T 4 -d 0 -M 8000

-i 输入文件；-o 输出文件；-c 序列相似度阈值；-s consensus序列的覆盖度；-T 线程数；-d 序列名称（全）；-M 所需最大内存

6.sra-tool-kit 转换sra到fastaq（双端测序）
fastq-dump DRR003149.sra --split-3 

******************************************************BitBuck..............................
一.对已存在与BitBuck的代码修改后再上传
1. 打开BitBuck主页，找到project（复制clone语句）
2. 命令行执行Git clone语句
3.下载到当前目录位置
4. cd project/
5. git pull
5. 修改文件
6. git add . 
7. git commit -m 'This id the ** modified!'
8. Git push

二、新建project
1.在主页Creat
2.本地： mkdir project2
3. cd project2
4. vi myscript.py
5. git init
6. git remote add temp http://******project2.git
7. git add .
8. git commit -m 'First Edition'
9. git push -u temp master

*************************************************************************************************
#http://driverboss.com/canon/canon-i-sensys-lbp6230dw-driver-download/ 打印机驱动


#Mysql 添加表
create table命令用来创建数据表。

create table命令格式：create table <表名> (<字段名1> <类型1> [,..<字段名n> <类型n>]);
######################################################################################
##例如，建立一个名为MyClass的表：
##字段名	数字类型	数据宽度	是否为空	是否主键	自动增加	默认值
##id		int		4		否		primary key	auto_increment	 
##name		char		20		否	 	 	 
##sex		int		4		否	 	 	0
##degree	double		16		是
###############################################################################################	 	 	 
mysql> create table mytab1(
    > id int(4) not null primary key auto_increment,
    > name char(20) not null,
    > sex int(4) not null default '0',
    > degree double(16,2));


 insert into mytab1 values(1,"zhangshan","nan","master");



##Mysql 瘦身
/usr/bin/mysqldump -uroot -p111111 --quick --force --add-drop-database --all-databases --add-drop-table > /home/wuzefeng/mysql/mysqldump.sql #备份数据库到新建的目录里
sudo /etc/init.d/mysql stop #停止数据库

rm /var/lib/mysql/ibdata1 #root用户操作
rm /var/lib/mysql/ib_logfile*

sudo /etc/init.d/mysql start #然后启动数据库
/usr/bin/mysql -uroot -p4207035 < /home/wuzefeng/mysql/mysqldump.sql #还原数据库

##################################################################################################
搭建lamp
sudo apt-get install apache2  apache2-doc
#测试:http://localhost
#sudo gedit /etc/apache2/mods-enabled/dir.conf修改顺序

sudo apt-get install php5 libapache2-mod-php5
sudo service apache2 restart #重启
sudo vim /var/www/html/info.php #测试
sudo apt-get install mysql-server mysql-client
sudo apt-get install phpmyadmin
sudo ln -s /usr/share/phpmyadmin /var/www/html
#测试http://localhost/phpmyadmin
sudo php5enmod mcrypt


##########################linux 挂载iso文件##############################################################################
mount -o loop /mnt/yourpath/file.iso /mnt/cdrom


#################image to ASCII
jp2a --width=76 /home/wuzefeng/文档/u盘２/照片/IMG_0273.JPG 
################# tophat log结果reads 长度统计
for m in $(find -name "tophat.log"); do grep -H "left reads" $m; done | awk '{print $1,$5,$7}' | sort -k 2



##########circos
#1.conf文件：配置文件，调用其他数据
~/.circos -conf xx.conf --outputdir=~/Desktop
　
############### matlab安装
#1挂载
sudo mount -t iso9660 MATHWORKS_R2014A.iso /mnt
cd /mnt
./install
3.激活MATLAB
安装结束前，会要求输入License文件
选择Crack文件夹中的license_405329_R2014a.lic
将Crack/Linux文件夹中的libmwservices.so覆盖至安装目录：
$ sudo cp Linux/libmwservices.so /usr/local/MATLAB/R2014a/bin/glnxa64
安装完成。

4.创建快捷方式
将Matlab.png文件拷贝进/usr/local/MATLAB/R2014a
$ sudo cp Matlab.png /usr/local/MATLAB/R2014a/
建立以下文件
$ sudo vi /usr/share/applications/Matlab.desktop
输入以下内容:
[plain] view plain copy
[Desktop Entry]  
Type=Application  
Name=Matlab  
GenericName=Matlab 2014a  
Comment=Matlab:The Language of Technical Computing  
Exec=sh /usr/local/MATLAB/R2014a/bin/matlab -desktop  
Icon=/usr/local/MATLAB/R2014a/Matlab.png  
Terminal=false  
Categories=Development;Matlab;  
快捷方式完成。
sudo umount /media/matlab
##########################

##序列共线性
genoPlotR

###########安装jdk java (oracle java比open jdk好)
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
sudo apt-get install oracle-java7-installer
sudo apt-get install oracle-java8-installer
sudo update-java-alternatives -s java-7-oracle #配置默认java版本7
sudo update-java-alternatives -s java-8-oracle #配置默认java版本8

##########判断某个路径下时候生成文件
for m in $(ls); do if [ ! -f "$m/accepted_hits.bam.htseq.out" ]; then echo $m;fi; done

############统计染色体长度
samtools faidx sample.fa
cut -f1-2 sample.fa.fai

##########统计文件大小
find -name "accepted_hits.bam" | xargs du -h 

###########两行合并一行
sed 'N;s/\n/\t/' maizie.mapping_rate
 awk 'ORS=NR%2?"\t":"\n"{print}' #多行变一行
awk '{printf "%s",/^>/ ? $0" ":$0"\n" }' contig.fa  #fasta序列多行变一行
awk '/^>/&&NR>1{print "";}{ printf "%s",/^>/ ? $0" ":$0 }' test.fa

####升级python library
pip install -U numpy

####查看python模块包路径
python -c "from distutils.sysconfig import get_python_lib; print get_python_lib()"

########安装maftools需要另外两个python模块，在sonlib模块时，要改变make文件的./bin目录
安装maftools需要git下载安装，否则出错

##################### symap4 to circos link data
awk '{print "at"$1,$4,$5,"at"$2,$6,$7}' blocks  >/home/wuzefeng/MyResearch/Imprinting_prediction/imprint_gene_list/TAIR10/circos/data/ara_link.data


####################### grep 特定文件名并移动到另外目录
find -name "*.fa" | xargs grep -l contig |xargs -I{}  mv {} ../   # -l 匹配contig的文件并转移


########################## excel 条件格式
=AND($B4>0,$C4=0,$D4=0,$E4=0,$F4=0,$G4=0,$H4=0) 

######################### 空值填0
方法/步骤
选择数据源，按组合键“Ctrl+G”；
选择“定位条件”，然后选择“空值”；
在顶部赋值栏输入“0”；
最后，敲击键盘“Ctrl+Enter”组合键，给所选单元格赋“0”的值；

############## 批量删除进程
kill -9 `ps -ef |grep "/usr/lib/R/bin/exec/R --slave --no-restore -e paralle" |awk '{print $2}'`



##########数据库
http://structuralbiology.cau.edu.cn/sorghum/gene_detail.php?gene=Sobic.001G530400 (高粱功能基因组数据库)
########### 软件
pebl-project python for bayes intergation
rsync -av -u --update rsync://ftp.ensemblgenomes.org/all/pub/release-33/bacteria/gtf/*/*/*.gtf.gz ./
rsync -av -u --update rsync://ftp.ensemblgenomes.org/all/pub/release-31/plants/fasta/*/pep/*.pep.all.fa.gz ./

#########基因组浏览器
EpiGenome Gateway

#### 
cufflink
1.如果单个样本，也可以根据mapping的bam文件，根据参考gtf进行表达量定量
2.多个样本，推荐单独组装单个样本,然后用cuffmerge合并， 然后用cuffdiff根据融合的gff文件计算每个样本的表达量，以及差异表达。

# 根据基因id提取gtf文件对应id的上一行和下一行内容
 for m in $(cat test);do grep -A1 -B1 $m test.gtf| grep -v $m;done 

grep --no-group-separator -A1  "aa" test # grep 上一行

steme:
steme --minw=6 --maxw=14 --bg-model-order=5 --bg-fasta-file=/home/wuzefeng/MyResearch/Imprinting_prediction/imprint_gene_list/TAIR10/4imp_vs_non_imp_tf_enrich/imp_and_unimp_promoter/unimp_promoter.fa --output-dir=/home/wuzefeng/MyResearch/Imprinting_prediction/imprint_gene_list/TAIR10/4imp_vs_non_imp_tf_enrich/denovo/ /home/wuzefeng/MyResearch/Imprinting_prediction/imprint_gene_list/TAIR10/4imp_vs_non_imp_tf_enrich/imp_and_unimp_promoter/imp_promoter.fa 



gtf2protein_gene_ids
awk '$3=="gene"' Arabidopsis_thaliana.TAIR10.31.gtf | grep protein_coding | awk '{print $10}' | sed 's/"//g' | sed 's/;//g' > protein_gene_ids

for m in $(find -name *.gtf); do echo $m; grep "protein_coding" $m > ${m::-4}.protein_coding.gtf; done

##文件名通配符
ll *[!RV][!similar][1-9].motif


### beast
java -jar -Djava.library.path=$BEAST_LIB:/usr/local/lib beast.jar -beagle_info


## bam文件取uniqe mapping结果(注意会丢掉header的)
samtools view -h accepted_hits.bam | grep -E '^@|NH:i:1' | samtools view -Sb - > uniquely_mapped.bam


##
for m in $(ls -d */promoter/);do echo $m; done

##deep tools
bamCoverage  --normalizeUsingRPKM  --numberOfProcessors 8 -b H3K4me3.bam  -o ../8reads_coverage/H3K4me3_cheng.bw
computeMatrix scale-regions -S H3K4me3_cheng.bw -R gene.bed -a 1000 -b 1000 -out aa.out --numberOfProcessors 8
plotProfile --matrixFile  aa.out  -out H3K4me3.pdf --plotFileFormat pdf


###设定脚本执行时间(一次性)
at -f at.sh -t 1708182112

## Juicebox(下载数据)
juicebox_tools.7.5.jar dump norm KR https://hicfiles.s3.amazonaws.com/hiseq/gm12878/in-situ/combined.hic  BP 250000 1 1 GM12878_combined_250Kb_kr_norm.txt

## vcftools
vcftools --gzvcf 1001genomes_snp-short-indel_only_ACGTN.vcf.gz --freq --chr 1 --out chr1_analysis #计算1号染色体等位基因频率
vcftools --vcf input_file.vcf --remove-indels --recode --recode-INFO-all --out SNPs_only #删除indel 位点
vcftools --gzvcf input_file1.vcf.gz --gzdiff input_file2.vcf.gz --diff-site --out in1_v_in2 # 比较两个vcf文件
vcftools --bcf input_file.bcf --hardy --max-missing 1.0 --out output_noMissing # 哈迪温伯格p-value
vcftools --gzvcf 1001genomes_snp-short-indel_only_ACGTN.vcf.gz --site-pi  --chr 1 --out nucleotide_diversity # 输出pi值

## footprint
wellington_footprints.py DNase_peaks.bed  ../7good_bam/DNaseI.bam  footprint/  -o Ara_leaf -p 7



### conda
conda create -n pb-assembly  pb-assembly  #创建名为pb-assembly环境，并安装pb-assembly
source activate my_env #激活环境
conda info -e #查看环境列表


## 配置输入输出文件.cfg文件
fc_run [-h] config [logger]
