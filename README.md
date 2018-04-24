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


Code Book

The data columns are:

  Subject - An id of the experiment subject.
  Activity - Name of the activity performed.
  Other columns contains the following information as explained in the readme found in the data folder. 

The features selected for this database come from the accelerometer and gyroscope 3-axial raw signals tAcc-XYZ and tGyro-XYZ. These time domain signals (prefix 't' to denote time) were captured at a constant rate of 50 Hz. Then they were filtered using a median filter and a 3rd order low pass Butterworth filter with a corner frequency of 20 Hz to remove noise. Similarly, the acceleration signal was then separated into body and gravity acceleration signals (tBodyAcc-XYZ and tGravityAcc-XYZ) using another low pass Butterworth filter with a corner frequency of 0.3 Hz.

Subsequently, the body linear acceleration and angular velocity were derived in time to obtain Jerk signals (tBodyAccJerk-XYZ and tBodyGyroJerk-XYZ). Also the magnitude of these three-dimensional signals were calculated using the Euclidean norm (tBodyAccMag, tGravityAccMag, tBodyAccJerkMag, tBodyGyroMag, tBodyGyroJerkMag).

Finally a Fast Fourier Transform (FFT) was applied to some of these signals producing fBodyAcc-XYZ, fBodyAccJerk-XYZ, fBodyGyro-XYZ, fBodyAccJerkMag, fBodyGyroMag, fBodyGyroJerkMag. (Note the 'f' to indicate frequency domain signals).

These signals were used to estimate variables of the feature vector for each pattern:

The set of variables that were estimated from these signals are: mean(): Mean value std(): Standard deviation
tBodyAcc-mean()-X
tBodyAcc-mean()-Y
tBodyAcc-mean()-Z
tBodyAcc-std()-X
tBodyAcc-std()-Y
tBodyAcc-std()-Z
tGravityAcc-mean()-X
tGravityAcc-mean()-Y
tGravityAcc-mean()-Z
tGravityAcc-std()-X
tGravityAcc-std()-Y
tGravityAcc-std()-Z
tBodyAccJerk-mean()-X
tBodyAccJerk-mean()-Y
tBodyAccJerk-mean()-Z
tBodyAccJerk-std()-X
tBodyAccJerk-std()-Y
tBodyAccJerk-std()-Z
tBodyGyro-mean()-X
tBodyGyro-mean()-Y
tBodyGyro-mean()-Z
tBodyGyro-std()-X
tBodyGyro-std()-Y
tBodyGyro-std()-Z
tBodyGyroJerk-mean()-X
tBodyGyroJerk-mean()-Y
tBodyGyroJerk-mean()-Z
tBodyGyroJerk-std()-X
tBodyGyroJerk-std()-Y
tBodyGyroJerk-std()-Z
tBodyAccMag-mean()
tBodyAccMag-std()
tGravityAccMag-mean()
tGravityAccMag-std()
tBodyAccJerkMag-mean()
tBodyAccJerkMag-std()
tBodyGyroMag-mean()
tBodyGyroMag-std()
tBodyGyroJerkMag-mean()
tBodyGyroJerkMag-std()
fBodyAcc-mean()-X
fBodyAcc-mean()-Y
fBodyAcc-mean()-Z
fBodyAcc-std()-X
fBodyAcc-std()-Y
fBodyAcc-std()-Z
fBodyAccJerk-mean()-X
fBodyAccJerk-mean()-Y
fBodyAccJerk-mean()-Z
fBodyAccJerk-std()-X
fBodyAccJerk-std()-Y
fBodyAccJerk-std()-Z
fBodyGyro-mean()-X
fBodyGyro-mean()-Y
fBodyGyro-mean()-Z
fBodyGyro-std()-X
fBodyGyro-std()-Y
fBodyGyro-std()-Z
fBodyAccMag-mean()
fBodyAccMag-std()
fBodyBodyAccJerkMag-mean()
fBodyBodyAccJerkMag-std()
fBodyBodyGyroMag-mean()
fBodyBodyGyroMag-std()
fBodyBodyGyroJerkMag-mean()
fBodyBodyGyroJerkMag-std()
