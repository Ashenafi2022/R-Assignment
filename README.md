# R-Assignment
This is the R-Assignment Repository

# Molly's assignment reviewed by Ashenafi Beyi
# Date: March 22, 2021

---
  title: "Molly-Assignment"
author: "Ashenafi Beyi"
date: "3/22/2021"
output: html_document
---
  ---
  title: "R_assignment_README"
author: "Molly Bledsoe"
date: "3/18/2021"
output: html_document
---
  
  #Data Inspection: fang_et_al_genotypes.txt
  
#  Import genotypes and snp_positions data from repository

fang_genotypes <- read.table("https://raw.githubusercontent.com/EEOB-BioData/BCB546-Spring2021/master/assignments/UNIX_Assignment/fang_et_al_genotypes.txt",
                             header=T, sep="\t", stringsAsFactors =F)
dim(fang_genotypes) # 2782 observations/rows and 986 variables/columns

snp_position <- read.table("https://raw.githubusercontent.com/EEOB-BioData/BCB546-Spring2021/master/assignments/UNIX_Assignment/snp_position.txt",
                           header=T, sep="\t", stringsAsFactors =F)
dim(snp_position) # 983 observations or rows and 15 variables or columns
head(snp_position) 


##Commands used to inspect fang_genotypes:
typeof(fang_genotypes)
file.size("fang_et_al_genotypes.txt")
length(fang_genotypes)
dim(fang_genotypes)
nrow(fang_genotypes)
ncol(fang_genotypes)
names(fang_genotypes)
class(fang_genotypes)
sapply(fang_genotypes,class)
str(fang_genotypes)

##What I learned from inspecting this file:
##This file is a list
##This file has a size of 11054722 
##This file contains 986 elements
##This file has dimensions of 2782 x 986
##This file has 2782 rows
##This file has 986 columns
##This file's class is data.frame
##his file contains base-pairs (known and unknown)found on chromosomes and the Sample_ID


##Commands used to inspect snp:
typeof(snp_position)
file.size("snp_position.txt")
length(snp_position)
dim(snp_position)
nrow(snp_position)
ncol(snp_position)
names(snp_position)
class(snp_position)
sapply(snp_position,class)
str(snp_position)

##What I learned from inspecting this file:
##This file is a list
##This file has a size of 83747
##This file contains 15 elements
##This file has dimensions of 983 x 15
##This file has 983 rows
##This file has 15 columns
##The first row contains SNP_ID
##The third row contains the Chromosome numbers
##The forth row contains the positions

# Data Processing:

##Grab the maize and teosinte groups:
library(dplyr)
maize <- filter(fang_genotypes, Group=="ZMMIL" | Group=="ZMMLR" | Group=="ZMMMR")

teosinte <- filter(fang_genotypes, Group=="ZMPBA" | Group=="ZMPIL" | Group=="ZMPJA")  

##Grab the desired snp_position columns:

snp_select <- snp_position[c(1,3,4)]

##Transpose maize and teosinte files:

   # reading: https://tibble.tidyverse.org/reference/rownames.html
library(tidyverse)
maize1 <- column_to_rownames(maize, var = "Sample_ID")
maize_transposed <- t(maize1)%>%as.data.frame()%>%rownames_to_column(., var = "SNP_ID")
maize_transposed <- maize_transposed[3:nrow(maize_transposed),]


teosinte1 <- column_to_rownames(teosinte, var = "Sample_ID")
teosinte_transposed <- t(teosinte1)%>%as.data.frame()%>%rownames_to_column(., var = "SNP_ID")
teosinte_transposed <- teosinte_transposed[3:nrow(teosinte_transposed),]


##Join the maize/teosinte_transposed files with the snp_organized file:

joined_snp_maize_transposed <- merge(snp_select, maize_transposed, by="SNP_ID")
table(joined_snp_maize_transposed$Chromosome) # if you want to see the frequencies of chromosomes
  #  1       10        2        3        4        5        6        7        8        9 multiple  unknown 
  # 155       53      127      107       91      122       76       97       62       60        6       27 

joined_snp_teosinte_transposed <- merge(snp_select, teosinte_transposed, by="SNP_ID")
table(joined_snp_teosinte_transposed$Chromosome)
table(joined_snp_teosinte_transposed$Chromosome)

   # 1       10        2        3        4        5        6        7        8        9 multiple  unknown 
   #  155       53      127      107       91      122       76       97       62       60        6       27

