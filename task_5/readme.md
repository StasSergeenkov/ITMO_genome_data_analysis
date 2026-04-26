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
y_count=$(grep -v "^#" genotek.vcf | grep -c "^chrY")
x_count=$(grep -v "^#" genotek.vcf | grep -c "^chrX")

echo "Y: $y_count, X: $x_count, Ratio Y/X: $(echo "scale=4; $y_count/$x_count" | bc)"
```
Аналогично для 23andMe, но с изменениями (обозначение хромосом отличается):
```shell
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

Аналогично для 23andMe:
```shell
SnpSift annotate /home/stass/ITMO_genome_data_analysis/HW5/databases/clinvar.vcf \
  /home/stass/ITMO_genome_data_analysis/HW5/step_3/23andme/23andme_converted.vcf \
  > /home/stass/ITMO_genome_data_analysis/HW5/step_3/23andme/23andme_annotated.vcf
```

Результат шага 3:  
_для Genotek_  
Пол: мужской (соотношение Y/X 0,1361)  
rs12913832 (HERC2): 0/1  
rs1800407 (OCA2): 0/0  
Предположительный цвет глаз: голубой/зелёный  

_для 23andMe_  
Пол: мужской (соотношение Y/X 0,1110)  
rs12913832 (HERC2): 0/1  
rs1800407 (OCA2): 0/0  
Предположительный цвет глаз: голубой/зелёный

## Шаг 4
Выбор клинически значимых SNP осуществлялся в несколько шагов.  
Первым делом были найдены все варианты с мутацией:
```shell
echo "Количество вариантов с мутацией"
grep -v "^#" genotek_clean.vcf | awk '{if($10!="0/0" && $10!="0|0") print $0}' | wc -l
grep -v "^#" genotek_clean.vcf | awk '{if($10!="0/0" && $10!="0|0") print $0}' > mutations_real.txt
```

Клинически значимые из них получены так:
```shell
awk '{print $3}' mutations_real.txt > mutation_rsids.txt
grep -f mutation_rsids.txt genotek_annotated.vcf > mutations_clinical.txt
echo "Количество клинически значимых мутаций у индивида"
wc -l mutations_clinical.txt
```

Отфильтровали факторы риска:
```shell
grep "risk_factor" genotek_annotated.vcf | grep -f mutation_rsids.txt > mutations_risk.txt
echo "=== Количество risk_factor вариантов у индивида ==="
wc -l mutations_risk.txt
```

На основе полученных данных сформируем таблицу:
```shell
cat > real_mutations_detailed.txt << 'EOF'
EOF

while read line; do
    rsid=$(echo "$line" | awk '{print $3}')
    echo "" >> real_mutations_detailed.txt
    echo "=== $rsid ===" >> real_mutations_detailed.txt
    echo "Из VCF: $(grep -w "$rsid" genotek_clean.vcf)" >> real_mutations_detailed.txt
    echo "Из ClinVar: $(grep -w "$rsid" genotek_annotated.vcf | grep -o 'CLNDN=[^;]*' | head -1)" >> real_mutations_detailed.txt
    echo "Патогенность: $(grep -w "$rsid" genotek_annotated.vcf | grep -o 'CLNSIG=[^;]*' | head -1)" >> real_mutations_detailed.txt
done < <(head -10 mutations_risk.txt)
```

У индивида нет патогенных мутаций, реальные изменения нужно вносить на основе факторов риска. CRISPR-правки в данном случае направлены на снижение риска заболеваний, а не на исправление уже существующей болезни.
Выделены следующие SNP:

