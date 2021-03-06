# Getting and Cleaning Data Course Project

http://archive.ics.uci.edu/ml/datasets/Human+Activity+Recognition+Using+Smartphones 

Here are the data for the project: 

https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip 

	1. You should create one R script called run_analysis.R that does the following. 
	2. Merges the training and the test sets to create one data set.
	3. Extracts only the measurements on the mean and standard deviation for each measurement. 
	4. Uses descriptive activity names to name the activities in the data set
	5. Appropriately labels the data set with descriptive variable names. 
	6. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.


* Download data files from website. Download and unzip. 

	fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
	download.file(fileUrl, destfile = "Dataset.zip", method = "curl")
	unzip("Dataset.zip")


## 1. Merges the training and the test sets to create one data set.

* Load text file X_train.txt and X_test.txt. Then merge the X_data (Train and Test) - XYZ -axis data from the accelerometer and gyroscope  
        
        train_x <- read.table("UCI HAR Dataset/train/X_train.txt")
        test_x <- read.table("UCI HAR Dataset/test/X_test.txt")

* rbind to merge the (test and Train) datasets. rdind combines x-rows data to one. 
        
        merge_xdata <- rbind(test_x, train_x)

* Load text file X_train.txt and X_test.txt. Merge the Y_data (Train and Test) - ID data from the accelerometer and gyroscope 
 
        train_y <- read.table("UCI HAR Dataset/train/Y_train.txt")
        test_y <- read.table("UCI HAR Dataset/test/Y_test.txt")

* rbind to merge the (test and Train) datasets. rbind combines id-rows y data to one. 

        merge_ydata <- rbind(test_y, train_y )
        names(merge_ydata) <- "activity"

* Merge the Subject_data (subject_train and subject_test)

        train_subject <- read.table("UCI HAR Dataset/train/subject_train.txt")
        test_subject <- read.table("UCI HAR Dataset/test/subject_test.txt")

* rbind to the (test and Train) rows Subject data 

        merge_subject <- rbind(test_subject, train_subject)
        names(merge_subject) <- "subjectID"

## 2. Extracts only the measurements on the mean and standard deviation for each measurement. 
        
* Load - Features List has all the 561 Variables (columns names)

        mean_SD_features_list <- read.table("UCI HAR Dataset/features.txt")
        
* Pick column two Varibale names (column names)

        mean_SD_Fl <- mean_SD_features_list[,2]

* Add columns names to the top of merge_xdata

        colnames(merge_xdata) <- mean_SD_Fl

* Find mean and std columns in merge X data 

        mean_data_X <- merge_xdata[, grep("-mean\\(\\)" , colnames(merge_xdata))]
        STD_data_X <- merge_xdata[, grep("-std\\(\\)" , colnames(merge_xdata))]

* Extracts measurements on the mean and standard deviation (Column name with data)By using cbind to make one dataset mean_STD_X

        mean_STD_X <- cbind(mean_data_X, STD_data_X)

## 3. Uses descriptive activity names to name the activities in the data set

        activities_name_list <- read.table("UCI HAR Dataset/activity_labels.txt")
        merge_ydata[merge_ydata == 1] <- "WALKING"
        merge_ydata[merge_ydata == 2] <- "WALKING_UPSTAIRS"
        merge_ydata[merge_ydata == 3] <- "WALKING_DOWNSTAIRS"
        merge_ydata[merge_ydata == 4] <- "SITTING"
        merge_ydata[merge_ydata == 5] <- "STANDING"
        merge_ydata[merge_ydata == 6] <- "LAYING"
        
## 4. Appropriately labels the data set with descriptive variable names. 

* Cbind add merge_xdata, merge_ydata with all variable labels variable has 10299 obs and 562 variables all with labels variable_name is a Tidy dataset 

        variable_name <- cbind(merge_ydata, merge_xdata)
    

## 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

    Using the reshape2 library, use the melt function to collapse the dataset. I create a molten dataset with the melt function, and then use the dcast function to collapse the molten set into a new collapsed and tidy data frame. Finally, I write the tidy dataset to a txt file.
        
* Combind subjectID + activity + mean data

        bind_tidy <- cbind(merge_subject, variable_name)

* create the tidy data set

        melted_tidyData <- melt(bind_tidy , id=c("subjectID","activity"))
        tidyData <- dcast(melted_tidyData, subjectID+activity ~ variable, mean)

* Save to a text file 

        write.table(tidyData, file = "Tidy.txt", row.names = FALSE)