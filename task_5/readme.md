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

## Шаг 3
Для определения пола искали Y-хромосомные SNP:
```shell
cd /home/stass/ITMO_genome_data_analysis/HW5/step_3
mkdir -p genotek

cp /home/stass/ITMO_genome_data_analysis/HW5/step_2/genotek/genotek.vcf genotek/

y_count=$(grep -v "^#" genotek.vcf | grep -c "^chrY")
x_count=$(grep -v "^#" genotek.vcf | grep -c "^chrX")

echo "Y: $y_count, X: $x_count, Ratio Y/X: $(echo "scale=4; $y_count/$x_count" | bc)"
```
Для определения цвета глаз извлекли генотипы SNP из VCF:
```shell
echo "" >> genotek/sex_eye_color.txt
echo "# Цвет глаз" >> genotek/sex_eye_color.txt

# Функция для извлечения генотипа из VCF по rsid
get_genotype() {
    rsid=$1
    grep -w "$rsid" genotek/genotek_clean.vcf | awk '{print $10}' | cut -d: -f1
}

rs12913832=$(get_genotype "rs12913832")
rs1800407=$(get_genotype "rs1800407")

echo "rs12913832 (HERC2): $rs12913832" >> genotek/sex_eye_color.txt
echo "rs1800407 (OCA2): $rs1800407" >> genotek/sex_eye_color.txt
```


Провели аннотацию genotek_clean.vcf:
```shell
SnpSift annotate /home/stass/ITMO_genome_data_analysis/HW5/databases/clinvar.vcf \
  /home/stass/ITMO_genome_data_analysis/HW5/step_3/genotek/genotek_clean.vcf \
  > /home/stass/ITMO_genome_data_analysis/HW5/step_3/genotek/genotek_annotated.vcf
```
Результат шага 3:  
Пол: мужской (соотношение Y/X 0,1361)  
rs12913832 (HERC2): 0/1  
rs1800407 (OCA2): 0/0  
Предположительный цвет глаз: голубой/зелёный или карий (гетерозигота)




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
