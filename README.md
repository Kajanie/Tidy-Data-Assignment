# Tidy-Data-Assignment

This repo contains the getting and cleaning data peer review assigment.

Data source

The data used in the script below comes from UC Irvine's Human Activity Recognition Using Smartphones Data Set. The data used is here:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

More information about the data can be found here:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

Script

The script used is called run_analysis.R.

Requirements

Prior to running of the script, the data files should be in the working directory. 


Step by step explanation of the script

1. Load required packages.

if (!require("data.table")) {
  install.packages("data.table")
}
library(data.table)
library(dplyr)
library(reshape2)

2. Read files

subject_train   <- read.table("/Users/Jane/Desktop/DatasciencecourseR/data/UCI HAR Dataset/train/subject_train.txt")
X_train         <- read.table("/Users/Jane/Desktop/DatasciencecourseR/data/UCI HAR Dataset/train/X_train.txt")
Y_train         <- read.table("/Users/Jane/Desktop/DatasciencecourseR/data/UCI HAR Dataset/train/y_train.txt")
subject_test    <- read.table("/Users/Jane/Desktop/DatasciencecourseR/data/UCI HAR Dataset/test/subject_test.txt")
X_test          <- read.table("/Users/Jane/Desktop/DatasciencecourseR/data/UCI HAR Dataset/test/X_test.txt")
Y_test          <- read.table("/Users/Jane/Desktop/DatasciencecourseR/data/UCI HAR Dataset/test/y_test.txt")
features        <- read.table("/Users/Jane/Desktop/DatasciencecourseR/data/UCI HAR Dataset/features.txt")
activity_labels <- read.table("/Users/Jane/Desktop/DatasciencecourseR/data/UCI HAR Dataset/activity_labels.txt")


2. To label the activity column as "Activity", activity names from the activity_labels file are applied to the variables from the Y_ files:

        activity_train <- data.frame(Activity = activity_labels$V2[Y_train$V1])
        activity_test  <- data.frame(Activity = activity_labels$V2[Y_test$V1])

3. The lines below  carryout the labeling of the Data column from the subject_ files as "Subject".

        colnames(subject_train) <- c("Subject")
        colnames(subject_test) <- c("Subject")

4. The data columns from the X_ files are then labeled with the descriptions from the features file.

        colnames(X_train)       <- features$V2
        colnames(X_test)       <- features$V2

5. Combining the two sets of data with bind_cols:

        train <- bind_cols(subject_train, activity_train, X_train)
        test  <- bind_cols(subject_test, activity_test, X_test)
        Merge the training and the test sets to create one data set.

6. The two datasets are then combined to gether:

      data  <- rbind(train, test)

7. Subsetting only the measurements on the mean and standard deviation for each measurement.

The script takes the "Subject" and "Activity" columns and selects the columns with names containing "mean()" or "std()".

since the original data has some other columns with "Mean" in their names which are angle of means but are not means themselves,these columns excluded in the final data. A new file is also written which contains tidy data.

      cols <- c(1,2, grep("(mean|std)\\(\\)", colnames(data)))
      tidyData <- data[cols]
      write.table(tidyData, file="tidydata.txt", row.name=FALSE) 

8. A second, independent tidy data set with the average of each variable for each activity and each subject is created using the melt and dcast frm reshape2 packages.

        meltData <- melt(tidyData,id = c("Subject","Activity"))
        cleandata <- dcast(meltData, Subject + Activity ~ variable, mean)
        write.table(cleandata, file="tidy_data.txt", row.name=FALSE) 
