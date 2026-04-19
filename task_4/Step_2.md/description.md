The number of obtained proteins - 16435 (использовала команду: ```grep -c ">" augustus.whole.aa```)

### Шаг 3
На третьем шаге я выполнила пункт, который был recommended, то есть я сначала создала базу данных с помощью команды ```makeblastdb -in augustus.whole.aa -dbtype prot -out tardigrade_proteins_db```, а затем произвела поиск интересующих меня белков с помощью команды ```blastp -db tardigrade_proteins_db -query peptides.fa -out blast_results.txt -outfmt```, команда вывела мне файл blast_results.txt, который я положила в папочку. Далее мы попытались найти айди всех уникальных белков:
```awk '{print $2}' blast_results.txt | sort | uniq > protein_ids.txt``` - с помощью этой команды мы попросили терминал сделать нам файл с айди белков, затем с помощью ```wc -l protein_ids.txt``` - посчитали количество таких белков - 34 белка. Зптем нам надо было вытащить сами последовательности этих белков. Это мы делали с помощью команды ```seqtk subseq augustus.whole.aa protein_ids.txt > candidates.fa```
### Шаг 4
Воспользовались WoLF PSORT, [вот результат](https://wolfpsort.hgc.jp/results/aKC5bd986bc0fe5cc455c89f80dd05743c7.html)
Воспользовались TargetP Server [результаты](https://services.healthtech.dtu.dk/cgi-bin/webface2.cgi?jobid=69E3A87E00171D0FFE60BDAD&wait=20)

### Шаг 5
Забластили файл candidates.fa, чтобы найти похожие последовательности белков, [вот что получилось](https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Get&RID=Y7BHKMDP014), также на всякий случай прикреплю в файлы скаченный файл. 
### Шаг 6
Предсказали функции пептидов с помощью Pfam HMMER [результаты](https://www.ebi.ac.uk/Tools/hmmer/results/6b3aee6c-a34e-4412-b1ee-c2156375e7f4/score)

### ШАГ7
# Candidate proteins summary: localization, BLAST hits, and Pfam domains

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
