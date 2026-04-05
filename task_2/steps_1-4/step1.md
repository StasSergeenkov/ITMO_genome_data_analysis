### Step 1-2
Мы скачали файлы с сырыми ридами, проверили, что это они и они записаны в прафильном формате. 
```
head -20 amp_res_1.fastq
head -20 amp_res_1.fastq
cat amp_res_1.fastq
```
Честно, ничего кроме того, что `cat`  выводит весь файл, я не заметила
```
wc - l amp_res_1.fastq
wc - l amp_res_2.fastq
```
Эти команды вывели следующий результат:
```
1823504 amp_res_1.fastq
1823504 amp_res_2.fastq
```
Затем, мы последовали совету из файла с дз, и загрузили  seqkit
использовали следующие команды:
```
seqkit stats amp_res_1.fastq
seqkit stats amp_res_2.fastq
```
Получили следующий результат:
```

seqkit stats amp_res_1.fastq 
file             format  type  num_seqs     sum_len  min_len  avg_len  max_len
amp_res_1.fastq  FASTQ   DNA    455,876  46,043,476      101      101      101

seqkit stats amp_res_2.fastq 
file             format  type  num_seqs     sum_len  min_len  avg_len  max_len
amp_res_2.fastq  FASTQ   DNA    455,876  46,043,476      101      101      101

```
