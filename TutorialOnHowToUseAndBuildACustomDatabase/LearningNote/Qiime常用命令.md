# 写在前面
更全面的用法请参见：https://github.com/YongxinLiu/QIIME2ChineseManual
# 1. 文件导入、导出、数据预处理

- fasta格式文件
```
##导入fasta格式，QIIME 2目前支持导入QIIME 1 seqs.fna文件格式，该格式由一个fasta文件组成，每条记录只有两行：header和sequence。
#每个序列必须正好一行，不能拆分多行。每条序列的ID必须遵循格式_的要求。
#是序列所属样本的标识符，是其样本中序列的标识符。
qiime tools import --input-path sequences.fna --output-path sequences.qza --type 'FeatureData[Sequence]'
```

- 根据样品清单导入fastq格式
```
##已拆分好的fastq双端原始数据导入，Phred=32
time qiime tools import \
  --type "SampleData[SequencesWithQuality]" \
  --input-format SingleEndFastqManifestPhred33V2 \
  --input-path ./manifest.tsv \
  --output-path ./demux_seqs.qza
##导入已拆分好的Phred33格式单端测序结果  
qiime tools import --type 'SampleData[SequencesWithQuality]' --input-path manifest.valid.tsv \
--output-path single-end-demux.valid.qza --input-format SingleEndFastqManifestPhred33V2
##导入已拆分好的Phred33格式双端合并后的序列
qiime tools import --type SampleData[JoinedSequencesWithQuality] --input-path fj-joined/manifest \
--output-path fj-joined-demux.qza  --input-format SingleEndFastqManifestPhred33V2 
```

- 双端序列合并Joining reads
```
qiime vsearch join-pairs --i-demultiplexed-seqs paired-end-demux.qza --o-joined-sequences paired-end-demux-joined.qza --p-threads 8
```

- 导入物种注释文件
```
qiime tools import   --type 'FeatureData[Taxonomy]' \  
--input-format HeaderlessTSVTaxonomyFormat \    #HeaderlessTSVTaxonomyFormat没有表头的Tsv文件，Tab键分隔而不是空格分隔，TSVTaxonomyFormat有表头
 --input-path 3xia.genus.taxonomy --output-path 3xia.genus.ref.taxonomy.qza    
```

- 导出文件
```
##导出Phylogeny[Unrooted]对象为newick格式
qiime tools export \
  --input-path unrooted-tree.qza \
  --output-path exported-tree
##导出物种注释文件,导出结果为tsv文件，其实就是将qza文件重命名为zip后解压
qiime tools export --input-path taxonomy.qza --output-path taxa   
##导出代表序列文件，导出结果为dna-sequences.fasta
qiime tools export --input-path filtered_rep_seq.qza --output-path filtered_rep_seq  
```

# 2. BIOM文件常用命令+Qiime

- 文件导入
```
#BIOM v1.0.0
time qiime tools import --input-path feature-table-v100.biom --type 'FeatureTable[Frequency]'  --input-format BIOMV100Format --output-path feature-table-1.qza
  
#BIOM v2.1.0
qiime tools import --input-path feature-table-v210.biom --type 'FeatureTable[Frequency]' --input-format BIOMV210Format   --output-path feature-table-2.qza
  
##导入taxonomy文件
qiime tools import --input-path taxa_table.biom --type 'FeatureData[Taxonomy]' --input-format BIOMV210Format --output-path taxonomy.qza
```

- 文件导出为Biom格式
```
# 导出FeatureTable[Frequency]对象为BIOM v2.1格式
qiime tools export --input-path feature-table.qza  --output-path exported-feature-table
```


- Biom文件转换
```
#转换biom为tsv格式
biom convert -i feature-table.biom -o feature-table.tsv --to-tsv

#tsv转换为biom格式
biom convert -i feature-table.txt -o feature-table.biom --table-type="OTU table" --to-hdf5

#转换biom为经典格式，并在最后列包括物种注释信息
biom convert -i table.biom -o table.from_biom_w_taxonomy.txt --to-tsv --header-key taxonomy

#转换biom为经典格式，并在最后列包括物种注释信息，并改名为ConsensusLineage，此功能对于一些软件要求指定的列名有很有用
biom convert -i table.biom -o table.from_biom_w_consensuslineage.txt --to-tsv --header-key taxonomy --output-metadata-id "ConsensusLineage"

#带物种注释表格互转
biom convert -i table.biom -o table_tax.txt --to-tsv --header-key taxonomy
biom convert -i table_tax.txt -o new_table.biom --to-hdf5 --table-type="OTU table" --process-obs-metadata taxonomy
biom convert -i table_tax.txt -o new_table.biom --to-json --table-type="OTU table" --process-obs-metadata taxonomy
```

