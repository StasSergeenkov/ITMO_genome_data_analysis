The number of obtained proteins - 16435 (использовала команду: ```grep -c ">" augustus.whole.aa```)

### Шаг 3
На третьем шаге я выполнила пункт, который был recommended, то есть я сначала создала базу данных с помощью команды ```makeblastdb -in augustus.whole.aa -dbtype prot -out tardigrade_proteins_db```, а затем произвела поиск интересующих меня белков с помощью команды ```blastp -db tardigrade_proteins_db -query peptides.fa -out blast_results.txt -outfmt```, команда вывела мне файл blast_results.txt, который я положила в папочку
