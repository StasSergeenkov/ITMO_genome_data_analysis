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
Аналогично для 23andMe, но с изменениями (обозначение хромосом отличается):
```shell
cd /home/stass/ITMO_genome_data_analysis/HW5/step_3
mkdir -p 23andme

cp /home/stass/ITMO_genome_data_analysis/HW5/step_2/23andme/23andme_converted.vcf 23andme/

y_count=$(grep -v "^#" 23andme_converted.vcf | grep -c "^24")
x_count=$(grep -v "^#" 23andme_converted.vcf | grep -c "^23")

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
Аналогично для 23andMe:
```shell
echo "" >> 23andme/sex_eye_color.txt
echo "# Цвет глаз" >> 23andme/sex_eye_color.txt

# Функция для извлечения генотипа из VCF по rsid
get_genotype() {
    rsid=$1
    grep -w "$rsid" 23andme/23andme_converted.vcf | awk '{print $10}' | cut -d: -f1
}

rs12913832=$(get_genotype "rs12913832")
rs1800407=$(get_genotype "rs1800407")

echo "rs12913832 (HERC2): $rs12913832" >> 23andme/sex_eye_color.txt
echo "rs1800407 (OCA2): $rs1800407" >> 23andme/sex_eye_color.txt
```

Провели аннотацию genotek_clean.vcf:
```shell
SnpSift annotate /home/stass/ITMO_genome_data_analysis/HW5/databases/clinvar.vcf \
  /home/stass/ITMO_genome_data_analysis/HW5/step_3/genotek/genotek_clean.vcf \
  > /home/stass/ITMO_genome_data_analysis/HW5/step_3/genotek/genotek_annotated.vcf
```
Результат шага 3:
_для Genotek_
Пол: мужской (соотношение Y/X 0,1361)  
rs12913832 (HERC2): 0/1  
rs1800407 (OCA2): 0/0  
Предположительный цвет глаз: голубой/зелёный

_для 23andMe_
Пол: мужской (соотношение Y/X 0,1361)  
rs12913832 (HERC2): 0/1  
rs1800407 (OCA2): 0/0  
Предположительный цвет глаз: голубой/зелёный

## Шаг 4
Выполнили поиск pathogenic вариантов. Из них уже выбрали 5 конкретных вариантов по степени представленности в литературе.

| № | rsID | Ген | Заболевание | Тип мутации |
|---|------|-----|-------------|-------------|
| 1 | **rs387907304** | SKI | Shprintzen-Goldberg syndrome | missense |
| 2 | **rs397514590** | SKI | Familial thoracic aortic aneurysm | missense |
| 3 | **rs121434297** | MTHFR | Homocystinuria / Neural tube defects | missense |
| 4 | **rs786201085** | SDHB | Hereditary cancer-predisposing syndrome | missense |
| 5 | **rs397516835** | SDHB | Hereditary cancer-predisposing syndrome | missense |

Далее найдены все варианты:
=== rs387907304 ===
1	2160299	rs387907304	C	G	.	.	PR	GT	0/0
=== rs397514590 ===
1	2160308	rs397514590	C	T	.	.	PR	GT	0/0
=== rs121434297 ===
1	11855218	rs121434297	A	G	.	.	PR	GT	0/0
=== rs786201085 ===
1	17349110	rs786201085	C	T	.	.	PR	GT	0/0
=== rs397516835 ===
1	17350535	rs397516835	C	T	.	.	PR	GT	0/0





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
