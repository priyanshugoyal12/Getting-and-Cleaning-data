# Getting-and-Cleaning-data
## Downloading the data and unziping the data.

library(data.table)
fileurl = 'https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip'
if (!file.exists('./data1/dataset.zip'))
  {
download.file(fileurl,'./data1/dataset.zip', mode = 'wb')
unzip("./data1/dataset.zip", exdir = getwd())
}

## READING THE DATA AND MERGING IT.

features <- read.csv('./data1/dataset/features.txt', header = FALSE, sep = ' ')
features <- as.character(features[,2])
data.train.x <- read.table('./data1/dataset/train/X_train.txt')
# data.train.x as a variable for train data set(x_train)
data.train.y <- read.csv('./data1/dataset/train/y_train.txt', header = FALSE, sep = ' ')
#  data.train.y as a variable for train data set(y_train)
data.train.subject <- read.csv('./data1/dataset/train/subject_train.txt',header = FALSE, sep = ' ')
# data.train.subject as a variable for train data set(subject_train)
data.train <-  data.frame(data.train.subject, data.train.y, data.train.x)
# compiling the data into a data frame
names(data.train) <- c(c('subject', 'activity'), features)
data.test.x <- read.table('./data1/dataset/test/X_test.txt')
data.test.y <- read.csv('./data1/ataset/test/y_test.txt', header = FALSE, sep = ' ')
data.test.y <- read.csv('./data1/dataset/test/y_test.txt', header = FALSE, sep = ' ')
data.test.subject <- read.csv('./data1/dataset/test/subject_test.txt', header = FALSE, sep = ' ')
data.test <-  data.frame(data.test.subject, data.test.y, data.test.x)
names(data.test) <- c(c('subject', 'activity'), features)

## 1) Merges the training and the test sets to create one data set.
data.all <- rbind(data.train, data.test)


## 2) Extracts only the measurements on the mean and standard deviation for each measurement.
mean_std.select <- grep('mean|std', features)
data.sub <- data.all[,c(1,2,mean_std.select + 2)]


## 3) Uses descriptive activity names to name the activities in the data set
activity.labels <- read.table('./data1/dataset/activity_labels.txt', header = FALSE)
activity.labels <- as.character(activity.labels[,2])
data.sub$activity <- activity.labels[data.sub$activity]


## 4)Appropriately labels the data set with descriptive variable names.
name.new <- names(data.sub)
name.new <- gsub("[(][)]", "", name.new)
name.new <- gsub("^t", "TimeDomain_", name.new)
name.new <- gsub("^f", "FrequencyDomain_", name.new)
name.new <- gsub("Acc", "Accelerometer", name.new)
name.new <- gsub("Gyro", "Gyroscope", name.new)
name.new <- gsub("Mag", "Magnitude", name.new)
name.new <- gsub("-mean-", "_Mean_", name.new)
name.new <- gsub("-std-", "_StandardDeviation_", name.new)
name.new <- gsub("-", "_", name.new)
names(data.sub) <- name.new

## 5)From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
data.tidy <- aggregate(data.sub[,3:81], by = list(activity = data.sub$activity, subject = data.sub$subject),FUN = mean)
write.table(x = data.tidy, file = "data_tidy.txt", row.names = FALSE)
