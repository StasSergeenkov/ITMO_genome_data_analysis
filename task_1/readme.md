# Задание 1
В этом файле представлено краткое описание последовательности действий и технической стороны вопроса. [Полная версия отчета доступна по ссылке](https://docs.google.com/document/d/1Mxa0mwZh_K2JXTpOsK5Ljf-0QHickCP6N2xZKEnexZc/edit?usp=sharing).
## Шаг 0
Шаг 0 не выполнялся в связи с отсутствием технической возможности осуществления сборки сырых данных.

## Шаг 1
Выполнялся локально в QUAST 5.3.0. [Подробный отчет по ссылке](task_1/step_1). 
```shell
quast -o quast_results spades_scaffolds.fasta
```

Краткие метрики:
+ GC (%)                      45.92           
+ N50                         779             
+ Total length                2988329  

## Шаг 2
Выполнялся в точности с описанием в задании.
Первое - индексация и удаление контигов <3000 п.н.
```shell
# Индексация
samtools faidx spades_scaffolds.fasta

# Вывод и создание файла без контигов <3000 п.н.
more spades_scaffolds.fasta.fai  | awk '$2 >= 3000 {print $1}' | xargs samtools faidx spades_scaffolds.fasta > 3000.fa
```

Второе - последующая обработка файла выполнялась в virsorter 2.2.4. 
```shell
virsorter run \
  -w /home/stass/ITMO_genome_data_analysis/virsorter_output \
  -i /home/stass/ITMO_genome_data_analysis/spades_scaffolds.fasta \
  -j 16 \
  --db-dir /home/stass/ITMO_genome_data_analysis/db \
  all
```
параметр ```--min-length 3000``` не задавался, так как мы сделали это ранее (а можно было бы и не заморачиваться, но мы уже это выполнили).
В результате шага получены данные о 6 контигах, предположительно принадлежащих вирусам, [выходные данные в этом каталоге](task_1/step_2/virsorter_output)
| seqname | dsDNAphage | ssDNA | max_score | max_score_group | length | hallmark | viral | cellular |
|---------|------------|-------|-----------|-----------------|--------|----------|-------|----------|
| NODE_1_length_29907_cov_150.822528\|\|full | 0.640 | 0.100 | 0.640 | dsDNAphage | 29268 | 0 | 11.100 | 0.000 |
| NODE_3_length_5584_cov_13759.613946\|\|full | 0.627 | 0.607 | 0.627 | dsDNAphage | 5581 | 2 | 50.000 | 0.000 |
| NODE_9_length_3842_cov_27.242963\|\|full | 0.493 | 0.907 | 0.907 | ssDNA | 3838 | 1 | 20.000 | 0.000 |
| NODE_15_length_3618_cov_6.273652\|\|full | 0.987 | 0.947 | 0.987 | dsDNAphage | 3526 | 1 | 50.000 | 0.000 |
| NODE_21_length_3341_cov_35.065870\|\|full | 0.640 | 0.853 | 0.853 | ssDNA | 3167 | 1 | 33.300 | 0.000 |
| NODE_23_length_3129_cov_6.226737\|\|full | 0.520 | 0.127 | 0.520 | dsDNAphage | 3129 | 0 | 33.300 | 0.000 |

Все отобранные строки были переданы на следующий шаг.

## Шаг 3
Бластинг выполнялся стандартными средствами веб-интерфейса и [подробно описан в этом файле](task_1/step_3).
В результате было определено следующее:
+ Контиг NODE_1_length_29907_cov_150.822528 больше всего похож на Bat SARS-like coronavirus isolate bat-SL-CoVZC45 (метрики по ссылке в файле)
+ Контиг  NODE_3_length_5584_cov_13759.613946 вероятнее всего принадлежит Escherichia phage phiX174 - он используется как положительный контроль в в секвенировании illumina.

## Шаг 4
Для структурной и функциональной аннотации использовался программный инструмент Prokka 1.15.6 (конечно мы хотели бы использовать Bakta, но инструмент для этого непригоден).
В результате получены [следующие данные, которые доступны по ссылке](task_1/step_4/prokka_output). Вот короткий вывод:
```
organism: Genus species strain 
contigs: 1
bases: 29268
CDS: 9
gene: 9
```
## Шаг 5
Выполнена разработка праймеров для ПЦР с помощью PrimerBlast, [подробное описание тут](task_1/step_5), вот краткая таблица:

| Пара | Forward праймер (5'→3') | Reverse праймер (5'→3') | Длина продукта (bp) |
|------|------------------------|------------------------|---------------------|
| Пара 1 | GACTAATTCTCCTCGGCGGG (2031–2050) | TGTACCCGCTAACAGTGCAG (2643–2624) | 613 |
| Пара 2 | CTGCACTGTTAGCGGGTACA (2624–2643) | ACACCATGAGGTGCTGACTG (3179–3160) | 556 |
| Пара 3 | GATGCTGTCCGTGATCCACA (1720–1739) | CCCGCCGAGGAGAATTAGTC (2050–2031) | 331 |
| Пара 4 | TTGCCACCTTTGCTCACAGA (2581–2600) | CATGAGGTGCTGACTGAGGG (3175–3156) | 595 |
| Пара 5 | CTGATGCTGTCCGTGATCCA (1718–1737) | GTGGCAAAACAGTAAGGCCG (2587–2568) | 870 |
| Пара 6 | CAAATCGCTCCAGGGCAAAC (1225–1244) | CTGTGGATCACGGACAGCAT (1740–1721) | 516 |
| Пара 7 | CCTCAGTCAGCACCTCATGG (3157–3176) | ATGGCAGGAGCAGTTGTGAA (3242–3223) | 86 |
| Пара 8 | TTCAGGTTGGACAGCTGGTG (765–784) | CAGATGCAAATCTGGTGGCG (1048–1029) | 284 |
| Пара 9 | ACTGATGCTGTCCGTGATCC (1717–1736) | TCTGTGAGCAAAGGTGGCAA (2600–2581) | 884 |
| Пара 10 | TGGCCATGGTACATTTGGCT (3634–3653) | TGACTCCTTTGAGCACTGGC (3805–3786) | 172 |


# Платформы
1. Вычисления для шага 1 и 4 выполнялись локально:
_OS: macOS Big Sur 10.16 23G80 arm64
Host: MacBookAir10,1
Kernel: 23.6.0
Shell: zsh 5.9
CPU: Apple M1
Memory: 1502MiB / 8192MiB_
2. Вычисления для шага 2 выполнялись локально:
_OS: Ubuntu 24.04.4 LTS x86_64
Host: VMware Virtual Platform None
Kernel: 6.8.0-106-generic
Shell: bash 5.2.21
CPU: Intel Xeon E3-1230 v6 (4) @ 3.504GHz
Memory: 858MiB / 13678MiB_
3. Вычисления для шага 3 выполнялись онлайн в [Standard Nucleotide BLAST](https://blast.ncbi.nlm.nih.gov/Blast.cgi?PROGRAM=blastn&PAGE_TYPE=BlastSearch&LINK_LOC=blasthome)
4. Вычисления для шага 5 выполнялись ондлайн в [Primer-BLAST ](https://www.ncbi.nlm.nih.gov/tools/primer-blast/)