| № | rsID | Ген | Хромосома | Позиция (GRCh37) | Риск | Текущий GT | Желаемый GT | Замена (ALT-REF) |
|---|------|-----|-----------|------------------|------|------------|-------------|------------------|
| 1 | rs5174 | LRP8 | 1 | 53712727 | Инфаркт миокарда | 0/1 (C/T) | 0/0 (C/C) | T-C |
| 2 | rs3738401 | — | — | — | not_provided | 0/1 | 0/0 | — |
| 3 | rs6548238 | TMEM18 | 2 | 622051 | Ожирение | 0/1 (C/T) | 0/0 (C/C) | T-C |
| 4 | rs121909343 | MAPT | 17 | 44089581 | Синдром Перри, ALS | 0/1 (A/G) | 0/0 (A/A) | G-A |
| 5 | rs11558538 | HNMT | 2 | 138759649 | Астма, HNMT-нарушение | 0/1 (C/T) | 0/0 (C/C) | T-C |
| 6 | rs3792267 | CAPN10 | 2 | 241584825 | Сахарный диабет 2 типа | 0/1 (G/A) | 0/0 (G/G) | A-G |
| 7 | rs4402960 | IGF2BP2 | 3 | 185511013 | Сахарный диабет 2 типа | 0/1 (A/G) | 0/0 (A/A) | G-A |
| 8 | rs1368408 | — | — | — | Астма | 0/1 | 0/0 | — |
| 9 | rs7754840 | CDKAL1 | 6 | 20802271 | Ожирение | 0/1 (C/G) | 0/0 (C/C) | G-C |
| 10 | rs2071592 | — | — | — | Ревматоидный артрит | 0/1 | 0/0 | — |

Аналогично для 23andme (все действия единой командой):
```shell
echo "Количество вариантов с мутацией"
grep -v "^#" /home/stass/ITMO_genome_data_analysis/HW5/step_4/23andme/23andme_converted.vcf | awk '{if($10!="0/0" && $10!="0|0") print $0}' | wc -l
grep -v "^#" /home/stass/ITMO_genome_data_analysis/HW5/step_4/23andme/23andme_converted.vcf | awk '{if($10!="0/0" && $10!="0|0") print $0}' > mutations_real.txt

awk '{print $3}' mutations_real.txt > mutation_rsids.txt
grep -f mutation_rsids.txt /home/stass/ITMO_genome_data_analysis/HW5/step_4/23andme/23andme_annotated.vcf > mutations_clinical.txt
echo "Количество клинически значимых мутаций у индивида"
wc -l mutations_clinical.txt

grep "risk_factor" /home/stass/ITMO_genome_data_analysis/HW5/step_4/23andme/23andme_annotated.vcf | grep -f mutation_rsids.txt > mutations_risk.txt
echo "=== Количество risk_factor вариантов у индивида ==="
wc -l mutations_risk.txt

cat > real_mutations_detailed.txt << 'EOF'
EOF

while read line; do
    rsid=$(echo "$line" | awk '{print $3}')
    echo "" >> real_mutations_detailed.txt
    echo "=== $rsid ===" >> real_mutations_detailed.txt
    echo "Из VCF: $(grep -w "$rsid" /home/stass/ITMO_genome_data_analysis/HW5/step_4/23andme/23andme_converted.vcf)" >> real_mutations_detailed.txt
    echo "Из ClinVar: $(grep -w "$rsid" /home/stass/ITMO_genome_data_analysis/HW5/step_4/23andme/23andme_annotated.vcf | grep -o 'CLNDN=[^;]*' | head -1)" >> real_mutations_detailed.txt
    echo "Патогенность: $(grep -w "$rsid" /home/stass/ITMO_genome_data_analysis/HW5/step_4/23andme/23andme_annotated.vcf | grep -o 'CLNSIG=[^;]*' | head -1)" >> real_mutations_detailed.txt
done < <(head -10 mutations_risk.txt)
```

