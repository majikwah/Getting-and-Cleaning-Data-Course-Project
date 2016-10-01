
##Getting and Cleaning Data Course Assignment
###Goal

Companies like FitBit, Nike, and Jawbone Up are racing to develop the most advanced algorithms to attract new users. The data linked are collected from the accelerometers from the Samsung Galaxy S smartphone.

A full description is available at the site where the data was obtained:

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones

##Download the Data
The data is available at:

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

###Download, unzip and store in your working directory. 

For this project I stored the data in ~/Getting_Data_Assignment and I unzip sourcefiles in that folder not nested in UCI HAR Dataset

##Project Goal
The aim of the project is to clean and extract usable data from the above zip file. R script called run_analysis.R that does the following: - Merges the training and the test sets to create one data set. - Extracts only the measurements on the mean and standard deviation for each measurement. - Uses descriptive activity names to name the activities in the data set - Appropriately labels the data set with descriptive variable names. - From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

In this project, you find:

    *run_analysis.R : the R-code run on the data set

    *Tidy.txt : the clean data extracted from the original data using run_analysis.R

    *CodeBook.md : the CodeBook reference to the variables in Tidy.txt

    *README.md : the analysis of the code in run_analysis.R

    
##Getting Started
###Basic Assumption

The R code in run_analysis.R proceeds under the assumption that the zip file available at https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip is downloaded and extracted in the R Home Directory.
Libraries Used

The libraries used in this operation are data.table and dplyr. We prefer data.table as it is efficient in handling large data as tables. dplyr is used to aggregate variables to create the tidy data.
###Load the libraries

    if (!require("data.table")) {
            install.packages("data.table")
    }

    if (!require("dplyr")) {
        install.packages("dplyr")
    }

    require("data.table")
    require("dplyr")

##Read Supporting Metadata

The supporting metadata in this data are the name of the features and the name of the activities. They are loaded into variables featureNames and activityLabels.

featureNames <- read.table("Getting_Data_Assignment/features.txt")
activityLabels <- read.table("Getting_Data_Assignment/activity_labels.txt", header = FALSE)

##Format training and test data sets

Both training and test data sets are split up into subject, activity and features. They are present in three different files.
Read training data

    subjectTrain <- read.table("Getting_Data_Assignment/train/subject_train.txt", header = FALSE)
    activityTrain <- read.table("Getting_Data_Assignment/train/y_train.txt", header = FALSE)
    featuresTrain <- read.table("Getting_Data_Assignment/train/X_train.txt", header = FALSE)

##Read test data

    subjectTest <- read.table("Getting_Data_Assignment/test/subject_test.txt", header = FALSE)
    activityTest <- read.table("Getting_Data_Assignment/test/y_test.txt", header = FALSE)
    featuresTest <- read.table("Getting_Data_Assignment/test/X_test.txt", header = FALSE)

##Part 1 - Merge the training and the test sets to create one data set

We can use combine the respective data in training and test data sets corresponding to subject, activity and features. The results are stored in subject, activity and features.

    subject <- rbind(subjectTrain, subjectTest)
    activity <- rbind(activityTrain, activityTest)
    features <- rbind(featuresTrain, featuresTest)

##Naming the columns

The columns in the features data set can be named from the metadata in featureNames

    colnames(features) <- t(featureNames[2])

##Merge the data

The data in features,activity and subject are merged and the complete data is now stored in completeData.

    colnames(activity) <- "Activity"
    colnames(subject) <- "Subject"
    completeData <- cbind(features,activity,subject)

##Part 2 - Extracts only the measurements on the mean and standard deviation for each measurement

Extract the column indices that have either mean or std in them.

    columnsWithMeanSTD <- grep(".*Mean.*|.*Std.*", names(completeData), ignore.case=TRUE)

Add activity and subject columns to the list and look at the dimension of completeData

    requiredColumns <- c(columnsWithMeanSTD, 562, 563)
    dim(completeData)

    *1] 10299   563

We create extractedData with the selected columns in requiredColumns. And again, we look at the dimension of requiredColumns.

    extractedData <- completeData[,requiredColumns]
    dim(extractedData)

    *1] 10299    88

Part 3 - Uses descriptive activity names to name the activities in the data set