- 取子集亚组进行分析
```
biom subset-table -i otu_table.biom -a sample -s samples_list.txt -o otu_table_subset.biom
```

- Biom表统计
```
##统计每个样品
biom summarize-table -i feature-table.biom -o feature-table.summarize.txt
biom summarize-table -i table.w_md.biom      #结果不输出为文件
##
biom summarize-table -i table.w_md.biom --qualitative -o table.w_md_qual_summary.txt
```

# 3. 数据统计及可视化

- 元数据可视化
```
qiime metadata tabulate --m-input-file metadata.tsv --o-visualization metadata.qzv
```

- 物种注释可视化
```
qiime metadata tabulate --m-input-file taxonomy.qza --o-visualization taxonomy.qzv
```

- 特征表汇总及可视化
```
qiime feature-table summarize --i-table table.qza --o-visualization table.qzv \
--m-sample-metadata-file sample-metadata.tsv
```

- 特征序列汇总及可视化
```
qiime feature-table tabulate-seqs --i-data rep-seqs.qza --o-visualization rep-seqs.qzv
```

- 拆分文件统计及可视化
```
##这使我们确定每个样本获得多少序列，并且还可以获得序列数据中每个位置处序列质量分布的摘要。
time qiime demux summarize \
  --i-data ./demux_seqs.qza \
  --o-visualization ./demux_seqs.qzv
```

# 4. 数据过滤（OTU表过滤）

- Qiime1过滤Biom文件
```
# 按样品数据测序量过滤：选择counts>30000的样品
filter_samples_from_otu_table.py -i otu_table.biom -o otu_table1.biom -n 30000
# 查看过滤后结果：
biom summarize-table -i otu_table1.biom
# 按样品数据测序量过滤：选择counts<10000的样品
filter_samples_from_otu_table.py -i otu_table.biom -o otu_table_no_high_coverage_samples.biom -x 10000
# 按OTU丰度过滤：选择相对丰度均值大于十万分之一的OTU
filter_otus_from_otu_table.py --min_count_fraction 0.00001 -i otu_table.biom -o otu_table1.biom
# 按物种过滤OTU表：去除p__Chloroflexi菌门等 
filter_taxa_from_otu_table.py -i otu_table3.biom -o otu_table4.biom -n p__Chloroflexi
```

- Qiime2过滤OTU_table
```
#过滤至少在2个样品中存在的Feature，去除偶然的Feature
qiime feature-table filter-features --i-table table.qza --p-min-samples 2 --o-filtered-table sample-contingency-filtered-table.qza
#过滤低丰度，< 5的特征被过滤掉
qiime feature-table filter-features --i-table table.qza --p-min-frequency 5 --o-filtered-table feature-frequency-filtered-table.qza
##一次命令：筛选最小频率为50，至少在4个样品中出现的特征
time qiime feature-table filter-features \
  --i-table ./table_2k.qza \
  --p-min-frequency 50 \
  --p-min-samples 4 \
  --o-filtered-table ./table_2k_abund.qza
```


- Qiime2基于物种过滤过滤OTU_table
```
#过滤掉含Obelia的feature
qiime taxa filter-table --i-table table.qza --i-taxonomy taxonomy.qza --p-exclude mitochondria --o-filtered-table table-no-Obelia.qza
Qiime2基于OTU_Table过滤ref-seq序列
qiime feature-table  filter-seqs --i-data rep-seqs.qza --i-table filtered-table.qza --o-filtered-data filtered-rep-seqs.qza
Qiime2过滤掉比稀疏深度更少的序列的样本
time qiime feature-table filter-samples \
  --i-table ./dada2_table.qza \
  --p-min-frequency 2000 \
  --o-filtered-table ./table_2k.qza
```

