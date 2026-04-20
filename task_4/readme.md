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
был получен файл blast_results.txt, (который доступен вот здесь)[task_4/step_3/blast_results.txt]
Далее осуществили поиск id всех уникальных белков:
```shell
# создание файла с id
awk '{print $2}' blast_results.txt | sort | uniq > protein_ids.txt

# подсчёт количества
wc -l protein_ids.txt
``` 

В резулььтате обнаружено 34 белка. 
Последовательности этих белков получены с помощью команды

```shell
seqtk subseq augustus.whole.aa protein_ids.txt > candidates.fa
```
## Шаг 4
Lля предсказания субклеточной локализации белков на основе их аминокислотной последовательности воспользовались WoLF PSORT, [вот результат](https://wolfpsort.hgc.jp/results/aKC5bd986bc0fe5cc455c89f80dd05743c7.html)  
Для уточнения локализации воспользовались TargetP Server: он предсказывает наличие сигнальных пептидов, митохондриальных или хлоропластных транзитных пептидов, [вот результат](https://services.healthtech.dtu.dk/cgi-bin/webface2.cgi?jobid=69E3A87E00171D0FFE60BDAD&wait=20)

## Шаг 5
Для поиска гомологичных белков в базах данных воспользовались веб версией blast, анализировали файл candidates.fa, [вот что получилось](https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Get&RID=Y7BHKMDP014). Файл с результатами доступен.

## Шаг 6
В веб версии Pfam HMMER произведен поиск доменов, предсказание функции пептидов, [результаты](https://www.ebi.ac.uk/Tools/hmmer/results/6b3aee6c-a34e-4412-b1ee-c2156375e7f4/score)

## ШАГ7
Итоговая таблица с кандидатами:

| Protein ID | Best BLAST hit (annotation, e-value) | Predicted Pfam domains | Probable localization(s) (WoLF PSORT) | Localization (TargetP-2.0) |
| :--- | :--- | :--- | :--- | :--- |
| g702.t1 | Uncharacterized protein P0DPW4.1 (e=1.50e-11) | Chitin binding Peritrophin-A domain | extr: 29, plas: 2, lyso: 1 | Signal peptide |
| g1285.t1 | Uncharacterized protein P0DPW4.1 (e=1.79e-12) | Chitin binding Peritrophin-A domain | extracellular (25), plasma membrane (5) | Signal peptide |
| g2203.t1 | Putative glucosidase GANAB (Q69ZQ1.2, e=2.44e-126) | Glycosyl hydrolases family 31 TIM-barrel domain, Glycosyl hydrolase family 31 C-terminal domain | plasma membrane (29), nucleus (2) | Other |
| g3428.t1 | Myosin regulatory light chain 2 (Q09510.1, e=8.78e-65) | EF-hand domain pair | mitochondria (18), cytoplasm (11) | Other |
| g3679.t1 | Uncharacterized protein (Q19269.2, e=7.44e-22) |  | extracellular (26), mitochondria (2) | Signal peptide |
| g4106.t1 | **No significant hits found** | *Fill in* | ER (14.5), ER/Golgi (9.5), extracellular (7) | Other |
| g4970.t1 | LDLRAD3 (Q9JIQ8.3, e=1.38e-15) | *Fill in* | plasma membrane (32) | Other |
| g5237.t1 | **No significant hits found** | *Fill in* | plasma membrane (24), mitochondria (8) | Other |
| g5443.t1 | **No significant hits found** | *Fill in* | extracellular (28), nucleus (3) | Other |
| g5467.t1 | Uncharacterized protein P0DPW4.1 (e=3.92e-13) | *Fill in* | extracellular (27), plasma membrane (4) | Signal peptide |
| g5502.t1 | Uncharacterized protein P0DPW4.1 (e=5.84e-14) | *Fill in* | extracellular (31) | Signal peptide |
| g5503.t1 | Uncharacterized protein P0DPW4.1 (e=6.75e-14) | *Fill in* | extracellular (29) | Signal peptide |
| g5510.t1 | **No significant hits found** | *Fill in* | plasma membrane (23), mitochondria (7) | Other |
| g5616.t1 | Uncharacterized protein P0DPW4.1 (e=2.25e-14) | *Fill in* | extracellular (31) | Signal peptide |
| g5641.t1 | Uncharacterized protein P0DPW4.1 (e=5.12e-13) | *Fill in* | extracellular (31) | Signal peptide |
| g5927.t1 | Zinc finger protein ZFP-1 (Q17427.1, e=1.08e-18) | *Fill in* | **nucleus (30.5)**, cyto_nucl (16.5) | Other |
| g7861.t1 | SWI/SNF complex component (B4F769.1, e=1.57e-71) | *Fill in* | **nucleus (16)**, cyto_nucl (14), cytoplasm (8) | Other |
| g8100.t1 | Uncharacterized protein (Q2YDR3.1, e=3.01e-46) | *Fill in* | **nucleus (16.5)**, cyto_nucl (12.5), cytoplasm (7.5) | Other |
| g8312.t1 | V-type proton ATPase subunit (Q5KU39.1, e=0.0) | *Fill in* | **nucleus (15.5)**, cyto_nucl (15.5), cytoplasm (12.5) | Other |
| g10513.t1 | **No significant hits found** | *Fill in* | **nucleus (20)**, cyto_nucl (14.5), cytoplasm (7) | Other |
| g10514.t1 | **No significant hits found** | *Fill in* | **nucleus (19)**, cyto_nucl (15), cytoplasm (9) | Other |
| g11320.t1 | **No significant hits found** | *Fill in* | plasma membrane (24.5), extr_plas (16) | Signal peptide |
| g11513.t1 | Uncharacterized protein (Q32PH0.1, e=6.72e-83) | *Fill in* | cytoplasm (17), cyto_nucl (12.8), cyto_mito (9.8) | Other |
| g11806.t1 | **No significant hits found** | *Fill in* | **nucleus (18)**, cyto_nucl (11.8), mitochondria (5) | Other |
| g11960.t1 | Nuclear protein (Q8CJB9.1, e=6.23e-98) | *Fill in* | **nucleus (32)** | Other |
| g12388.t1 | Uncharacterized protein P0DPW4.1 (e=2.77e-11) | *Fill in* | extracellular (25), plasma membrane (4) | Signal peptide |
| g12510.t1 | **No significant hits found** | *Fill in* | plasma membrane (29), cytoplasm (3) | Other |
| g12562.t1 | Uncharacterized protein P0DPW4.1 (e=7.17e-13) | *Fill in* | extracellular (30) | Signal peptide |
| g13530.t1 | **No significant hits found** | *Fill in* | extracellular (13), nucleus (6.5), lysosome (5) | Signal peptide |
| g14472.t1 | **Identical to P0DOW4.1** (e=0.0, 100% identity) | *Fill in* | **nucleus (28)**, plasma membrane (2) | Other |
| g15153.t1 | Uncharacterized protein P0DPW4.1 (e=1.89e-14) | *Fill in* | extracellular (32) | Signal peptide |
| g15484.t1 | Histone acetyltransferase (Q155U0.1, e=0.0) | *Fill in* | **nucleus (17.5)**, cyto_nucl (15.3), cytoplasm (12) | Other |
| g16318.t1 | **No significant hits found** | *Fill in* | **nucleus (20.5)**, cyto_nucl (13), extracellular (5) | Other |
| g16368.t1 | **No significant hits found** | *Fill in* | **nucleus (20.5)**, cyto_nucl (13), extracellular (5) | Other |

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
