Mean-Data
=========

Script analyses a set of data files containing geospacial measurements associated to different activities (e.g. walking, walking upstairs, running)

#######            RUN ANALYSIS ROUTINE     ######### 

### The following code extracts and cleans data from a set of files on the
### "getdata-projectfiles-UCI HAR Dataset.zip" subdirectory of the working
### directory. The result is a tidy dataframe with information about mean
### metrics.......

##   Unzip data source
        dir <- getwd()
        unzip("getdata-projectfiles-UCI HAR Dataset.zip",exdir=dir)

##   Extract activity levels
        filePath <-"UCI HAR Dataset/activity_labels.txt"
        activityLabels <-read.table(filePath)
        colnames(activityLabels)<-c("factor","activity")
        activityLabels$activity<-tolower(gsub("_","",activityLabels$activity))

##   Extract features vector
        filePath <-"UCI HAR Dataset/features.txt"
        features <-read.table(filePath)
        colnames(features)<-c("factor","feature")

##   Extract and clean test data

##    (1) read subjects into a vector subjectTest
        filePath <-"UCI HAR Dataset/test/subject_test.txt"
        subjectTest <-read.table(filePath)
        colnames(subjectTest)<-"subject"

##    (2) read features measurements "X_test" into xTest data frame
        filePath <-"UCI HAR Dataset/test/X_test.txt"
        xTest <- read.table(filePath)
        colnames(xTest)<-features$feature

##    (3) read activity "y_Test" into yTest data frame
        filePath <-"UCI HAR Dataset/test/y_test.txt"
        yTest <- read.table(filePath)
        colnames(yTest)<-"activity"

##    (4) add subject and activity column to data frame
        xTest$subject<- NA
        xTest$subject<- unlist(subjectTest)
        xTest$activity<- NA
        xTest$activity<- unlist(yTest)

##   Extract and clean train data

##    (1) read subjects into a vector subjecttrain
        filePath <-"UCI HAR Dataset/train/subject_train.txt"
        subjectTrain <-read.table(filePath)
        colnames(subjectTrain)<-"subject"

##    (2) read features measurements "X_train" into xtrain data frame
        filePath <-"UCI HAR Dataset/train/X_train.txt"
        xTrain <- read.table(filePath)
        colnames(xTrain)<-features$feature

##    (3) read activity "y_train" into ytrain data frame
        filePath <-"UCI HAR Dataset/train/y_train.txt"
        yTrain <- read.table(filePath)
        colnames(yTrain)<-"activity"

##    (4) add subject and activity column to data frame
        xTrain$subject<- NA
        xTrain$subject<- unlist(subjectTrain)
        xTrain$activity<- NA
        xTrain$activity<- unlist(yTrain)

###    Merge test and train datasets by subject

        completeData <- rbind(xTrain,xTest)

####   Apply descriptive activity names (e.g.Walk, Run)

        completeData$activity <-factor(completeData$activity,levels=activityLabels$factor,labels = activityLabels$activity)

###     Create a new tidy data frame with the average of each variable for each
###     activity and each subject and save it into a text file named meanData.txt
###     on the working directory

        meanColumns<- c(562,563,grep("Mean)",names(completeData)),grep("mean()",names(completeData)))
        meanData <- completeData[,meanColumns]
        write.table(meanData, file="meanData.txt")
