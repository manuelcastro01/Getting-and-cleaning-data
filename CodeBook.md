# Getting and Cleaning Data Course Project

## The Process

1. Import the required libraries: </br>
  library("dplyr")
  library(stringr)
  
2. Use wd <- getwd() to get the current working directory

3. Get the Activiy labels </br>
activityLabels <- read.table(paste(wd, "/UCI HAR Dataset/activity_labels.txt", sep =""))
activityLabels <- as.character(activityLabels[,2])

featureLabels <- read.table(paste(wd, "/UCI HAR Dataset/features.txt", sep =""))
featuresW <- grep("(mean|std)\\(\\)", featureLabels[, 2])

features <- featureLabels[featuresW,2]
measures <- str_replace_all(features, "[()]","")

4. Load the train dataset </br>

trainx <- read.table(paste(wd, "/UCI HAR Dataset/train/X_train.txt", sep =""))
trainy <- read.table(paste(wd, "/UCI HAR Dataset/train/y_train.txt", sep =""))
subject_train <- read.table(paste(wd, "/UCI HAR Dataset/train/subject_train.txt", sep =""))
trainx <- trainx[,featuresW]

names(subject_train) <- c("Subject")
names(trainx) <- measures
names(trainy) <- c("Activity")

train <- cbind(subject_train, trainx, trainy)


  
5. Load the test set </br>

testx <- read.table(paste(wd, "/UCI HAR Dataset/test/X_test.txt", sep =""))
testy <- read.table(paste(wd, "/UCI HAR Dataset/test/y_test.txt", sep =""))
subject_test <- read.table(paste(wd, "/UCI HAR Dataset/test/subject_test.txt", sep =""))
names(subject_test) <- c("Subject")

testx <- testx[,featuresW]

names(subject_test) <- c("Subject")
names(testx) <- measures
names(testy) <- c("Activity")

test <- cbind(subject_test, testx, testy)

6. Merging the datasets </br>
mds <- rbind(train, test)

mds$ActivityName <- activityLabels[mds$Activity]

mds <- mds[complete.cases(mds),]

tidyds <- mds %>% group_by(Subject, ActivityName) %>% 
  summarise_at(vars(1:(ncol(mds)-2)), list(name = mean))
  
7. Save the dataset </br>
write.table(tidyds,"tidy.txt",sep="\t",row.names=FALSE)
  
## The Datset

The final datset will have 69 columns that represents the HAR variables described by the UCI, the user id and the actvity name that could be useful in a further Machine Learning Analysys

 [1] "Subject"                        "ActivityName"                  
 [3] "tBodyAcc-mean-X_name"           "tBodyAcc-mean-Y_name"          
 [5] "tBodyAcc-mean-Z_name"           "tBodyAcc-std-X_name"           
 [7] "tBodyAcc-std-Y_name"            "tBodyAcc-std-Z_name"           
 [9] "tGravityAcc-mean-X_name"        "tGravityAcc-mean-Y_name"       
[11] "tGravityAcc-mean-Z_name"        "tGravityAcc-std-X_name"        
[13] "tGravityAcc-std-Y_name"         "tGravityAcc-std-Z_name"        
[15] "tBodyAccJerk-mean-X_name"       "tBodyAccJerk-mean-Y_name"      
[17] "tBodyAccJerk-mean-Z_name"       "tBodyAccJerk-std-X_name"       
[19] "tBodyAccJerk-std-Y_name"        "tBodyAccJerk-std-Z_name"       
[21] "tBodyGyro-mean-X_name"          "tBodyGyro-mean-Y_name"         
[23] "tBodyGyro-mean-Z_name"          "tBodyGyro-std-X_name"          
[25] "tBodyGyro-std-Y_name"           "tBodyGyro-std-Z_name"          
[27] "tBodyGyroJerk-mean-X_name"      "tBodyGyroJerk-mean-Y_name"     
[29] "tBodyGyroJerk-mean-Z_name"      "tBodyGyroJerk-std-X_name"      
[31] "tBodyGyroJerk-std-Y_name"       "tBodyGyroJerk-std-Z_name"      
[33] "tBodyAccMag-mean_name"          "tBodyAccMag-std_name"          
[35] "tGravityAccMag-mean_name"       "tGravityAccMag-std_name"       
[37] "tBodyAccJerkMag-mean_name"      "tBodyAccJerkMag-std_name"      
[39] "tBodyGyroMag-mean_name"         "tBodyGyroMag-std_name"         
[41] "tBodyGyroJerkMag-mean_name"     "tBodyGyroJerkMag-std_name"     
[43] "fBodyAcc-mean-X_name"           "fBodyAcc-mean-Y_name"          
[45] "fBodyAcc-mean-Z_name"           "fBodyAcc-std-X_name"           
[47] "fBodyAcc-std-Y_name"            "fBodyAcc-std-Z_name"           
[49] "fBodyAccJerk-mean-X_name"       "fBodyAccJerk-mean-Y_name"      
[51] "fBodyAccJerk-mean-Z_name"       "fBodyAccJerk-std-X_name"       
[53] "fBodyAccJerk-std-Y_name"        "fBodyAccJerk-std-Z_name"       
[55] "fBodyGyro-mean-X_name"          "fBodyGyro-mean-Y_name"         
[57] "fBodyGyro-mean-Z_name"          "fBodyGyro-std-X_name"          
[59] "fBodyGyro-std-Y_name"           "fBodyGyro-std-Z_name"          
[61] "fBodyAccMag-mean_name"          "fBodyAccMag-std_name"          
[63] "fBodyBodyAccJerkMag-mean_name"  "fBodyBodyAccJerkMag-std_name"  
[65] "fBodyBodyGyroMag-mean_name"     "fBodyBodyGyroMag-std_name"     
[67] "fBodyBodyGyroJerkMag-mean_name" "fBodyBodyGyroJerkMag-std_name" 
[69] "Activity_name"       
