# Задание 3
В этом файле представлено краткое описание последовательности действий и технической стороны вопроса. [Полная версия отчета доступна по ссылке](https://docs.google.com/document/d/13edNjYbhcxL4m8EDiOXHleemtLYLHgA0MnRz3cv7tyg/edit?usp=sharing). 

## Шаг 1
## Шаг 2
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


## Шаг 6
Выполним выделение 16s из генома посредством barrnap:
```shell
barrnap --kingdom bac \
        --threads 4 \
        --outseq rrna.fasta \
        scaffolds.fna > rrna_coords.gff
```
В выходном файле выбрана одна 16s и использована для выраванивания посредством BLAST на установленных заданием условиях.

# Платформы
1. Вычисления для шагов 1-4 выполнялись локально:
_OS: macOS Big Sur 10.16 23G80 arm64
Host: MacBookAir10,1
Kernel: 23.6.0
Shell: zsh 5.9
CPU: Apple M1
Memory: 1502MiB / 8192MiB_

2. Вычисления для шагов 5-8 выполнялись локально:
_OS: Ubuntu 24.04.4 LTS on Windows 10 x86_64
Kernel: 6.6.87.2-microsoft-standard-WSL2
Shell: bash 5.2.21
CPU: Intel Ultra 9 285H (16) @ 3.686GHz
Memory: 408MiB / 15672MiB_
