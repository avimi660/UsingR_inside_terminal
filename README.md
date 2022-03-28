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