The activity field in extractedData is originally of numeric type. We need to change its type to character so that it can accept activity names. The activity names are taken from metadata activityLabels.

    extractedData$Activity <- as.character(extractedData$Activity)
    for (i in 1:6){
    extractedData$Activity[extractedData$Activity == i] <- as.character(activityLabels[i,2])
    }

We need to factor the activity variable, once the activity names are updated.

    extractedData$Activity <- as.factor(extractedData$Activity)

##Part 4 - Appropriately label the data set with descriptive variable names

Here are the names of the variables in extractedData

    names(extractedData)

* "tBodyAcc-mean()-X"                   
* "tBodyAcc-mean()-Y"                   
* "tBodyAcc-mean()-Z"                   
* "tBodyAcc-std()-X"                    
* "tBodyAcc-std()-Y"                    
* "tBodyAcc-std()-Z"                    
* "tGravityAcc-mean()-X"                
* "tGravityAcc-mean()-Y"                
* "tGravityAcc-mean()-Z"                
* "tGravityAcc-std()-X"                 
* "tGravityAcc-std()-Y"                 
* "tGravityAcc-std()-Z"                 
* "tBodyAccJerk-mean()-X"               
* "tBodyAccJerk-mean()-Y"               
* "tBodyAccJerk-mean()-Z"               
* "tBodyAccJerk-std()-X"                
* "tBodyAccJerk-std()-Y"                
* "tBodyAccJerk-std()-Z"                
* "tBodyGyro-mean()-X"                  
* "tBodyGyro-mean()-Y"                  
* "tBodyGyro-mean()-Z"                  
* "tBodyGyro-std()-X"                   
* "tBodyGyro-std()-Y"                   
* "tBodyGyro-std()-Z"                   
* "tBodyGyroJerk-mean()-X"              
* "tBodyGyroJerk-mean()-Y"              
* "tBodyGyroJerk-mean()-Z"              
* "tBodyGyroJerk-std()-X"               
* "tBodyGyroJerk-std()-Y"               
* "tBodyGyroJerk-std()-Z"               
* "tBodyAccMag-mean()"                  
* "tBodyAccMag-std()"                   
* "tGravityAccMag-mean()"               
* "tGravityAccMag-std()"                
* "tBodyAccJerkMag-mean()"              
* "tBodyAccJerkMag-std()"               
* "tBodyGyroMag-mean()"                 
* "tBodyGyroMag-std()"                  
*  tBodyGyroJerkMag-mean()"             
* "tBodyGyroJerkMag-std()"              
* "fBodyAcc-mean()-X"                   
* "fBodyAcc-mean()-Y"                   
* "fBodyAcc-mean()-Z"                   
* "fBodyAcc-std()-X"                    
* "fBodyAcc-std()-Y"                    
* "fBodyAcc-std()-Z"                    
* "fBodyAcc-meanFreq()-X"               
* "fBodyAcc-meanFreq()-Y"               
* "fBodyAcc-meanFreq()-Z"               
* "fBodyAccJerk-mean()-X"               
* "fBodyAccJerk-mean()-Y"               
* "fBodyAccJerk-mean()-Z"               
* "fBodyAccJerk-std()-X"                
* "fBodyAccJerk-std()-Y"                
* "fBodyAccJerk-std()-Z"                
* "fBodyAccJerk-meanFreq()-X"           
* "fBodyAccJerk-meanFreq()-Y"           
* "fBodyAccJerk-meanFreq()-Z"           
* "fBodyGyro-mean()-X"                  
* "fBodyGyro-mean()-Y"                  
* "fBodyGyro-mean()-Z"                  
* "fBodyGyro-std()-X"                   
* "fBodyGyro-std()-Y"                   
* "fBodyGyro-std()-Z"                   
* "fBodyGyro-meanFreq()-X"              
* "fBodyGyro-meanFreq()-Y"              
* "fBodyGyro-meanFreq()-Z"              
* "fBodyAccMag-mean()"                  
* "fBodyAccMag-std()"                   
* "fBodyAccMag-meanFreq()"              
* "fBodyBodyAccJerkMag-mean()"          
* "fBodyBodyAccJerkMag-std()"           
* "fBodyBodyAccJerkMag-meanFreq()"      
* "fBodyBodyGyroMag-mean()"             
* "fBodyBodyGyroMag-std()"              
* "fBodyBodyGyroMag-meanFreq()"         
* "fBodyBodyGyroJerkMag-mean()"         
* "fBodyBodyGyroJerkMag-std()"          
* "fBodyBodyGyroJerkMag-meanFreq()"     
* "angle(tBodyAccMean,gravity)"         
* "angle(tBodyAccJerkMean),gravityMean)"
* "angle(tBodyGyroMean,gravityMean)"    
* "angle(tBodyGyroJerkMean,gravityMean)"
* "angle(X,gravityMean)"                
* "angle(Y,gravityMean)"                
* "angle(Z,gravityMean)"                
* "Activity"                            
* "Subject"