# 5. 物种注释+物种组成分析

- 提取参考序列（兼并引物，可能不行）
```
按测序的引物来提取参考序列中的一段
time qiime feature-classifier extract-reads --i-sequences 85_otus.qza \
--p-f-primer GGWACWGGWTGAACWGTWTAYCCYCC --p-r-primer TAIACYTCIGGRTGICCRAARAAYCA \
--p-min-length 300 --p-max-length 400 --o-reads ref-seqs.qza
##--p-trunc-len 120 
```

- 过滤参考序列与注释信息

```
## 仅保留细菌的代表序列命令
qiime taxa filter-seqs 
--i-sequences ref-seqs.qza  --i-taxonomy 99_otu_taxonomy.qza 
--p-include Bacteria --o-filtered-sequences ref-seqs-Bacteria.qza


## 仅保留细菌的注释信息命令

qiime rescript filter-taxa  --i-taxonomy 99_otu_taxonomy.qza 
--m-ids-to-keep-file ref-seqs-Bacteria.qza --o-filtered-taxonomy ref-seqs-Bacteria-tax.qza
```

- 训练分类集
```
qiime feature-classifier fit-classifier-naive-bayes --i-reference-reads ref-seqs.qza 
--i-reference-taxonomy ref-taxonomy.qza   --o-classifier classifier.qza
```

