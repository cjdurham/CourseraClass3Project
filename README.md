# All import analysis done by the code below.  Please see notes above each section. note, code is designed for a local directory on my laptop.  It will require editing to your local location.


# Install Readr for use with fixed width files
library(readr)

# Set Working Directory for Test Data
setwd("/Users/cjdurham/Dropbox/Coursera/3. Getting Clean Data/Project/Data/test")

# Specify width
widthstring<-replicate(561,as.numeric("16"))

# Import Files from the folder with .txt ending
filelist <- list.files(pattern="*.txt")
for (i in 1:length(filelist)) 
{
        assign(filelist[i],
        read.fwf(file=filelist[i],width=widthstring))
}
# Clean up subject test from extra columns
subject_test.txt<-subject_test.txt[,1]
y_test.txt<-y_test.txt[,1]



# Set Working Directory for train data
setwd("/Users/cjdurham/Dropbox/Coursera/3. Getting Clean Data/Project/Data/train")

# Import Files from the folder with .txt ending
filelist <- list.files(pattern="*.txt")
for (i in 1:length(filelist)) 
{
        assign(filelist[i],
               read.fwf(file=filelist[i],width=widthstring))
}

# Clean up subject train from extra columns
subject_train.txt<-subject_train.txt[,1]
y_train.txt<-y_train.txt[,1]

# Cleanup Unneeded Data from the upload
rm(i)
rm(filelist)
rm(widthstring)

# Set Working Directory for train data
setwd("/Users/cjdurham/Dropbox/Coursera/3. Getting Clean Data/Project/Data/")

# Read in list for column names
features<-read.table("features.txt")

# Update Column Headers in X Train/Test files.
colnames(X_test.txt) <- features[,2]
colnames(X_train.txt) <- features[,2]


# Combine activities and subjects
subjectactivitytest<-cbind(subject_test.txt,y_test.txt)
subjectactivitytrain<-cbind(subject_train.txt,y_train.txt)
subjectactivity<-rbind(subjectactivitytest,subjectactivitytrain)
colnames(subjectactivity)<-c("subject","activity")

# Combine the X datasets
combo<-rbind(X_test.txt,X_train.txt)

# Combine Both
combo<-cbind(combo,subjectactivity)


# Look for match of mean / Mean / std
output<-combo[,colnames(combo)[grep("Mean",colnames(combo))]]
output<-cbind(output,combo[,colnames(combo)[grep("mean",colnames(combo))]])
output<-cbind(output,combo[,colnames(combo)[grep("std",colnames(combo))]])
output<-cbind(output,combo$subject)
output<-cbind(output,combo$activity)


# Rename Columns
colnames(output)[87]<-"subject"
colnames(output)[88]<-"activity"

# Grouping
result<-aggregate(output, by = list(output$subject,output$activity),mean) 
colnames(result)[1]<-"subject"
colnames(result)[2]<-"activity"
result<-result[,-89]
result<-result[,-89]

# Clean up
rm(list=ls(pattern=".txt")) 
rm(output,subjectgroup,combo,features,subjectactivity)

# Write File
setwd("/Users/cjdurham/Dropbox/Coursera/3. Getting Clean Data/Project")
write.csv(result,"Tidy.Data.CSV")