By examining extractedData, we can say that the following acronyms can be replaced:

* Acc can be replaced with Accelerometer

* Gyro can be replaced with Gyroscope

* BodyBody can be replaced with Body

* Mag can be replaced with Magnitude

* Character f can be replaced with Frequency

* Character t can be replaced with Time

    names(extractedData)<-gsub("Acc", "Accelerometer", names(extractedData))
    names(extractedData)<-gsub("Gyro", "Gyroscope", names(extractedData))
    names(extractedData)<-gsub("BodyBody", "Body", names(extractedData))
    names(extractedData)<-gsub("Mag", "Magnitude", names(extractedData))
    names(extractedData)<-gsub("^t", "Time", names(extractedData))
    names(extractedData)<-gsub("^f", "Frequency", names(extractedData))
    names(extractedData)<-gsub("tBody", "TimeBody", names(extractedData))
    names(extractedData)<-gsub("-mean()", "Mean", names(extractedData), ignore.case = TRUE)
    names(extractedData)<-gsub("-std()", "STD", names(extractedData), ignore.case = TRUE)
    names(extractedData)<-gsub("-freq()", "Frequency", names(extractedData), ignore.case = TRUE)
    names(extractedData)<-gsub("angle", "Angle", names(extractedData))
    names(extractedData)<-gsub("gravity", "Gravity", names(extractedData))

###Here are the names of the variables in extractedData after they are edited

    names(extractedData)

