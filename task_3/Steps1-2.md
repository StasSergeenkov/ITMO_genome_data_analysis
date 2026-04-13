## Шаг 1 FastQC analysis
прогнали в FastQC прямые и обратные риды, вот [резульаты прямого прочтения](file:///Users/bogdanamaksimova/Desktop/SRR292678sub_S1_L001_R1_001_fastqc.html), вот [результаты обратного прочтения](file:///Users/bogdanamaksimova/Desktop/SRR292678sub_S1_L001_R2_001_fastqc.html)
Затем, с помощю  ```seqkit stats``` над файлом SRR292678sub_S1_L001_R1_001.fastq получили следующие данные:
file                               format  type   num_seqs      sum_len  min_len  avg_len  max_len
SRR292678sub_S1_L001_R1_001.fastq  FASTQ   DNA   5,499,346  494,941,140       90       90       90
над файлом SRR292678sub_S1_L001_R2_001.fastq получили следующее:
file                               format  type   num_seqs      sum_len  min_len  avg_len  max_len
SRR292678sub_S1_L001_R2_001.fastq  FASTQ   DNA   5,499,346  494,941,140       90       90       90
то есть количество ридов 5,499,346
## Шаг 2 К-мерный пройиль и предсказание размера генома
По техническим причинам я не смогла воспользоваться jellyfish, поэтому пользовалась dsk - ссылка на статью лежит [здесь](https://academic.oup.com/bioinformatics/article/29/5/652/253092)
Я вводила следующие команды:
```dsk -file SRR292678sub_S1_L001_R1_001.fastq -kmer-size 31 -abundance-min 1 -histo 1 -out k31```
```dsk -file SRR292678sub_S1_L001_R2_001.fastq -kmer-size 31 -abundance-min 1 -histo 1 -out k31```
Для первого файла результаты, визуализированные с помошью GenomeScope version 1.0: 
![resultpic](http://genomescope.org/user_data/somjLcek4ZvYnMCaOOVy/plot.png)
![resultpic](http://genomescope.org/user_data/somjLcek4ZvYnMCaOOVy/plot.log.png)
k = 31

property                      min               max               
Heterozygosity                0.364525%         0.378321%         
Genome Haploid Length         4,807,028 bp      4,823,888 bp      
Genome Repeat Length          69,761 bp         70,006 bp         
Genome Unique Length          4,737,267 bp      4,753,882 bp      
Model Fit                     94.8965%          95.6352%          
Read Error Rate               0.0780987%        0.0780987%        

Для второго файла: GenomeScope version 1.0
![resultpic](http://genomescope.org/user_data/FgGuLrDrKVd4wUJqc54M/plot.png)
![resultpic](<img width="2000" height="2000" alt="image" src="https://github.com/user-attachments/assets/4a9efceb-18a5-4e74-9f52-cf2d84eb18ed" />)


k = 31

property                      min               max               
Heterozygosity                0.374961%         0.388117%         
Genome Haploid Length         4,798,156 bp      4,814,419 bp      
Genome Repeat Length          81,618 bp         81,894 bp         
Genome Unique Length          4,716,538 bp      4,732,524 bp      
Model Fit                     95.0208%          95.7264%          
Read Error Rate               0.0941036%        0.0941036%     

