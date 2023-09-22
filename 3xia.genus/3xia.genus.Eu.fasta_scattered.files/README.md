# 写在前面
## 该文件夹下的文件是由完整文件3xia.genus.Eu.fasta分隔而来（由于Github不支持上传100M以上的文件）
```
split -b 60M -d -a 1 3xia.genus.Eu.fasta 3xia.genus.Eu.fasta_scattered.files/3xia.genus.Eu.fasta_
```
## 请将这7个文件合并成一个完整的3xia.genus.Eu.fasta文件进行使用
```
cat 3xia.genus.Eu.fasta_* > 3xia.genus.Eu.fasta
```
