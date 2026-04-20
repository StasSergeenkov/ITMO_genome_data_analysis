> [!WARNING]
> Это задание сейчас находится в работе.


# Задание 4
В этом файле представлено краткое описание последовательности действий и технической стороны вопроса. [Полная версия отчета доступна по ссылке](ССЫЛЬ). 

## Шаг 2
Подсчет количества предсказанных белков выполнен следующей командой:
```shell
grep -c "^>" augustus.whole.aa
```
и составил 16435.


## Шаг 3
С помощью команды создана база данных:
```shell
makeblastdb -in augustus.whole.aa -dbtype prot -out tardigrade_proteins_db
```
а затем произведён поиск интересующих белков с помощью команды 
```shell
blastp -db tardigrade_proteins_db -query peptides.fa -out blast_results.txt -outfmt
```
был получен файл blast_results.txt
Далее осуществили поиск id всех уникальных белков:
```shell
# создание файла с id
awk '{print $2}' blast_results.txt | sort | uniq > protein_ids.txt

# подсчёт количества
wc -l protein_ids.txt``` 

В резулььтате обнаружено 34 белка. 
Последовательности этих белков получены с помощью команды
```shell
seqtk subseq augustus.whole.aa protein_ids.txt > candidates.fa
```


# Платформы
1. Вычисления для шагов 1-2, 7-10 и 5 (Prokka) выполнялись локально:
_OS: macOS Big Sur 10.16 23G80 arm64
Host: MacBookAir10,1
Kernel: 23.6.0
Shell: zsh 5.9
CPU: Apple M1
Memory: 1502MiB / 8192MiB_

2. Вычисления для шагов 3, 4, 6 выполнялись локально:
_OS: Ubuntu 24.04.4 LTS on Windows 10 x86_64
Kernel: 6.6.87.2-microsoft-standard-WSL2
Shell: bash 5.2.21
CPU: Intel Ultra 9 285H (16) @ 3.686GHz
Memory: 408MiB / 30354MiB_

3. Вычисления для шага 5 (Bakta) выполнялись локально:
_OS: Ubuntu 24.04.4 LTS x86_64
Kernel: 6.8.0-107-generic
Shell: bash 5.2.21
CPU: Intel Xeon E3-1230 v6 (4) @ 3.504GHz
Memory: 355MiB / 13678MiB_
