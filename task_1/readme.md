# Задание 1
## Шаг 0
Шаг 0 не выполнялся в связи с отсутствием технической возможности осуществления сборки сырых данных.

## Шаг 1
Выполнялся в веб-интерфейсе Galaxy

## Шаг 2
Выполнялся в точности с описанием в задании.
Первое - индексация и удаление контигов <3000 п.н.
```shell
# Индексация
samtools faidx spades_scaffolds.fasta

# Вывод и создание файла без контигов <3000 п.н.
more spades_scaffolds.fasta.fai  | awk '$2 >= 3000 {print $1}' | xargs samtools faidx spades_scaffolds.fasta > 3000.fa
```

Второе - последующая обработка файла выполнялась в virsorter. 
```shell
virsorter run \
  -w /home/stass/ITMO_genome_data_analysis/virsorter_output \
  -i /home/stass/ITMO_genome_data_analysis/spades_scaffolds.fasta \
  -j 16 \
  --db-dir /home/stass/ITMO_genome_data_analysis/db \
  all
```
параметр ```--min-length 3000``` не задавался, так как мы сделали это ранее (а можно было бы и не заморачиваться, но мы уже это выполнили)
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

# 
Для вычислений использовались:
1. Для локальных вычислений:
_OS: Ubuntu 24.04.4 LTS x86_64
Host: VMware Virtual Platform None
Kernel: 6.8.0-106-generic
Shell: bash 5.2.21
CPU: Intel Xeon E3-1230 v6 (4) @ 3.504GHz
Memory: 858MiB / 13678MiB_
3. 
4. Для 
