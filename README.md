#Load some useful libraries
library(data.table)
library(dplyr)
library(tidyr)

#Create a Directory for the Project
#if(!file.exists("./Getting and Cleaning Data/Project")){dir.create("./Getting and Cleaning Data/Project")}
#fileUrl1 = "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
#Download the zip file
#download.file(fileUrl1,destfile="./Getting and Cleaning Data/Project/project.zip")
#Unzip it
#unzip("./Getting and Cleaning Data/Project/project.zip",exdir="./Getting and Cleaning Data/Project")
#List the files
files<-list.files("./Getting and Cleaning Data/Project",full.names=TRUE)
files<-list.files(files[4], full.names=TRUE)

#Extract required files and read into environment.  Trim the features file to one column.
testfolder<-files[5]
trainfolder<-files[6]
act<-fread(files[1],header=FALSE)
act<-tbl_df(act)
features<-fread(files[2],header=FALSE)
features<-features$V2

#Create a vector of columns containing "mean" or std" and remove others from "features"
features1<-grep("[mM][eE][aA][nN]\\()-X|[mM][eE][aA][nN]\\()-Y|[mM][eE][aA][nN]\\()-Z|[sS][tT][dD]\\()-X|[sS][tT][dD]\\()-Y|[sS][tT][dD]\\()-Z",features)
features<-features[features1]

#Reading training files and keeping only relevant variables
trainfiles<-list.files(trainfolder,full.names=TRUE)
subtrain<-fread(trainfiles[2],header=FALSE)
xtrain<-read.table(trainfiles[3],header=FALSE)
xtrain<-select(xtrain,features1)
ytrain<-read.table(trainfiles[4],header=FALSE)

#Reading test files and keeping only relevant variables
testfiles<-list.files(testfolder,full.names=TRUE)
subtest<-read.table(testfiles[2],header=FALSE)
xtest<-read.table(testfiles[3],header=FALSE)
xtest<-select(xtest,features1)
ytest<-read.table(testfiles[4],header=FALSE)

#Editing the variable names
features<-gsub("\\-","",features)
features<-gsub("\\)","",features)
features<-gsub("\\(","",features)
features<-tolower(features)

#Arrange the Dataset and name the variables
fit<-rbind(xtrain,xtest)
setnames(fit,names(fit),features)
fitsub<-rbind(subtrain,subtest)
setnames(fitsub,"V1","subject")
fity<-rbind(ytrain,ytest)

#Rename Values in ytrain and ytest, i.e. fity from numbers to names.
fity[,1]<-gsub("\\\n","",fity[,1])
fity[,1]<-gsub("1","walking",fity[,1])
fity[,1]<-gsub("2","walking_upstairs",fity[,1])
fity[,1]<-gsub("3","walking_downstairs",fity[,1])
fity[,1]<-gsub("4","sitting",fity[,1])
fity[,1]<-gsub("5","standing",fity[,1])
fity[,1]<-gsub("6","laying",fity[,1])

#label fity variable
setnames(fity,"V1","action")

#Combine dataframes
fitall<-cbind(fitsub,fity,fit)
fitall<-tbl_df(fitall)

#Group and summarise the data capturing the mean of each activity for each subject.
fitall<-group_by(fitall,subject,action)
summarise_each(fitall,funs(mean))
