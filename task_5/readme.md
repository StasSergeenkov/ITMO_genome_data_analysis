> [!WARNING]
> Это задание находится на этапе выполнения

# Задание 5
В этом файле представлено краткое описание последовательности действий и технической стороны вопроса. [Полная версия отчета доступна по ссылке](ССЫЛЬ). 

Два файла genotek.vcf и SNP_raw_v4_Full_20170514175358.txt обрабатывались параллельно для последующего сравнения.

## Шаг 2
Первым делом - конвертация 23andMe в VCF:

```shell
plink --23file 23andme_original.txt --recode vcf --out 23andme_converted --snps-only just-acgt
```
Количество SNP в исходном: 610526. После конвертации в VCF: 595401.
> [Выходной файл 23andme_converted.vcf тут]()

Также проведена очистка файла Genotek VCF:
```shell
plink --vcf genotek_original.vcf --snps-only just-acgt --recode vcf --out genotek_clean
```
Количество SNP в исходном: 618255. После очистки (snps-only): 608921.
> [Выходной файл genotek_clean.vcf тут]()

# Платформы
1. Вычисления для шагов 3, 7 выполнялись локально:
_OS: macOS Big Sur 10.16 23G80 arm64
Host: MacBookAir10,1
Kernel: 23.6.0
Shell: zsh 5.9
CPU: Apple M1
Memory: 1502MiB / 8192MiB_

2. Вычисления для шага 2 выполнялись локально:
_OS: Ubuntu 24.04.4 LTS x86_64
Kernel: 6.8.0-107-generic
Shell: bash 5.2.21
CPU: Intel Xeon E3-1230 v6 (4) @ 3.504GHz
Memory: 355MiB / 13678MiB_
