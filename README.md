Project-Getting-and-Cleaning-Data
=================================

The final goal of this project is obtain a tidy data from a rar source of data by following some steps.

1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement. 
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names. 
5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

All these steps are done by script run_analysis.R. Next, I am going to explain some details by steps.

1. Merges the training and the test sets to create one data set.
In this first part, I have read train and test data and add subject and activity. For this porpouse I have used read.table, cbind and rbind.
The dimension of final data is 10290 by 563. Extracts only the measurements on the mean and standard deviation for each measurement. 

2. Extracts only the measurements on the mean and standard deviation for each measurement. 
For the next part I have obtain the features and using grep function look for features with mean and std in their names, which are the measurement  that we are interested in. The dimension after that is  10299 by 81.

3. Uses descriptive activity names to name the activities in the data set
As the name of variable are a little bit confuse and using the text file that describes variable, I have changed tha names of the variables. For example, t is named Time, f FrequencyDomain, Acc Accelerometer, Gyro Gyrocospe and so on.

4. Appropriately labels the data set with descriptive variable names. 
Using the label of the previous step and the function name I have obtained the label for data.

5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
For this last step I have used dplyr library and function group_by and summarise_each to obtain the average of each variable for each activity and each subject.

The code is written next
```
# Merges the training and the test sets to create one data set
X_train <- read.table('./train/X_train.txt')
y_train <- read.table('./train/y_train.txt')
subject_train <- read.table('./train/subject_train.txt')
X_test <- read.table('./test/X_test.txt')
y_test <- read.table('./test/y_test.txt')
subject_test <- read.table('./test/subject_test.txt')
train <- cbind(X_train, subject_train, y_train)
test <- cbind(X_test, subject_test, y_test)
data <- rbind(train,test)
features <- read.table('./features.txt')

# Extracts only the measurements on the mean and standard deviation for each measurement. 

std_index <- grep('std',features[,2])
data_std <- data[,std_index]
mean_index <- grep('mean',features[,2])
data_mean <- data[,mean_index]
data_index <- cbind(data_std, data_mean)

# Uses descriptive activity names to name the activities in the data set
features_change1 <- gsub('tB','TimeB',features[,2])
features_change2 <- gsub('tG','TimeB',features_change1)
features_change3 <- gsub('fB','FrequencyDomainB',features_change2)
features_change4 <- gsub('fG','FrequencyDomainB',features_change3)
features_change5 <- gsub('-','.',features_change4)
features_change6 <- gsub('()','',features_change5)
features_change7 <- gsub('Acc','Accelerometer',features_change6)
features_change8 <- gsub('Gyro','Gyroscope',features_change7)
features_change9 <- gsub('Mag','Magnitude:EuclideanNorm',features_change8)
features_change10 <- gsub('sma','SignalMagnitudeArea',features_change9)
features_change11 <- gsub('iqr','InterquartileRange',features_change10)

# Appropriately labels the data set with descriptive variable names.
index <- c(std_index, mean_index)
features_final <- features_change11[index]
Subject <- data[,562]
Activity <- data[,563]
names(data_index) <-features_final
data_final <- cbind(data_index,Subject, Activity)

# From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
library(dplyr)
group <- group_by(data_final, Activity)
data_tidy_act <-summarise_each(group,funs(mean))
group2 <- group_by(data_final, Subject)
data_tidy_sub <-summarise_each(group2,funs(mean))
data_tidy <-rbind(data_tidy_act ,data_tidy_sub)

write.table(data_tidy,"./TidyData.txt")
```
