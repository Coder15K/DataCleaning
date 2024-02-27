---
title: "CodeBook"
output: html_document
---

# DOWNLOAD AND UNZIP DATASET

```{r}
#DOWNLOAD AND EXTRACT DATASET
library(dplyr)
#url <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
#download.file(url,destfile= "/home/rstudio/data.zip", method="auto")
#unzip("/home/rstudio/data.zip", exdir = "/home/rstudio")

```

#PASS TO TABLES FOR PROCESSING

```{r}

#TEST
Features_Test<- read.table("/home/rstudio/UCI HAR Dataset/test/X_test.txt", header = F)
Activity_Test <- read.table("/home/rstudio/UCI HAR Dataset/test/y_test.txt", header = F)
Subject_Test <- read.table("/home/rstudio/UCI HAR Dataset/test/subject_test.txt", header = F)
#TRAIN
Features_Train <- read.table("/home/rstudio/UCI HAR Dataset/train/X_train.txt", header = F)
Activity_Train <- read.table("/home/rstudio/UCI HAR Dataset/train/y_train.txt", header = F)
Subject_Train <- read.table("/home/rstudio/UCI HAR Dataset/train/subject_train.txt", header = F)

#ACTIVITY LABELS
Activity_Labels <- read.table("/home/rstudio/UCI HAR Dataset/activity_labels.txt", header = F)
#FEATURE NAMES
Features_Names <- read.table("/home/rstudio/UCI HAR Dataset/features.txt", header = F)



```



#NAMES CHANGE

```{r}

colnames(Features_Test) <- Features_Names[,2]
colnames(Activity_Test) <- "Activity_ID"
colnames(Subject_Test) <- "Subject_ID"
colnames(Features_Train) <- Features_Names[,2]
colnames(Activity_Train) <- "Activity_ID"
colnames(Subject_Train) <- "Subject_ID"        
colnames(Activity_Labels) <- c("Activity_ID", "Activity_Type")

```

# MERGE AND ANSWERS 


```{r}

Merge_Train<- cbind(Features_Test,Activity_Test,Subject_Test)
Merge_Test <- cbind(Features_Train,Activity_Train,Subject_Train)
Final_Dataset <- rbind(Merge_Train,Merge_Test)

NAME_OF_COL <- colnames(Final_Dataset)

MEAN_SD <- (grepl("Activity_ID", NAME_OF_COL) |
              grepl("Subject_ID", NAME_OF_COL) |
              grepl("mean..", NAME_OF_COL) |
              grepl("std...", NAME_OF_COL))

DATA_MEAN_SD <- Final_Dataset[ , MEAN_SD == TRUE]

DATA_ACT_NAMES <- merge(DATA_MEAN_SD, Activity_Labels,by = "Activity_ID",all.x = TRUE)

TidySet <- aggregate(.~ Subject_ID + Activity_ID, DATA_ACT_NAMES, mean)
TidySet <- TidySet[order(TidySet$Subject_ID,TidySet$Activity_ID), ]
write.table(TidySet, "TidySet.txt", row.names = FALSE)

```










