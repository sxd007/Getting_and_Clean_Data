library(plyr); library(dplyr); library(tidyr)
fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl, destfile = "./rawdata.zip", method ="curl" )
# unzip rawdata.zip in linux
setwd("/home/alpha/UCI HAR Dataset")

## 1. Merges the training and the test sets to create one data set.

#read data from downloaded and unzipped folders

traindata <- read.table("./train/X_train.txt", head=FALSE)
testdata <- read.table("./test/X_test.txt", head = FALSE)

dim(traindata); dim(testdata)
#[1] 7352  561
#[1] 2947  561
# same col length but different nrows.

features <- read.table("features.txt", head = FALSE)
activitylable <- read.table("activity_labels.txt", head = FALSE, col.names = c("ID","lable"))

# load lable and subject for train and test respectively
trainlable <- read.table("./train/y_train.txt", head= FALSE)
trainsubject <- read.table("./train/subject_train.txt", head=FALSE) 
testlable <- read.table("./test/y_test.txt", head= FALSE)
testsubject <-read.table("./test/subject_test.txt", head=FALSE) 

#we can tell the nrows of traindata/testdata equals to length of subject table and lable table
#to merge the dataset of training and test, should use the row combine, and need to lable it's train or test
#what are the variable name for 561 col? if dim(features), you may find it equals to numbers of features

# mutate a mark of train/test to each of the dataset - this step is no need eventually.
#traindata$type <- "train"; testdata$type <- "test"
mergedata <- rbind(traindata, testdata)
names(mergedata)       

## 2. Extracts only the measurements on the mean and standard deviation for each measurement. 

extract <- as.logical(grepl("mean\\()|std\\()", features$V2)) # a logic vector with TRUE for either mean() or std() as measurement

#extract[562] =TRUE # add 1 length to vector as a col of type added to original dataset- no need eventually
names(mergedata) <- as.character(features$V2)
mergedata2 <- mergedata[,extract]

## 3. Uses descriptive activity names to name the activities in the data set
trainlable$lable <- activitylable$lable[match(trainlable$V1,activitylable$ID)] #add lables description to y_train IDs
testlable$lable <- activitylable$lable[match(testlable$V1, activitylable$ID)]
names(trainlable) <- c("lableID","lableDesc")  #refine the names of table trainlable
names(testlable) <- c("lableID","lableDesc")
mergeactivity <- rbind(trainlable, testlable)

## 4. Appropriately labels the data set with descriptive variable names. 

# add subject column to dataset 
mergesubj <- rbind(trainsubject, testsubject)
names(mergesubj) <- "subject"

mergedata3 <- cbind(mergesubj, mergeactivity, mergedata2)

## 5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject. 

#the new dataset contains (30 subject X 66 varibles X 6 activities) dimensions
# set up factors applied to calculate mean of variables
mergedata3$level <- as.factor(paste(mergedata3[,1],mergedata3[,3]))

#define a dataframe to store the tidy data
finaldata <- matrix(data = NA, nrow = 180, ncol = 66)
#calculate the mean of the each varianbles for each subject and each activities
for(i in 1:66){
        dt <- array(NA)
        dt <- melt(tapply(mergedata3[,i+3],mergedata3$level,mean))
        finaldata[,i] <- dt[,2]
}
#separate the factors back to 2 variables = subject and activities
rownames = as.data.frame(levels(mergedata3$level)); names(rownames) <- "X" 
        newrn <- separate(rownames, X, into = c("subject", "activity"), sep = " ", remove = TRUE)
finaldata <- cbind(newrn, finaldata)

#set up column names for variables
sel_feature = names(mergedata2)
colnames(finaldata) <- c("subject","activity", sel_feature)

#output the tidy_data
write.table(finaldata, file="./tidy_data_set.txt", row.names=FALSE)