* "TimeBodyAccelerometerMean()-X"                    
* "TimeBodyAccelerometerMean()-Y"                    
* "TimeBodyAccelerometerMean()-Z"                    
* "TimeBodyAccelerometerSTD()-X"                     
* "TimeBodyAccelerometerSTD()-Y"                     
* "TimeBodyAccelerometerSTD()-Z"                     
* "TimeGravityAccelerometerMean()-X"                 
* "TimeGravityAccelerometerMean()-Y"                 
* "TimeGravityAccelerometerMean()-Z"                 
* "TimeGravityAccelerometerSTD()-X"                  
* "TimeGravityAccelerometerSTD()-Y"                  
* "TimeGravityAccelerometerSTD()-Z"                  
* "TimeBodyAccelerometerJerkMean()-Y"                
* "TimeBodyAccelerometerJerkMean()-Z"                
* "TimeBodyAccelerometerJerkSTD()-X"                 
* "TimeBodyAccelerometerJerkSTD()-Y"                 
* "TimeBodyAccelerometerJerkSTD()-Z"                 
* "TimeBodyGyroscopeMean()-X"                        
* "TimeBodyGyroscopeMean()-Y"                        
* "TimeBodyGyroscopeMean()-Z"                        
* "TimeBodyGyroscopeSTD()-X"                         
* "TimeBodyGyroscopeSTD()-Y"                         
* "TimeBodyGyroscopeSTD()-Z"                         
* "TimeBodyGyroscopeJerkMean()-X"                    
* "TimeBodyGyroscopeJerkMean()-Z"                    
* "TimeBodyGyroscopeJerkSTD()-X"                     
* "TimeBodyGyroscopeJerkSTD()-Y"                     
* "TimeBodyGyroscopeJerkSTD()-Z"                     
* "TimeBodyAccelerometerMagnitudeSTD()"              
* "TimeGravityAccelerometerMagnitudeMean()"          
* "TimeGravityAccelerometerMagnitudeSTD()"           
* "TimeBodyAccelerometerJerkMagnitudeMean()"         
* "TimeBodyAccelerometerJerkMagnitudeSTD()"          
* "TimeBodyGyroscopeMagnitudeMean()"                 
* "TimeBodyGyroscopeMagnitudeSTD()"                  
* "TimeBodyGyroscopeJerkMagnitudeMean()"             
* "TimeBodyGyroscopeJerkMagnitudeSTD()"              
* "FrequencyBodyAccelerometerMean()-X"               
* "FrequencyBodyAccelerometerMean()-Y"               
* "FrequencyBodyAccelerometerMean()-Z"               
* "FrequencyBodyAccelerometerSTD()-X"                
* "FrequencyBodyAccelerometerSTD()-Y"                
* "FrequencyBodyAccelerometerSTD()-Z"                
* "FrequencyBodyAccelerometerMeanFreq()-X"           
* "FrequencyBodyAccelerometerMeanFreq()-Y"           
* "FrequencyBodyAccelerometerMeanFreq()-Z"           
* "FrequencyBodyAccelerometerJerkMean()-X"           
* "FrequencyBodyAccelerometerJerkMean()-Y"           
* "FrequencyBodyAccelerometerJerkMean()-Z"           
* "FrequencyBodyAccelerometerJerkSTD()-X"            
* "FrequencyBodyAccelerometerJerkSTD()-Y"            
* "FrequencyBodyAccelerometerJerkSTD()-Z"            
* "FrequencyBodyAccelerometerJerkMeanFreq()-X"       
* "FrequencyBodyAccelerometerJerkMeanFreq()-Y"       
* "FrequencyBodyAccelerometerJerkMeanFreq()-Z"       
* "FrequencyBodyGyroscopeMean()-X"                   
* "FrequencyBodyGyroscopeMean()-Y"                   
* "FrequencyBodyGyroscopeMean()-Z"                   
* "FrequencyBodyGyroscopeSTD()-X"                    
* "FrequencyBodyGyroscopeSTD()-Y"                    
* "FrequencyBodyGyroscopeSTD()-Z"                    
* "FrequencyBodyGyroscopeMeanFreq()-X"               
* "FrequencyBodyGyroscopeMeanFreq()-Y"               
* "FrequencyBodyGyroscopeMeanFreq()-Z"               
* "FrequencyBodyAccelerometerMagnitudeMean()"        
* "FrequencyBodyAccelerometerMagnitudeSTD()"         
* "FrequencyBodyAccelerometerMagnitudeMeanFreq()"    
* "FrequencyBodyAccelerometerJerkMagnitudeMean()"    
* "FrequencyBodyAccelerometerJerkMagnitudeSTD()"     
* "FrequencyBodyAccelerometerJerkMagnitudeMeanFreq()"
* "FrequencyBodyGyroscopeMagnitudeMean()"            
* "FrequencyBodyGyroscopeMagnitudeSTD()"             
* "FrequencyBodyGyroscopeMagnitudeMeanFreq()"        
* "FrequencyBodyGyroscopeJerkMagnitudeMean()"        
* "FrequencyBodyGyroscopeJerkMagnitudeSTD()"         
* "FrequencyBodyGyroscopeJerkMagnitudeMeanFreq()"    
* "Angle(TimeBodyAccelerometerMean,Gravity)"         
* "Angle(TimeBodyAccelerometerJerkMean),GravityMean)"
* "Angle(TimeBodyGyroscopeMean,GravityMean)"         
* "Angle(TimeBodyGyroscopeJerkMean,GravityMean)"     
* "Angle(X,GravityMean)"                             
* "Angle(Y,GravityMean)"                             
* "Angle(Z,GravityMean)"                             
* "Activity"                                         
* "Subject"

##Part 5 - From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject

###Firstly, let us set Subject as a factor variable.

    extractedData$Subject <- as.factor(extractedData$Subject)
    extractedData <- data.table(extractedData)

We create tidyData as a data set with average for each activity and subject. Then, we order the enties in tidyData and write it into data file Tidy.txt that contains the processed data.

    tidyData <- aggregate(. ~Subject + Activity, extractedData, mean)
    tidyData <- tidyData[order(tidyData$Subject,tidyData$Activity),]
    write.table(tidyData, file = "Tidy.txt", row.names = FALSE)

