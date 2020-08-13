# Run_analysis
## opening the data in R

library(dplyr)

filename <- "Coursera_final.zip"

fileURL <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileURL, filename, method = "curl")

unzip(filename)
head(filename)

features <- read.table("UCI HAR Dataset/features.txt", col.names = c("n", "functions"))

activities <- read.table("UCI HAR Dataset/activity_labels.txt", col.names = c("code", "activity"))

subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")

x_test <- read.table("UCI HAR Dataset/test/x_test.txt", col.names = features$functions)

y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "code")

subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names = "subject")

x_train <- read.table("UCI HAR Dataset/train/x_train.txt", col.names = features$functions)

y_train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "code")

#### merge the datasets ####

x <- rbind(x_train, x_test)
y <- rbind(y_train, y_test)
subject <- rbind(subject_test, subject_train)
merged_data <- cbind(subject, y, x)



### 2 ###
head(merged_data)
Tidydata <- select(merged_data, subject, code, contains("mean"), contains("std"))
Tidydata

### 3 ###
Tidydatacode <- activities[Tidydata$code, 2]

### 4 ###
names(Tidydata)[2] = "activity"

names(Tidydata) <- gsub("Acc", "Accelerometer", names(Tidydata))

names(Tidydata) <- gsub("Gyro", "Gyroscope", names(Tidydata))

names(Tidydata) <- gsub("BodyBody", "Body", names(Tidydata))

names(Tidydata) <- gsub("Mag", "Magnitude", names(Tidydata))

names(Tidydata) <- gsub("^t", "Time", names(Tidydata))

names(Tidydata) <- gsub("^f", "Frequency", names(Tidydata))

names(Tidydata) <- gsub("tBody", "TimeBody", names(Tidydata))

names(Tidydata) <- gsub("-mean()", "Mean", names(Tidydata), ignore.case = T)

names(Tidydata) <- gsub("-std()", "STD", names(Tidydata), ignore.case = T)

names(Tidydata) <- gsub("-freq()", "Frequency", names(Tidydata), ignore.case = T)

names(Tidydata) <- gsub("angle", "Angle", names(Tidydata))

names(Tidydata) <- gsub("gravity", "Gravity", names(Tidydata))


### 5 ###
finaldata <- Tidydata %>%
  group_by(subject, activity) %>%
  summarize_all(funs(mean))

write.table(finaldata, "finaldata.txt", row.names = FALSE)

  