##Set Chromosome and Position values as numeric: why?

joined_snp_maize_transposed$Position <- as.numeric(joined_snp_maize_transposed$Position)
joined_snp_maize_transposed$Chromosome <- as.numeric(joined_snp_maize_transposed$Chromosome) # Why?

joined_snp_teosinte_transposed$Position <- as.numeric(joined_snp_teosinte_transposed$Position)
joined_snp_teosinte_transposed$Chromosome <- as.numeric(joined_snp_teosinte_transposed$Chromosome)

##Remove SNPS at "unknown" and "multiple" Positions and Chromosomes:

joined_maize <- joined_snp_maize_transposed[!(joined_snp_maize_transposed$Position == "unknown") | !(joined_snp_maize_transposed$Chromosome == "unknown")| !(joined_snp_maize_transposed$Chromosome == "multiple"),]
joined_maize <- na.omit(joined_maize)

joined_teosinte <- joined_snp_teosinte_transposed[!(joined_snp_teosinte_transposed$Position == "unknown") | !(joined_snp_teosinte_transposed$Chromosome == "unknown")| !(joined_snp_teosinte_transposed$Chromosome == "multiple"),]
joined_teosinte <- na.omit(joined_maize)

##Filter joined files by Chromosome numbers:

maize_chr1 <- joined_maize[joined_maize$Chromosome=="1",]
maize_chr2 <- joined_maize[joined_maize$Chromosome=="2",]
maize_chr3 <- joined_maize[joined_maize$Chromosome=="3",]
maize_chr4 <- joined_maize[joined_maize$Chromosome=="4",]
maize_chr5 <- joined_maize[joined_maize$Chromosome=="5",]
maize_chr6 <- joined_maize[joined_maize$Chromosome=="6",]
maize_chr7 <- joined_maize[joined_maize$Chromosome=="7",]
maize_chr8 <- joined_maize[joined_maize$Chromosome=="8",]
maize_chr9 <- joined_maize[joined_maize$Chromosome=="9",]
maize_chr10 <- joined_maize[joined_maize$Chromosome=="10",]

# Alternative approach
#...........................................................................
maize_chromosome1 <- subset(joined_maize, Chromosome==1)%>%arrange(Position)
teosinte_chromosome1 <- subset(joined_teosinte, Chromosome==1)%>%arrange(Position)

maize_chromosome.dec1 <- subset(joined_maize, Chromosome==1)%>%arrange(desc(Position))
teosinte_chromosome.dec1 <- subset(joined_teosinte, Chromosome==1)%>%arrange(desc(Position))
#..........................................................................

teosinte_chr1 <- joined_teosinte[joined_teosinte$Chromosome=="1",]
teosinte_chr2 <- joined_teosinte[joined_teosinte$Chromosome=="2",]
teosinte_chr3 <- joined_teosinte[joined_teosinte$Chromosome=="3",]
teosinte_chr4 <- joined_teosinte[joined_teosinte$Chromosome=="4",]
teosinte_chr5 <- joined_teosinte[joined_teosinte$Chromosome=="5",]
teosinte_chr6 <- joined_teosinte[joined_teosinte$Chromosome=="6",]
teosinte_chr7 <- joined_teosinte[joined_teosinte$Chromosome=="7",]
teosinte_chr8 <- joined_teosinte[joined_teosinte$Chromosome=="8",]
teosinte_chr9 <- joined_teosinte[joined_teosinte$Chromosome=="9",]
teosinte_chr10 <- joined_teosinte[joined_teosinte$Chromosome=="10",]

##Organizing Chromosome files with SNPs in increasing position values:

library(tidyverse)

increasing_maize_chr1 <- arrange(maize_chr1, Position)
increasing_maize_chr2 <- arrange(maize_chr2, Position)
increasing_maize_chr3 <- arrange(maize_chr3, Position)
increasing_maize_chr4 <- arrange(maize_chr4, Position)
increasing_maize_chr5 <- arrange(maize_chr5, Position)
increasing_maize_chr6 <- arrange(maize_chr6, Position)
increasing_maize_chr7 <- arrange(maize_chr7, Position)
increasing_maize_chr8 <- arrange(maize_chr8, Position)
increasing_maize_chr9 <- arrange(maize_chr9, Position)
increasing_maize_chr10 <- arrange(maize_chr10, Position)

