# curseproject
The code in this repository implements is named run_analysis.R and it creates a .txt file displayr
the Average value for standard deviation and mean for signals colected from 30 subjects using Galaxy Samsung
mobile phones.

To get the analysis done, run the run_analysis.R script on RStudio or R IDE environment; the localization of raw data are hardcoded on the script.

Variables defined in run_analysis.R



Initially we start defining the localization  from where the raw data must be uploaded
############################################################################ Start defining the directory name where the training and test file are loaded
  train_archive <- "C:/Users/USUARIO PC/Documents/Coursera- Getting and Cleaning Data/UCI HAR Dataset/train/X_train.txt" 
test_archive <- "C:/Users/USUARIO PC/Documents/Coursera- Getting and Cleaning Data/UCI HAR Dataset/test/X_test.txt"
activity_train_labels <- "C:/Users/USUARIO PC/Documents/Coursera- Getting and Cleaning Data/UCI HAR Dataset/train/y_train.txt"
activity_test_labels <-   "C:/Users/USUARIO PC/Documents/Coursera- Getting and Cleaning Data/UCI HAR Dataset/test/y_test.txt"

We read every raw data file into data frames to merge in a single data structure

############################################################################
# Load string with pathname to file where subjects are defined - subject_train and subject_test
subject_train <- "C:/Users/USUARIO PC/Documents/Coursera- Getting and Cleaning Data/UCI HAR Dataset/train/subject_train.txt"
subject_test <- "C:/Users/USUARIO PC/Documents/Coursera- Getting and Cleaning Data/UCI HAR Dataset/test/subject_test.txt"
############################################################################
# Load string with pathname to filu where activities are define
activity_names <- "C:/Users/USUARIO PC/Documents/Coursera- Getting and Cleaning Data/UCI HAR Dataset/activity_labels.txt"

############################################################################
# Load string with pathname to filu where columns names are defineds - features.txt
features_archive <-"C:/Users/USUARIO PC/Documents/Coursera- Getting and Cleaning Data/UCI HAR Dataset/features.txt"
############################################################################
# Load features file content in dataframe features
features <- read.table(features_archive) #Read file with column names
############################################################################
# Load test data on x_test_data dataframe
x_test_data <-read.table(test_archive)   
############################################################################
# Load train data on x_test_data dataframe
x_train_data <-read.table(train_archive)

############################################################################
# Load train Activity Labels
activity_train_data <- read.table(activity_train_labels)
activity_test_data <- read.table(activity_test_labels)
############################################################################
# Load train Activity Names
Activity_Names_Data <- read.table(activity_names)
############################################################################
# Load subject Labels and merge train and test Data
subject_train_data <- read.table(subject_train)
subject_test_data  <- read.table(subject_test)

After we have load  the raw data, we start to build a single data structure  we the individual data pieces
Firstly we join the training and test data structure with 561 colums displaying data colected by sensors
############################################################################
# Join  acivities from train and test and rename rows
Activity_Data_Merged <-  rbind.data.frame(activity_train_data,activity_test_data)

Secondly, we join  the test and train subject identifiers
############################################################################
Subject_Data_Merge <- rbind.data.frame(subject_train_data,subject_test_data)

The third step is to set up the activity name vector with train and test values and use it to name a vector to be joined to the data frame
############################################################################
# Load Activity Labels
Activity_Data <- read.table(activity_names)
############################################################################
# Join  acivities from train and test and rename rows
Activity_Data_Merged <-  rbind.data.frame(activity_train_data,activity_test_data)

############################################################################
# rename rows
Activity_Data_Merged$v1 <- Activity_Names_Data[,2][match(Activity_Data_Merged$V1,Activity_Names_Data[,1])]

###########################################################################################################
#  Extract 2 colum of features data frame to a character vector named featurs_vect

features_vect <- features[1:561,2]

###########################################################################################################
# Use the features_vect to name the columns on both dataframes x_train_data and x_test_data
# 4th Requirement of Programming Assignment: Appropriately labels the data set with descriptive variable names

colnames(x_train_data)<-features_vect

colnames(x_test_data) <-features_vect

###########################################################################################################
# 1st Requirement of Programming Assignment: Merge both test and train dataframes on one datafame named Data_Merged

Data_Merged <-  rbind.data.frame(x_train_data,x_test_data)

Data_Merged$ActivityNames <-Activity_Data_Merged$v1

Data_Merged$SubjectID <- Subject_Data_Merge$V1


##########################################################################################################
# 2nd Requirement of Programing Assignment: Extracts only the measurements on the mean and standard deviation for each measurement.

##########################################################################################################
# Stract columns I want to summarise - mean and std and join them in a single chacter vector

Vector_Std <-grep("-std",names(x_test_data),value = TRUE)
Vector_mean <-grep("-mean",names(x_test_data),value = TRUE)
Vector_to_Be_Analyzed <- c(Vector_mean,Vector_Std)

##########################################################################################################
# Transforme Data Frame in current Data Frame
Data_Merged_Tbl <- tbl_df(Data_Merged)
Nodup_Data_Merged <- Data_Merged_Tbl[,!duplicated(colnames(Data_Merged_Tbl))]

############################################################################
 Group Local Data Frame by Activity Names and Subject ID columns and sumarize only the columns with  Standard Deviation and Mean of the signals colected from Samsung Galaxy Mobile phones. Save in variable Tidy_Data

Tidy_Data <- Nodup_Data_Merged %>% group_by(ActivityNames,SubjectID) %>%  #n