| № | rsID | Ген | Хромосома | Позиция (GRCh37) | Риск | Текущий GT | Желаемый GT | Замена (ALT-REF) |
|---|------|-----|-----------|------------------|------|------------|-------------|------------------|
| 1 | rs5174 | LRP8 | 1 | 53712727 | Инфаркт миокарда | 0/1 (C/T) | 0/0 (C/C) | T→C |
| 2 | rs2073658 | USF1 | 1 | 159277386 | Семейная комбинированная гиперлипидемия | 0/1 (A/G) | 0/0 (A/A) | G-A |
| 3 | i3000469 | — | — | — | Нарушение HNMT, астма | 0/1 | 0/0 | — |
| 4 | rs4402960 | IGF2BP2 | 3 | 185511013 | Сахарный диабет 2 типа | 0/1 (A/G) | 0/0 (A/A) | G-A |
| 5 | rs909253 | — | — | — | Псориатический артрит, инфаркт миокарда | 0/1 | 0/0 | — |
| 6 | rs4880 | SOD2 | 6 | 160113872 | Диабетические микрососудистые осложнения | 0/1 (A/G) | 0/0 (A/A) | G-A |
| 7 | rs1800795 | IL6 | 7 | 22766645 | Диабет 1 и 2 типа, болезнь Крона | 0/1 (G/C) | 0/0 (C/C) | G-C |
| 8 | rs7794745 | CNTNAP2 | 7 | 145813893 | Аутизм | 0/1 (A/T) | 0/0 (A/A) | T-A |
| 9 | rs13266634 | SLC30A8 | 8 | 118184783 | Сахарный диабет 2 типа | 0/1 (C/T) | 0/0 (C/C) | T-C |
| 10 | rs10757274 | CDKN2B-AS1 | 9 | 22096055 | Трехсосудистое поражение коронарных артерий | 0/1 (A/G) | 0/0 (A/A) | G-A |


Таблица сравнения двух наборов данных на основе предоставленных вами показателей:

| Показатель | Genotek | 23andMe |
|-----------|------------------------|------------------------------|
| **Количество SNP в исходном VCF** | 608 921 | 595 401 |
| **Количество аннотированных SNP** | 608 921 | 595 401 |
| **Количество вариантов** | 87 520 | 171 043 |
| **Количество pathogenic** | 2 045 | 12 |
| **Количество risk_factor** | 20 | 12 |

Пересечение rsID: 29 224 общих маркеров

Общие для обоих файлов:
rs5174 (LRP8) — инфаркт миокарда
rs11558538 (HNMT) — астма
rs4402960 (IGF2BP2) — диабет 2 типа
rs909253 (LTA) — псориатический артрит/инфаркт
rs4880 (SOD2) — оксидативный стресс
rs1800795 (IL6) — диабет, воспаление
rs13266634 (SLC30A8) — диабет 2 типа

Только в Genotek:
rs3738401 (DISC1) — шизофрения
rs6548238 — ожирение
rs3792267 (CAPN10) — диабет 2 типа
rs1368408 (SCGB3A2) — астма
rs7754840 (CDKAL1) — ожирение
rs2071592 (NFKBIL1) — ревматоидный артрит

Только в 23andMe:
rs2073658 (USF1) — гиперлипидемия
rs7794745 (CNTNAP2) — аутизм
rs10757274 (CDKN2B-AS1) — коронарная болезнь

Итоговый список согласованных мутаций:

| № | rsID | Ген | Заболевание/Риск | Текущий генотип | Желаемый генотип | Замена (ALT-REF) |
|---|------|-----|------------------|----------------|------------------|------------------|
| 1 | rs1800795 | IL6 | Сахарный диабет (тип 1 и 2), воспаление, синдром Капоши, болезнь Крона | 0/1 | 0/0 | G-C |
| 2 | rs4880 | SOD2 | Оксидативный стресс, микрососудистые осложнения диабета | 0/1 | 0/0 | G-A |
| 3 | rs13266634 | SLC30A8 | Сахарный диабет 2 типа | 0/1 | 0/0 | T-C |
| 4 | rs11558538 | HNMT | Бронхиальная астма | 0/1 | 0/0 | T-C |
| 5 | rs5174 | LRP8 | Инфаркт миокарда | 0/1 | 0/0 | T-C |
| 6 | rs4402960 | IGF2BP2 | Сахарный диабет 2 типа | 0/1 | 0/0 | T-G |
| 7 | rs909253 | LTA | Псориатический артрит, инфаркт миокарда | 0/1 | 0/0 | A-G |


# Платформы
1. Вычисления для шагов выполнялись локально:
_OS: Ubuntu 24.04.4 LTS on Windows 10 x86_64
Kernel: 6.6.87.2-microsoft-standard-WSL2
Shell: bash 5.2.21
CPU: Intel Ultra 9 285H (16) @ 3.686GHz
Memory: 408MiB / 15672MiB_
