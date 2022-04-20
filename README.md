```
# UsingR_inside_terminal
# now I need to load R by typing R in the command line 
 R
 # make a results table to view in R 
 results_table <-  read.table("results_matches.txt")
 # <- is assigning a variable for results_table and remember to use quotes when talking about files. 
 
 # tips - 
 dim(results_table) will give me numbers the first number is the number of rows in the table and the second number is the number of columns. - the number in square brackets first just refers to how many lines of output are generated if there are massive tables. 
 # to pull out a value specifically from the table 
 name_of_table[row_number,column_number] 
 # if i want to pull out an entire column or row you still need to use the comma but row/column (whatever you want to pull out completely) blank 
# e.g. 
results_table[1700,]

 # or 

results_table[,2]

# some other stuff we did to make sure we have the right number of lines in our file 
sum(results_table[,2])
# to quit R type 
q()
# to install a package in R (only have to do this the first time)
install.package ("name_of_package")
# pull it from library 
library(tidyverse)
# assigning variable to name and reading the results matches txt file.
results_table_tidyverse <- read_table("results_matches.txt")
# col names=FALSE to remove header of the table. delim is setting a space as the delimeter. 
results_table_tidyverse <- read_delim("results_matches.txt", delim=" ", col_names=FALSE)

# Within tidyverse, select selects columns, filter filters rows


# This is a pipe that shoots one thing into another
 %>%

# making something that gives us results that returned more than one match in our loop search  
multilple_gene_matches <- results_table_tidyverse %>% filter(X2>1)
# making something that gives us results that returned zero matches in our loop search
zero_gene_matches <- results_table_tidyverse %>% filter(X2==0)

# writing them out to a txt file so they are saved in the directory 
write_delim(multilple_gene_matches,"multiple_gene_matches.txt",delim=" ",col_names=FALSE)
write_delim(zero_gene_matches,"zero_gene_matches.txt",delim=" ",col_names=FALSE)


## some R stuff from 12th april using R to generate some tables and then loading into blast to search some stuff
module load R/4.1.0-gimkl-2020a

 

Type R to load R:

 

library(tidyverse)

 

results_table_tidyverse <- read_delim("results_matches.txt",delim=" ",col_names=FALSE)

 

multiple_gene_matches <- results_table_tidyverse %>% filter(X2>1)

 

zero_gene_matches <- results_table_tidyverse %>% filter(X2==0)

 

write_delim(multiple_gene_matches, "multiple_gene_matches.txt", delim=" ",col_names=FALSE)

 

write_delim(zero_gene_matches, "zero_gene_matches.txt", delim=" ",col_names=FALSE)

 

q()

 

# Starting to build code to figure this out. We will be working through our zero_matches file.

zero_matches_total_lines=`wc -l zero_gene_matches.txt | awk '{ print $1 }'`

 

for zero_match_line in `seq 1 1 $zero_matches_total_lines`; do echo $zero_match_line; done

 

zero_match_line=1

 

gene_search_term=`head -n $zero_match_line zero_gene_matches.txt | tail -n 1 | awk '{ print $1 }'`

 

grep "gene="$gene_search_term";" GCF_000001405.39_GRCh38.p13_genomic.gff  | grep $'\t'CDS$'\t' | cut -f1,4,5 > temp_human.gff

 

 

# Bedtools is 0 based, gff are 1-based. Is bedtools smart enough to go: hey you are using a gff I’ll automatically adjust that for you.

 

module load BEDTools/2.29.2-GCC-9.2.0

 

bedtools getfasta -fi GCF_000001405.39_GRCh38.p13_genomic.fna -bed temp_human.gff > temp_human.fna

 

# We did all of the the possum genome indexing on the 1st April so we do not need to do it again

word size = 11, expect value = 0.05, match/mismatch scores 2/-3, gapcosts 5,2 blastn

 

module load BLAST/2.12.0-GCC-9.2.0

 

blastn -task blastn -db GCF_011100635.1_mTriVul1.pri_genomic.fna -query temp_human.fna -evalue 0.05 -word_size 11 -gapopen 5 -gapextend 2 -penalty -3 -reward 2 -outfmt 6


Trouble shooting our weird grep message, which has appeared below the gene: MAOB

 

Line 381

 

mito_line=381

 

# Cool – added some new stuff to our loop to account for square brackets in the grep format. And then went on to redoing blast stuff

 

# This is changed from yesterday because Bedtools can handle gff format so we don’t need to get rid of all the other columns

grep "gene="$gene_search_term";" GCF_000001405.39_GRCh38.p13_genomic.gff  | grep $'\t'CDS$'\t' > temp_human.gff

 

Followed commands the same way as yesterday, but modified the blast command to save things into a file called temp.blast

 blastn -task blastn -db GCF_011100635.1_mTriVul1.pri_genomic.fna -query temp_human.fna -evalue 0.05 -word_size 11 -gapopen 5 -gapextend 2 -penalty -3 -reward 2 -outfmt 6 > temp.blast

 

R

library(tidyverse)
temp_blast <- read_delim("temp.blast",delim="\t",col_names=FALSE)

temp_blast %>% group_by(X1,X2) %>% summarise(sum_match=sum(X8)) %>% pivot_wider(names_from=X2,values_from=sum_match

 

