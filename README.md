# Антонников Григорий ДЗ_1 Бионформатика


## Выбираем директорию

```
mkdir hw1
cd hw1
```
## Ссылки на данные

```
ln -s /usr/share/data-minor-bioinf/assembly/oil_R1.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oil_R2.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R1_001.fastq
ln -s /usr/share/data-minor-bioinf/assembly/oilMP_S4_L001_R2_001.fastq
```

## Формируем рандомные чтения 

```
SEED=1122
seqtk sample -s$SEED oil_R1.fastq 5000000 > pe_1.fastq
seqtk sample -s$SEED oil_R2.fastq 5000000 > pe_2.fastq
seqtk sample -s$SEED oilMP_S4_L001_R1_001.fastq 1500000 > mp_1.fastq
seqtk sample -s$SEED oilMP_S4_L001_R2_001.fastq 1500000 > mp_2.fastq
```

## Ремуваем ссылки

```
rm -r oil_R1.fastq
rm -r oil_R2.fastq
rm -r oilMP_S4_L001_R1_001.fastq
rm -r oilMP_S4_L001_R2_001.fastq
```

## Проверяем качество

```
qualityCheck() {
  rm -rf fastqc multiqc
  mkdir fastqc multiqc
  fastqc "$@" -o fastqc
  multiqc fastqc -o multiqc
}


qualityCheck pe_*.fastq mp*.fastq
```

## Обрезаем данные и удаляем старые 

```
platanus_trim pe_*.fastq
platanus_internal_trim mp_*.fastq
rm pe_*.fastq mp_*.fastq
```

## Проверяем качество обрезанных данных
```
qualityCheck *trimmed
```

## Собираем контиги, собираем скаффолды
```
platanus assemble -f *.trimmed
platanus scaffold -c out_contig.fa -IP1 *.trimmed -OP2 *.int_trimmed
platanus gap_close -c out_scaffold.fa -IP1 *.trimmed -OP2 *.int_trimmed
```
## Удаляем trimmed файлы
```
rm *trimmed
```
##  Самый длинный скайффолд в файл longest 
```
sed -n '1,/^>/p' scaffolds.fasta | head -n -1 >longest.fasta
```

##  Отчет для первоначальных чтений

![Image alt](https://github.com/greggasd/hse21_hw1/blob/main/image/1.1.png)
![Image alt](https://github.com/greggasd/hse21_hw1/blob/main/image/1.2.png)

## Отчет для обрезанных чтений

![Image alt](https://github.com/greggasd/hse21_hw1/blob/main/image/2.1.png)
![Image alt](https://github.com/greggasd/hse21_hw1/blob/main/image/2.2.png)