increasing_teosinte_chr1 <- arrange(teosinte_chr1, Position)
increasing_teosinte_chr2 <- arrange(teosinte_chr2, Position)
increasing_teosinte_chr3 <- arrange(teosinte_chr3, Position)
increasing_teosinte_chr4 <- arrange(teosinte_chr4, Position)
increasing_teosinte_chr5 <- arrange(teosinte_chr5, Position)
increasing_teosinte_chr6 <- arrange(teosinte_chr6, Position)
increasing_teosinte_chr7 <- arrange(teosinte_chr7, Position)
increasing_teosinte_chr8 <- arrange(teosinte_chr8, Position)
increasing_teosinte_chr9 <- arrange(teosinte_chr9, Position)
increasing_teosinte_chr10 <- arrange(teosinte_chr10, Position)

##Organizing Chromosome files with SNPs in decreasing position values:

decreasing_maize_chr1 <- arrange(maize_chr1, desc(Position))
decreasing_maize_chr2 <- arrange(maize_chr2, desc(Position))
decreasing_maize_chr3 <- arrange(maize_chr3, desc(Position))
decreasing_maize_chr4 <- arrange(maize_chr4, desc(Position))
decreasing_maize_chr5 <- arrange(maize_chr5, desc(Position))
decreasing_maize_chr6 <- arrange(maize_chr6, desc(Position))
decreasing_maize_chr7 <- arrange(maize_chr7, desc(Position))
decreasing_maize_chr8 <- arrange(maize_chr8, desc(Position))
decreasing_maize_chr9 <- arrange(maize_chr9, desc(Position))
decreasing_maize_chr10 <- arrange(maize_chr10, desc(Position))

decreasing_teosinte_chr1 <- arrange(teosinte_chr1, desc(Position))
decreasing_teosinte_chr2 <- arrange(teosinte_chr2, desc(Position))
decreasing_teosinte_chr3 <- arrange(teosinte_chr3, desc(Position))
decreasing_teosinte_chr4 <- arrange(teosinte_chr4, desc(Position))
decreasing_teosinte_chr5 <- arrange(teosinte_chr5, desc(Position))
decreasing_teosinte_chr6 <- arrange(teosinte_chr6, desc(Position))
decreasing_teosinte_chr7 <- arrange(teosinte_chr7, desc(Position))
decreasing_teosinte_chr8 <- arrange(teosinte_chr8, desc(Position))
decreasing_teosinte_chr9 <- arrange(teosinte_chr9, desc(Position))
decreasing_teosinte_chr10 <- arrange(teosinte_chr10, desc(Position))

##Changing missing values from "?" to "-" in the decreasing files:

decreasing_maize_chr1[decreasing_maize_chr1 == '?/?'] <- '-/-'
decreasing_maize_chr2[decreasing_maize_chr2 == '?/?'] <- '-/-'
decreasing_maize_chr3[decreasing_maize_chr3 == '?/?'] <- '-/-'
decreasing_maize_chr4[decreasing_maize_chr4 == '?/?'] <- '-/-'
decreasing_maize_chr5[decreasing_maize_chr5 == '?/?'] <- '-/-'
decreasing_maize_chr6[decreasing_maize_chr6 == '?/?'] <- '-/-'
decreasing_maize_chr7[decreasing_maize_chr7 == '?/?'] <- '-/-'
decreasing_maize_chr8[decreasing_maize_chr8 == '?/?'] <- '-/-'
decreasing_maize_chr9[decreasing_maize_chr9 == '?/?'] <- '-/-'
decreasing_maize_chr10[decreasing_maize_chr10 == '?/?'] <- '-/-'

decreasing_teosinte_chr1[decreasing_teosinte_chr1 == '?/?'] <- '-/-'
decreasing_teosinte_chr2[decreasing_teosinte_chr2 == '?/?'] <- '-/-'
decreasing_teosinte_chr3[decreasing_teosinte_chr3 == '?/?'] <- '-/-'
decreasing_teosinte_chr4[decreasing_teosinte_chr4 == '?/?'] <- '-/-'
decreasing_teosinte_chr5[decreasing_teosinte_chr5 == '?/?'] <- '-/-'
decreasing_teosinte_chr6[decreasing_teosinte_chr6 == '?/?'] <- '-/-'
decreasing_teosinte_chr7[decreasing_teosinte_chr7 == '?/?'] <- '-/-'
decreasing_teosinte_chr8[decreasing_teosinte_chr8 == '?/?'] <- '-/-'
decreasing_teosinte_chr9[decreasing_teosinte_chr9 == '?/?'] <- '-/-'
decreasing_teosinte_chr10[decreasing_teosinte_chr10 == '?/?'] <- '-/-'

