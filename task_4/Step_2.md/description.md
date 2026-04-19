The number of obtained proteins - 16435 (использовала команду: ```grep -c ">" augustus.whole.aa```)

### Шаг 3
На третьем шаге я выполнила пункт, который был recommended, то есть я сначала создала базу данных с помощью команды ```makeblastdb -in augustus.whole.aa -dbtype prot -out tardigrade_proteins_db```, а затем произвела поиск интересующих меня белков с помощью команды ```blastp -db tardigrade_proteins_db -query peptides.fa -out blast_results.txt -outfmt```, команда вывела мне файл blast_results.txt, который я положила в папочку. Далее мы попытались найти айди всех уникальных белков:
```awk '{print $2}' blast_results.txt | sort | uniq > protein_ids.txt``` - с помощью этой команды мы попросили терминал сделать нам файл с айди белков, затем с помощью ```wc -l protein_ids.txt``` - посчитали количество таких белков - 34 белка. Зптем нам надо было вытащить сами последовательности этих белков. Это мы делали с помощью команды ```seqtk subseq augustus.whole.aa protein_ids.txt > candidates.fa```

