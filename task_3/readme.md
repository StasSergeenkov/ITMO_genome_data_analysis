# Задание 3
В этом файле представлено краткое описание последовательности действий и технической стороны вопроса. [Полная версия отчета доступна по ссылке](https://docs.google.com/document/d/13edNjYbhcxL4m8EDiOXHleemtLYLHgA0MnRz3cv7tyg/edit?usp=sharing). 

## Шаг 1

Контроль качества исходных данных для библиотеки SRR292678 (парные концы, вставка 470 bp, ~400 Мб на файл) выполнен в FastQC.  
Анализ запущен следующей командой:
```shell
fastqc SRR292678_1.fastq SRR292678_2.fastq -o fastqc_reports/
```

Затем, с помощю команды seqkit stats над файлом SRR292678sub_S1_L001_R1_001.fastq получили следующие данные:
> file                               format  type   num_seqs      sum_len  min_len  avg_len  max_len  
> SRR292678sub_S1_L001_R1_001.fastq  FASTQ   DNA   5,499,346  494,941,140       90       90       90

над файлом SRR292678sub_S1_L001_R2_001.fastq получили следующее:
>file                               format  type   num_seqs      sum_len  min_len  avg_len  max_len  
>SRR292678sub_S1_L001_R2_001.fastq  FASTQ   DNA   5,499,346  494,941,140       90       90       90  
то есть количество ридов 5,499,346



## Шаг 2
По техническим причинам мы не смогли воспользоваться jellyfish, поэтому пользовалась dsk - ссылка на статью лежит [здесь](https://academic.oup.com/bioinformatics/article/29/5/652/253092)
Ввели следующие команды:
```dsk -file SRR292678sub_S1_L001_R1_001.fastq -kmer-size 31 -abundance-min 1 -histo 1 -out k31```
```dsk -file SRR292678sub_S1_L001_R2_001.fastq -kmer-size 31 -abundance-min 1 -histo 1 -out k31```
Для первого файла результаты, визуализированные с помошью GenomeScope version 1.0: 
![resultpic](http://genomescope.org/user_data/somjLcek4ZvYnMCaOOVy/plot.png)
![resultpic](http://genomescope.org/user_data/somjLcek4ZvYnMCaOOVy/plot.log.png)
k = 31

| property                 | min            | max            |
|--------------------------|----------------|----------------|
| Heterozygosity           | 0.364525%      | 0.378321%      |
| Genome Haploid Length    | 4,807,028 bp   | 4,823,888 bp   |
| Genome Repeat Length     | 69,761 bp      | 70,006 bp      |
| Genome Unique Length     | 4,737,267 bp   | 4,753,882 bp   |
| Model Fit                | 94.8965%       | 95.6352%       |
| Read Error Rate          | 0.0780987%     | 0.0780987%     |       

Для второго файла: GenomeScope version 1.0
![resultpic](http://genomescope.org/user_data/FgGuLrDrKVd4wUJqc54M/plot.png)
![resultpic](<img width="2000" height="2000" alt="image" src="https://github.com/user-attachments/assets/4a9efceb-18a5-4e74-9f52-cf2d84eb18ed" />)


k = 31

| property                 | min            | max            |
|--------------------------|----------------|----------------|
| Heterozygosity           | 0.374961%      | 0.388117%      |
| Genome Haploid Length    | 4,798,156 bp   | 4,814,419 bp   |
| Genome Repeat Length     | 81,618 bp      | 81,894 bp      |
| Genome Unique Length     | 4,716,538 bp   | 4,732,524 bp   |
| Model Fit                | 95.0208%       | 95.7264%       |
| Read Error Rate          | 0.0941036%     | 0.0941036%     |
  
## Шаг 3
Сборка генома E. coli X из парных ридов выполнялась в SPAdes v4.2.0:
```shell
spades.py \
  --pe1-1 /home/stass/ITMO_genome_data_analysis/HW3/row_data/SRR292678sub_S1_L001_R1_001.fastq \
  --pe1-2 /home/stass/ITMO_genome_data_analysis/HW3/row_data/SRR292678sub_S1_L001_R2_001.fastq \
  -o .
```

Дальнейшая оценка качества сборки выполнена в QUAST v5.0.2: 
```shell
quast.py /home/stass/ITMO_genome_data_analysis/HW3/step_3/contigs.fasta \
         /home/stass/ITMO_genome_data_analysis/HW3/step_3/scaffolds.fasta \
         -o quast_results
```
Вот краткие результаты:
| Metric                   | contigs  | scaffolds |
|--------------------------|----------|-----------|
| contigs (>= 0 bp)        | 517      | 499       |
| contigs (>= 1000 bp)     | 151      | 147       |
| contigs (>= 5000 bp)     | 83       | 82        |
| contigs (>= 10000 bp)    | 68       | 66        |
| contigs (>= 25000 bp)    | 50       | 50        |
| contigs (>= 50000 bp)    | 32       | 33        |
| Total length (>= 0 bp)   | 5315160  | 5316783   |
| Total length (>= 1000 bp)| 5204929  | 5205502   |
| Total length (>= 5000 bp)| 5038908  | 5045822   |
| Total length (>= 10000 bp)| 4935231 | 4935654   |
| Total length (>= 25000 bp)| 4639340 | 4685368   |
| Total length (>= 50000 bp)| 3960781 | 4051686   |
| contigs                  | 206      | 214       |
| Total length             | 5244705  | 5253645   |
| N50                      | 105346   | 105346    |
| L50                      | 15       | 15        |

## Шаг 4
Для оценки влияния длинных ридов на сборку генома *E. coli* X мы использовали гибридный режим ассемблера SPAdes, добавив к коротким парным ридам (библиотека SRR292678) библиотеку длинных ридов PacBio SMRT SRR1980037 со средней длиной фрагмента ~20 000 bp.

Сборка выполнялась командой:

```bash
spades.py \
  -1 /home/stass/ITMO_genome_data_analysis/HW3/row_data/SRR292678sub_S1_L001_R1_001.fastq \
  -2 /home/stass/ITMO_genome_data_analysis/HW3/row_data/SRR292678sub_S1_L001_R2_001.fastq \
  --pacbio /home/stass/ITMO_genome_data_analysis/HW3/row_data/SRR1980037.fastq \
  -o /home/stass/ITMO_genome_data_analysis/HW3/step_4 \
  -t 16
```

SPAdes выполнил коррекцию ошибок в коротких ридах и затем собрал контиги и скаффолды, используя длинные риды для разрешения повторяющихся областей.

Для объективной оценки улучшения мы сравнили две сборки:
- Только короткие риды (результат шага 3)
- Гибридная сборка (короткие + PacBio)

QUAST запускался с минимальной длиной контига 500 bp:

```bash
quast.py \
  /home/stass/ITMO_genome_data_analysis/HW3/step_3/contigs.fasta \
  /home/stass/ITMO_genome_data_analysis/HW3/step_4/contigs.fasta \
  -o /home/stass/ITMO_genome_data_analysis/HW3/step_4/quast_comparison \
  --min-contig 500 \
  -t 16
```

Отчёт в формате HTML был открыт в браузере, ключевые метрики приведены в таблице:

| Сборка | N50 (bp) | Число контигов (≥500 bp) |
|--------|----------|---------------------------|
| Только Illumina (шаг 3) | 105346 | 206 |
| Гибридная (Illumina + PacBio) | 816412 | 20 |

## Шаг 5

После сборки генома _E. coli X_ была проведена аннотация — поиск генов и других функциональных элементов. Для этого использовались две программы: Prokka и Bakta. 

### Аннотация с помощью Prokka

Prokka выполняет предсказание генов (в т.ч. tRNA, rRNA) и функциональную аннотацию через BLAST против базы RefSeq. Важное замечание из FAQ: для совместимости с Mauve (инструмент сравнительной геномики) необходимо переименовать контиги, чтобы они имели короткие имена без спецсимволов. Это сделано параметром `--centre XXX`.

Запуск на файле scaffolds.fasta из шага 4 выполнен командой:

```bash
prokka --outdir annotation --prefix bacteria --genus Escherichia --species coli  --compliant --centre XXX scaffolds.fasta
```

### Аннотация с помощью Bakta

Для совместимости с Mauwe потребовалось предварительно переименовать контиги в короткие имена (`contig1`, `contig2`…).

Переименование контигов выполнено командой (во временный файл):

```bash
awk '/^>/ {print ">contig" ++i; next} {print}' scaffolds.fasta > /tmp/scaffolds_renamed.fasta
```

Запуск Bakta с указанием рода _Escherichia_ и использованием 4 потоков:

```bash
bakta --db /home/stas/bakta_db/db \
      --output /home/stas/annotation_results \
      --genus Escherichia \
      --threads 4 \
      /tmp/scaffolds_renamed.fasta
```

После завершения все выходные файлы скопированы в рабочую директорию, а временная папка очищена (простите сервер дурак - права не права):

```bash
cp -r /home/stas/annotation_results/* /mnt/hgfs/SFTP/ITMO_genome_data_analysis/HW3_task_5/
rm -rf /home/stas/annotation_results/*
```

**В дальнейшем анализе использовался результат аннотации Prokka (для соответствия заданию)

## Шаг 6
Выполним выделение 16s из генома посредством barrnap:
```shell
barrnap --kingdom bac \
        --threads 4 \
        --outseq rrna.fasta \
        scaffolds.fna > rrna_coords.gff
```
В выходном файле выбрана одна 16s и использована для выраванивания посредством BLAST на установленных заданием условиях.  
Штамм: Escherichia coli str. K-12 substr. MG1655  
GenBank Accession Number: NC_000913.3  
Референсная последовательность сборки указанного организма находится [вот тут](task_3/step_6/55989.fasta)

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