#Part Two: Plotting

##Transpose our full dataset:

fang_genotypes11 <- column_to_rownames(fang_genotypes, var = "Sample_ID")
fang_transposed <- t(fang_genotypes11)%>%as.data.frame()%>%rownames_to_column(., var = "SNP_ID")
fang_transposed <- fang_transposed[3:nrow(fang_transposed),]

##Join the tranposed file with the organizes SNP file:

joined_fang <- merge(snp_select, fang_transposed, by="SNP_ID")
  

##Plot One - SNPs per Chromosome:

joined_fang$Chromosome <- factor(joined_fang$Chromosome, levels = c("1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "multiple", "unknown", "NA"))

snps_per_chromosome_plot <- ggplot(joined_fang, aes(x= Chromosome) ) + geom_histogram(stat= "Count", color = "blue", fill = "white") + ggtitle ("Number of SNPs per Chromosome")

snps_per_chromosome_plot

##Plot Two - SNP Distribution on each Chromosome:

snps_distribution_per_chromosome_plot <- ggplot(joined_fang, aes(x= Chromosome, y= Position))+ geom_point(stat=, color = "red", alpha= 0.01)+ ggtitle ("SNPs Distribution per Chromosome")

snps_distribution_per_chromosome_plot

##Plot Three - Amount of Heterozygosity:

###Melt the data set:
install.packages("reshape")
library(reshape)

fang_headers <- colnames(fang_genotypes)[-c(1:3)]
melted_fang <- melt(fang_genotypes, measure.vars = fang_headers)

###Rename missing values:

melted_fang[ melted_fang == "?/?"]= "No_Data"

###Create new column for Homozygous values:

melted_fang$Homozygous <- (melted_fang$value=="A/A" | melted_fang$value== "C/C" | melted_fang$value== "G/G" | melted_fang$value== "T/T")

###Organize the dataframe by Sample IDs:

fang_samples_organized <- melted_fang[order(melted_fang$Sample_ID),]

library(plyr)

amounts_homozygous_heterozygous <- ddply(fang_samples_organized, c("Sample_ID"), summarise, heterozygous_amount=sum(!Homozygous, na.rm=TRUE), homozygous_amount=sum(Homozygous, na.rm=TRUE), No_Data=sum(is.na(Homozygous)))

melted_amounts <- melt(amounts_homozygous_heterozygous, measure.vars= c("heterozygous_amount", "homozygous_amount", "No_Data"))

###Making the Missing Data and Amount of Heterozygosity Plot:

missing_heterozygosity_plot <- ggplot(melted_amounts, aes(x= Sample_ID, y= value, fill=variable)) + geom_bar(stat= "identity", position = "stack") + ggtitle("Missing Data and Amount of Heterozygosity")

missing_heterozygosity_plot

##Plot Four - Missing Data and Amount of Heterozygosity per Group:

###Organize the datafram by groups:

fang_groups_organized <- melted_fang[order(melted_fang$Group),]

amounts_groups <- ddply(fang_groups_organized, c("Group"), summarise, heterozygous_amount=sum(!Homozygous, na.rm=TRUE), homozygous_amount=sum(Homozygous, na.rm=TRUE), No_Data=sum(is.na(Homozygous)))

melted_groups <- melt(amounts_groups, measure.vars= c("heterozygous_amount", "homozygous_amount", "No_Data"))

###Making the plot:

missing_data_in_groups_plot <- ggplot(melted_groups, aes(x= Group, y= value, fill= variable)) + geom_bar(stat="identity", position = "fill") + ggtitle("Missing data and Amount of Heterozygosity per Group")

missing_data_in_groups_plot

##Making my own Visualization - Plotting How many samples per Group

samples_per_group_plot <- ggplot(melted_fang, aes(x= Group))+geom_bar(stat= "Count", color = "purple", fill = "purple") +ggtitle ("Number of Samples per Group")+ theme(axis.text.x = element_text(angle = 90))

samples_per_group_plot