- 物种注释
```
  qiime feature-classifier classify-sklearn --i-classifier 3xia.genus.classifier.qza --i-reads filtered-rep-seqs.qza 
  --o-classification taxonomy.3xia.genus.qza --p-n-jobs 16                       #可设置线程数
```
选择合理算法进行注释
```
由于测序序列长度的限制，以及微生物种类的多样性，导致容易出现“一对多”（一条序列对应多个潜在物种注释结果）的现象，
如果没有合理的注释方法，就会出现“选择困难症”。因此，在分析时，就需要对注释结果进行必要的取舍，既保证注释结果的可靠可信，
又尽可能不损失物种注释的精度（保证尽可能还原物种的精细组成）。
```
![image](https://github.com/BANZUIGE/Mito-COI/assets/95343596/34f1e7bd-c617-4505-a5d0-fba5efdab0f0)

- 物种注释结果可视化
```
qiime metadata tabulate --m-input-file taxonomy.qza --o-visualization taxonomy.qzv
```
- 交互式条形图查看样本的分类组成（直接结果解压结果 barplot.qzv可得各分类水平数据）
```
qiime taxa barplot --i-table table.qza --i-taxonomy taxonomy.qza --m-metadata-file sample-metadata.tsv  --o-visualization taxa-bar-plots.qzv
```
- （属）水平差异统计+可视化（3步）
```
#1. 各分类（l1-l6）水平合并并统计
qiime taxa collapse --i-table table-no-Cnidaria-unclassified.qza --i-taxonomy taxonomy.qza 
--p-level 6 --o-collapsed-table table-no-Cnidaria-unclassified-genus.stat.qza
循环语句多水平统计
for i in 2 6 
do 
qiime taxa collapse --i-table table_final.qza --i-taxonomy taxonomy.qza \
  --p-level $i  --o-collapsed-table table-l$i.qza
done
#导出 tsv
for file in ./table-l*.qza
do
  base=$(basename $file .qza)
  echo $base
  qiime tools export --input-path $file --output-path $base
  biom convert --to-tsv -i $base/feature-table.biom -o $base.tsv
done


#2. 格式化特征表，添加伪计数

qiime composition add-pseudocount --i-table genus.stat.qza --o-composition-table genus.stat.composition.qza 


#3. 计算差异属，指定分组类型   

qiime composition ancom \
  --i-table comp-gut-table-l6.qza --m-metadata-file sample-metadata.tsv \
```

- 相对丰度计算
```
qiime feature-table relative-frequency --i-table  filtered-table.qza \
--o-relative-frequency-table filtered-relative-frequency-table.qza
##导出为biom格式或可视化
```

# 6. 多样性分析

- 构建进化树用于多样性分析
```
time qiime phylogeny align-to-tree-mafft-fasttree --i-sequences rep-seqs.qza --o-alignment aligned-rep-seqs.qza --o-masked-alignment masked-aligned-rep-seqs.qza --o-tree unrooted-tree.qza --o-rooted-tree rooted-tree.qza
##创建一个片段插入树
time qiime fragment-insertion sepp \
  --i-representative-sequences ./dada2_rep_set.qza \
  --i-reference-database sepp-refs-gg-13-8.qza \
  --o-tree ./tree.qza \
  --o-placements ./tree_placements.qza \
  --p-threads 1
```

- Alpha稀疏曲线--注意选择测序深度
```
#基于进化树
time qiime diversity alpha-rarefaction \
  --i-table table.qza \
  --i-phylogeny rooted-tree.qza \
  --p-max-depth 4000 \
  --m-metadata-file sample-metadata.tsv \
  --o-visualization alpha-rarefaction.qzv
#不基于进化树
qiime diversity alpha-rarefaction --i-table ./dada2_table.qza --m-metadata-file ./metadata.tsv --o-visualization ./alpha_rarefaction_curves.qzv --p-min-depth 10 --p-max-depth 4250
```

- 计算核心多样性
```
time qiime diversity core-metrics-phylogenetic --i-phylogeny rooted-tree.qza --i-table table.qza --p-sampling-depth 1103 --m-metadata-file sample-metadata.tsv --output-dir core-metrics-results
```

- Alpha多样性组间显著性分析和可视化
```
##单因素对Alpha多样性的影响
#分别计算shannon、faith_pd、observed_features、evenness4个指数，注意${Alpha}_vector.qza这种文件
alpha=shannon      #指定变量，后面只需要修改指数名即可快速执行命令
for i in {shannon,faith_pd,observed_features,evenness};do qiime diversity alpha-group-significance --i-alpha-diversity core-metrics-results/${i}_vector.qza  
--m-metadata-file ../42sample.metadata2.tsv --o-visualization core-metrics-results/${i}-group-significance.qzv;done

##多因素对Alpha多样性的影响（如时间、空间）
##在一些实验中，多个相互作用的因素可能共同影响α多样性。 如果我们的α多样性估计遵循{正态分布}，我们可以使用方差分析（ANOVA）来测试多重效应是否显着影响α多样性
time qiime longitudinal anova \
  --m-metadata-file ./core-metrics-results/faith_pd_vector.qza \
  --m-metadata-file ./metadata.tsv \
  --p-formula 'faith_pd ~ genotype * donor_status' \
  --o-visualization ./core-metrics-results/faiths_pd_anova.qzv
```

- Beta多样性
```
##分别计算bray_curtis、jaccard、unweighted_unifrac、weighted_unifrac4个指数，注意${index}_distance_matrix.qza这种文件
beta=weighted_unifrac   #赋值
column=body-site
time qiime diversity beta-group-significance \
  --i-distance-matrix core-metrics-results/${beta}_distance_matrix.qza \
  --m-metadata-file sample-metadata.tsv \
  --m-metadata-column ${column} \
  --o-visualization core-metrics-results/${beta}-${column}-significance.qzv \
  --p-pairwise
  
##使用adonis动作来查看多变量模型。 adonis动作使用PERMANOVA检验，但是允许同时检验多种效应（类似于我们之前使用ANOVA对α多样性的多变量效应的方式）
time qiime diversity adonis \
  --i-distance-matrix core-metrics-results/unweighted_unifrac_distance_matrix.qza \
  --m-metadata-file metadata.tsv \
  --o-visualization core-metrics-results/unweighted_adonis.qzv \
  --p-formula genotype+donor          #genotype+donor两个因素
```

# 7. 纵向分析（时间序列，Beta多样性）


教程：https://github.com/BANZUIGE/QIIME2ChineseManual/tree/master/docs
----> QIIME 2教程. 07Cell帕金森小鼠Parkinson's Mouse(2021.2，最佳实战).md
```
##绘制波动率图，输入元数据，非权重unifrac的pcoa结果，指定状态列为时间，个体列为小鼠，分组列为供体状态，默认数值来自第二轴，输出pc_vol.qzv
time qiime longitudinal volatility \
  --m-metadata-file ./metadata.tsv \
  --m-metadata-file ./core-metrics-results/unweighted_unifrac_pcoa_results.qza \
  --p-state-column days_post_transplant \
  --p-individual-id-column mouse_id \
  --p-default-group-column 'donor_status' \
  --p-default-metric 'Axis 2' \
  --o-visualization ./pc_vol.qzv
```

# 8. Qiime1用法

- 安装
```
#可能需提前安装biom-format和bioconductor-microbiome
conda install biom-format  
conda install bioconductor-microbiome 
conda install --yes r-essentials blast-legacy cd-hit cdbtools cogent ea-utils \
infernal microbiomeutil muscle mothur r-vegan sourcetracker sortmerna sumaclust rdp-classifier swarm tax2tree  
#创建环境qiime1并安装qiime1
conda create -n qiime1 python=2.7 qiime matplotlib=1.4.3 mock nose -c bioconda   
print_qiime_config.py -t  #检查安装
```
- Classifier文件安装

```
rdp-classifier安装
echo "export /home/data/gmb14/qiime/Zooplankton/qiime1/rdb/rdp_classifier_2.2/rdp_classifier-2.2.jar" >> ~/.bashrc
```


- 物种注释
```
（1）vsearch运行 （生成biom文件，不可读）
vsearch --usearch_global ASVs.fa --db rdp_16s_v16.fa --biomout out_tax.txt --id 0.97

（2）qiime1运行（主流）
#bacterial
assign_taxonomy.py -i usearch_analysis/ASVs.fa  -r 99_otus.fasta -t 99_otu_taxonomy.txt -m rdp \ 
   --confidence=0.5 --min_consensus_fraction=0.51  --similarity=0.8 -o usearch_analysis/
     --blast_e_value / #使用blast方法时
# fungal:
assign_taxonomy.py -i ASVs.fa -r 99_otus.fasta -t 99_otu_taxonomy.txt \
    -m rdp --confidence=0.5 --min_consensus_fraction=0.51  --similarity=0.9 -o usearch_analysis/
    --blast_e_value=0.001\
#blast方法注释,不要用seqkitq去重复序列
assign_taxonomy.py -m uclust -i otu_rep.fasta -r 3xia.genus.Eu.fasta \
-t 3xia.genus.Eu.acc.tax  -o 3xia_taxonomy

（3）usearch运行
usearch -sintax ASVs.fa -db rdp_16s_v16.fa  -tabbedout ASV_tax_raw.txt -strand both -sintax_cutoff 0.4
fungal: 
usearch -sintax ASVs.fa -db silva_18s_v123.fa   -tabbedout ASVs_tax_assignments.txt -strand both -sintax_cutoff 0.6
```

![image](https://github.com/BANZUIGE/Mito-COI/assets/95343596/4975992e-49c1-490e-9e16-2c4b9c590a23)



- 结合qiime2获取相对丰度
```
1.导出物种分类信息和置信度
qiime tools export --input-path taxonomy.qza --output-path taxa

2.导出 BIOM 表，并加入将物种分类注释信息，就是处理下表头，让他兼容biom格式
#处理表头：替换操作
sed -i -e '1 s/Feature/#Feature/' -e '1 s/Taxon/taxonomy/' taxa/taxonomy.tsv
#导出otu(feature)表
qiime tools export    --input-path filtered-table.qza    --output-path qiime1/table_exported
#添加物种注释信息
biom add-metadata -i table_exported/feature-table.biom --observation-metadata-fp taxa/taxonomy.tsv 
-o feture_taxa/feture_taxa.biom --sc-separated taxonomy
#biom转换成txt格式
biom convert -i feture_taxa.biom  -o feture_taxa.txt --to-tsv --header-key taxonomy

3.qiime1获利各级分类结果--biom格式（相对丰度形式）
#结果按门、纲、目、科、属五个级别进行分类汇总，默认结果L2-L6
summarize_taxa.py -i feture_taxa.biom -o sum_taxa # summary each level percentage
统计L7种水平
summarize_taxa.py -L 7 -i feture_taxa.biom -o sum_taxa
```
# 9. Usearch11 用法
# 10. 分析流程

![image](https://github.com/BANZUIGE/Mito-COI/assets/95343596/358aa478-d323-4e58-9437-2f992e16548a)



















