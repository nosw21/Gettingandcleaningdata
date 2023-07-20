# Gettingandcleaningdata

##This is the part where we are designating the variables##

if(!file.exists("./data")){
fileUrl <- " 
https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./data/Dataset.zip",method="curl")
}
features <- read.table("UCI HAR Dataset/features.txt", col.names= c("n", "functions"))
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names= "subject")
activities <- read.table("UCI HAR Dataset/activity_labels.txt", col.names= c("code", "activity"))
x_test <- read.table("UCI HAR Dataset/test/X_test.txt", col.names= features$functions)
y_test <- read.table("UCI HAR Dataset/test/y_test.txt", col.names= "code")
x_train <- read.table("UCI HAR Dataset/train/X_train.txt", col.names= features$functions)
subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt", col.names= "subject")
y_train <- read.table("UCI HAR Dataset/train/y_train.txt", col.names= "code")

##This is where we are starting to bind / combine datasets##

x <- rbind(x_train, x_test)
y <- rbind(y_train, y_test)
subject <- rbind(subject_train, subject_test)
Merged_Data <- cbind(subject, y, x)
TidyData <- Merged_Data %>% select(subject, code, contains("mean"), contains("std"))
TidyData$code <- activities[TidyData$code, 2]
names(TidyData)[2] = "activity"                        
names(TidyData)<-gsub("Acc", "Accelerometer", names(TidyData))
names(TidyData)<-gsub("Gyro", "Gyroscope", names(TidyData))
names(TidyData)<-gsub("BodyBody", "Body", names(TidyData))
names(TidyData)<-gsub("Mag", "Magnitude", names(TidyData))
names(TidyData)<-gsub("^t", "Time", names(TidyData))
names(TidyData)<-gsub("^f", "Frequency", names(TidyData))
names(TidyData)<-gsub("tBody", "TimeBody", names(TidyData))
names(TidyData)<-gsub("-mean()", "Mean", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("-std()", "STD", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("-freq()", "Frequency", names(TidyData), ignore.case = TRUE)
names(TidyData)<-gsub("angle", "Angle", names(TidyData))
names(TidyData)<-gsub("gravity", "Gravity", names(TidyData))

##This is where we are starting to create the output##

FinalData <- TidyData %>%
  group_by(subject, activity) %>%
  summarise_all(funs(mean))
write.table(FinalData, "FinalData.txt", row.name=FALSE)
str(FinalData)
FinalData
