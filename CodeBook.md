# Getting and Cleaning Data Course Assignment Week 3#
##  ##

**DESCRIPTION:** 
Human Activity Recognition Using Smartphones Dataset
Version 1.0 Contains experiments have been carried out with a group of 30 volunteers within an age bracket of 19-48 years. Each person performed six activities (WALKING, WALKING_UPSTAIRS, WALKING_DOWNSTAIRS, SITTING, STANDING, LAYING) wearing a smartphone (Samsung Galaxy S II) on the waist. Using its embedded accelerometer and gyroscope, we captured 3-axial linear acceleration and 3-axial angular velocity at a constant rate of 50Hz. The experiments have been video-recorded to label the data manually. The obtained dataset has been randomly partitioned into two sets, where 70% of the volunteers was selected for generating the training data and 30% the test data. 
 
**PURPOSE:** The purpose of this project is to collect, work with, and clean a data set.


 
**SOURCE DATA LOCATION:** 
[https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip ](https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip)

## Steps ##
## Getting Reference Data ##

- Read features and activity label:

   		1. feature_names <- read.table("UCI HAR Dataset/features.txt")
   		2. activity_labels <- read.table("UCI HAR Dataset/activity_labels.txt", header = FALSE)
 
- Read train data.


		1. subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", header = FALSE)
		2. activity_train <- read.table("UCI HAR Dataset/train/y_train.txt", header = FALSE)
		3. features_train <- read.table("UCI HAR Dataset/train/X_train.txt", header = FALSE)
		4. subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", header = FALSE)
		5. activity_test <- read.table("UCI HAR Dataset/test/y_test.txt", header = FALSE)
		6. features_test <- read.table("UCI HAR Dataset/test/X_test.txt", header = FALSE)

## Step 1: Merges the training and the test sets to create one data set. ##
- Use rbind for subject, activity and features.

		subject <- rbind(subject_train, subject_test)    
		activity <- rbind(activity_train, activity_test)
		features <- rbind(features_train, features_test)

- Naming activity, subject and features columns.

		colnames(features) <- t(feature_names[2])
		colnames(activity) <- "Activity"
		colnames(subject) <- "Subject"

- Merge and produce full set data.

		full_set_data <- cbind(subject, activity,features)

## Step 2: Extracts only the measurements on the mean and standard deviation for each measurement. ##


- Match and filter the column indices with name for mean or standard deviation.

		mean_std_column <- grep(".*mean.*|.*std.*", names(full_set_data))

- Get require column, subject, activity and columns with mean or standard deviation.
 
		required_column <- c( 1, 2, mean_std_column)


- Extract require data into file required_data.txt

		required_data <- full_set_data[,required_column]
		write.table(required_data, file = "required_data.txt", row.names = FALSE)

## Step 3: Uses descriptive activity names to name the activities in the data set. ##

- Get activity column.

		required_data$Activity <- as.character(required_data$Activity)

- Loop all data, match same activity and replace with activity label.

		for (i in 1:6){
		    required_data$Activity[required_data$Activity == i] <- as.character(activity_labels[i,2])
		}

## Step 4: Appropriately labels the data set with descriptive variable names. ##

- Change columns name to better naming.

		names(required_data)<-gsub("Acc", "Accelerometer", names(required_data))
		names(required_data)<-gsub("Gyro", "Gyroscope", names(required_data))
		names(required_data)<-gsub("BodyBody", "Body", names(required_data))
		names(required_data)<-gsub("Mag", "Magnitude", names(required_data))
		names(required_data)<-gsub("^t", "Time", names(required_data))
		names(required_data)<-gsub("^f", "Frequency", names(required_data))
		names(required_data)<-gsub("tBody", "TimeBody", names(required_data))
		names(required_data)<-gsub("-mean()", "Mean", names(required_data))
		names(required_data)<-gsub("-std()", "STD", names(required_data))
		names(required_data)<-gsub("-freq()", "Frequency", names(required_data))
		names(required_data)<-gsub("angle", "Angle", names(required_data))
		names(required_data)<-gsub("gravity", "Gravity", names(required_data))


## Step 5: From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject. ##

- Factor data Subject and Activity for grouping.
 
		required_data$Subject <- as.factor(required_data$Subject)
		required_data$Activity <- as.factor(required_data$Activity)


- Aggregation

		tidy_data <- suppressWarnings(aggregate
						(required_data,list(Activity = required_data$Activity, Subject = required_data$Subject), mean))

- Remove NA columns.
 
		tidy_data <- tidy_data[-c(3:4)]

- Extract require data into file tidy_data.txt

		write.table(tidy_data, "tidy_data.txt", row.names = FALSE) # write out the dataset

