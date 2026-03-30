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

Второе - последующая обработка файла выполнялась в virsorter


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
